# Onde vive o catálogo e como encontrá-lo

O catálogo é **compartilhado entre projetos** (D1): módulo é ativo do
ecossistema, não da casa. Isso cria um problema que nenhuma outra skill do
método tem — o `memox`, o `stackx` e o `prodx` escrevem dentro do projeto em
que rodam; o `modulex` lê de fora dele.

Este arquivo responde a pergunta que decorre disso: **estando dentro de um
projeto qualquer, onde está o `modulos.json`?**

## A cadeia de resolução

Quatro degraus, do mais explícito ao mais genérico. **Pare no primeiro que
existir** e diga qual foi.

| # | Onde | Quando é esse |
|---|------|---------------|
| 1 | `$MODULEX_CATALOGO/modulos.json` | a variável de ambiente está definida — override explícito de quem sabe o que está fazendo |
| 2 | `.expx/modulex/docs/modulos/modulos.json` | o catálogo foi instalado no projeto pelo `npx expxdev init`, ao lado do marketplace das skills |
| 3 | `docs/modulos/modulos.json` | você está **dentro do próprio repositório do catálogo** |
| 4 | nenhum dos três | não há catálogo alcançável daqui |

Regras da cadeia:

- **Nenhum caminho absoluto.** O degrau 1 é uma variável que o usuário define;
  os degraus 2 e 3 são relativos à raiz do projeto. Caminho absoluto em
  artefato ou em saída é proibido em todo o método.
- **Sem rede.** Nenhum degrau busca nada no GitHub. A consulta é offline, como
  o README promete, e o catálogo desatualizado no disco é problema da M3, não
  da M0.
- **O primeiro que existir vence, e os outros não são consultados** — com uma
  exceção declarada, abaixo. O que a regra nunca permite é colisão
  **silenciosa**: dois `modulos.json` com o mesmo `id` e conteúdos diferentes é
  exatamente a ambiguidade que a skill existe para não criar.

## A exceção: fusão por namespace

O modelo real é um catálogo público curado **mais** um privado por
organização, e quem tem os dois quer os dois. A fusão é permitida quando — e
só quando — a ambiguidade é impossível: a chave global é `<namespace>/<id>`,
e a `precedencia` do `modulos.json` declara quem vence em colisão. O padrão é
`["privado", "publico"]` (D18).

Sem namespace declarado, a regra antiga vale inteira: o primeiro degrau vence
e os outros não são consultados.

## O degrau 2 é onde a rede aterrissa

A cadeia continua **sem rede**, e isso não mudou com o catálogo em nuvem. A
sincronização é operação à parte:

```
scripts/sincronizar.py   →   .expx/modulex/docs/modulos/   →   degrau 2
```

Ela baixa `modulos.json`, `INDICE.md` e `LACUNAS.md` do GitHub com ETag —
rodar de novo sem mudança no remoto não baixa nada — e a M0 volta a ler
arquivo em disco, offline, como sempre leu.

Sincronização falhando **nunca bloqueia**: a cópia anterior continua valendo,
e catálogo velho é problema da M3, não da M0 (regra 11).

## O quarto degrau nunca bloqueia

Catálogo inacessível é **ausência de modulex**, e a regra 11 vale
integralmente: a ausência do modulex nunca bloqueia nenhuma outra skill.

Quando nenhum degrau resolve, a resposta é esta, e é curta:

```
modulex: catalogo nao alcancavel a partir deste projeto.
Procurei, nesta ordem:
  1. $MODULEX_CATALOGO        (variavel nao definida)
  2. .expx/modulex/docs/modulos/   (nao existe)
  3. docs/modulos/                 (nao existe)

Seguindo sem modulo. O sprintx planeja do zero, como sempre planejou.
Para ligar o catalogo: defina MODULEX_CATALOGO ou rode `npx expxdev init`.
```

O que **não** fazer nesse caso:

- não criar `docs/modulos/` vazio no projeto do cliente — catálogo local
  nasce vazio em todo projeto novo e quebra o mecanismo central da skill (D1);
- não clonar o repositório do catálogo por conta própria;
- não responder `NAO EXISTE`. "Não há catálogo" e "há catálogo e ele não tem
  isto" são respostas diferentes, e confundi-las faz o P3 do prodx registrar
  uma lacuna que não existe.

## Declarar o degrau é obrigatório

Toda saída da M0 diz de onde leu. Uma linha, no fim:

```
Catalogo: .expx/modulex/docs/modulos/ (degrau 2) · 1 modulo · atualizado_em 2026-09-02
```

`scripts/buscar.py` imprime essa linha sozinho, em toda consulta.

Por quê: o defeito mais provável desta skill, depois de instalada, é alguém
consultar um catálogo velho sem saber. O degrau mais a data de atualização
custam uma linha e tornam esse defeito visível.

## O que cada estágio alcança

A cadeia resolve a pasta do catálogo. O que cada estágio pode abrir dentro
dela — e fora dela — não muda:

| Estágio | Lê | Não toca |
|---------|-----|---------|
| M0 consulta | `modulos.json`, e o `LACUNAS.md` no desfecho `NAO EXISTE` | `MODULO.md`, repositório de módulo, artefato |
| M1 injeção | o `MODULO.md` do módulo escolhido, pelo campo `repo` da entrada | os artefatos — só descem na F6, task a task |
| M2 extração | os artefatos do trabalho entregue; **escreve** no catálogo | — |
| M3 verificação | o `MODULO.md` do módulo e a documentação atual do fornecedor | — |

A M2 e a M3 **escrevem** no catálogo. Escrever exige que o degrau resolvido
seja gravável: se o catálogo veio do degrau 2 e é uma cópia instalada, a
escrita vai para o repositório do catálogo, não para a cópia. Diga isso ao
usuário em vez de gravar numa cópia que o próximo `expxdev init` sobrescreve.

## Casos de borda

| Situação | O que fazer |
|----------|-------------|
| `MODULEX_CATALOGO` definida, mas o caminho não existe | Reporte que a variável aponta para o nada e **siga para o degrau 2**. Variável errada é mais comum que variável certa |
| Dois degraus existem com conteúdos diferentes | O primeiro vence. Reporte em uma linha que há outro, e qual — pode ser cópia velha |
| O degrau resolveu, mas o `modulos.json` está corrompido ou não é `expx-schema-v1` | Reporte o defeito e trate como catálogo ausente. Não tente reparar na consulta: reparar é M2/M3 |
| O catálogo existe e tem zero módulos | Não é o quarto degrau. Responda "catálogo existe e está vazio" e ofereça a M2 — é o caso do catálogo recém-criado |
| A M1 resolveu o catálogo mas o `repo` do módulo está inalcançável | Injete o que o `modulos.json` já traz (problema, fatias, cobre/não cobre, stacks, pré-requisitos) e declare que o `MODULO.md` não desceu. Injeção parcial declarada é útil; injeção silenciosamente incompleta não |
