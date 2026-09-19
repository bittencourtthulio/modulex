# Publicação — o catálogo em nuvem é o GitHub, e nada além dele

O catálogo compartilhado (D1) precisa viver em algum lugar alcançável por
todos os projetos. A resposta é o GitHub, sem servidor, sem banco e sem
serviço próprio (D16).

Não é economia: em três pontos o primitivo nativo é melhor que a
alternativa construída.

| Peça | Sem GitHub | No GitHub |
|---|---|---|
| fonte da verdade | git + markdown | o repositório do catálogo |
| índice da M0 | banco | `modulos.json` por `raw.githubusercontent.com`, com CDN na frente |
| busca | banco vetorial | lexical sobre a seção 2; vetor **commitado** se um dia precisar |
| gate | serviço próprio | Actions no PR + secret scanning e push protection nativos |
| aprovação humana | fluxo inventado | **o PR já é a aprovação** |
| lacunas globais | tabela | **issues com label `lacuna`**, e 👍 como voto |
| vencimento | cron em servidor | Actions `schedule`, abrindo issue de M3 |

## O PR é o gate

O que seria um obstáculo inventado — "aprovação humana antes do primeiro
upload" — é, no GitHub, o caminho de menor resistência. Por isso ninguém
pula.

E o CI do PR roda o contrato inteiro:

| Confere | Script |
|---|---|
| segredo, dado pessoal, domínio de cliente, caminho absoluto | `scripts/gate_publicacao.py` |
| 14 seções, frontmatter, `não cobre` não-vazio, `NAO DETERMINADO` ↔ seção 13 | `scripts/validar_modulo.py` |
| o índice bate com os `MODULO.md` | `scripts/reindexar.py --conferir` |
| a M0 ainda responde nos dois desfechos | `scripts/buscar.py` |

O contrato deixa de ser norma escrita e passa a ser **teste que reprova
merge**. É a diferença entre uma regra que todo mundo concorda e uma regra
que acontece.

## A ordem importa: gate local antes de push

O gate roda **duas vezes**, e a primeira é na máquina de quem publica.

Push protection do GitHub bloqueia segredo no remoto — mas a essa altura o
dado já saiu da máquina, já passou por um proxy, já está num log em algum
lugar. Rodar só no CI é fechar a porta depois.

`scripts/publicar.py` executa nesta ordem, e para no primeiro erro:

```
1. gate de publicação      local, antes de qualquer coisa
2. validação do contrato
3. espelho + reindexação
4. branch, commit, PR
```

Sem `--confirmar`, nada é enviado. Publicação é irreversível: repositório
público é forkado, indexado e cacheado. O que vazou não volta com
force-push.

## Público é um eixo separado de GitHub

Dá para ter catálogo no GitHub e privado. Público é decisão de produto, e
ela tem consequência técnica direta: **a extração schema-based deixa de ser
refinamento e passa a ser a única linha de defesa** (D19).

Em catálogo público:

- nenhum artefato bruto sobe por padrão — o que viaja é o `MODULO.md`;
- a coluna **herdado** da seção 6 vira marcador, não literal
  (`company_id` → `{{coluna_tenancy}}`);
- código de cliente costuma estar sob contrato: o **opt-in é por projeto**,
  o escopo padrão é privado, e o que subiu fica registrado.

Se isso for automático por padrão, um cliente descobre um dia que a lógica
dele está num catálogo comum — e aí não importa quão bom era o gate.

## Dois catálogos, uma cadeia

O modelo real é um catálogo público curado mais um privado por organização.
A cadeia de resolução (`references/05-catalogo.md`) ganha a fusão por
namespace: a chave global é `<namespace>/<id>`, e em colisão o **privado
vence o público** (D18).

Isso preserva o motivo da regra original — dois `modulos.json` com o mesmo
`id` e conteúdos diferentes é a ambiguidade que a skill existe para não
criar. Com namespace, não há colisão silenciosa: há precedência declarada.

## A issue de lacuna

Busca que termina em `NAO EXISTE` vira issue no catálogo, com label
`lacuna`. Quem também procurou soma 👍.

Em alguns meses, a fila de extração está priorizada por demanda real, e é
pública. O `LACUNAS.md` local continua valendo — ele é cego, só enxerga o
que este projeto procurou; a issue é o que atravessa projetos.

**Catálogo inalcançável não abre issue.** "Não há catálogo" e "há catálogo
e ele não cobre isto" são respostas diferentes (D15): a segunda registra
lacuna, a primeira não. Confundi-las polui o arquivo que decide a próxima
extração com buscas que nunca foram feitas.

## O que se perde, e vale dizer

**Telemetria de leitura agregada.** O raw do GitHub não conta consulta, e o
indicador do modulex tem duas metades — leitura e escrita. A escrita se
mede (PRs de módulo); a leitura, não.

O que sobra: contagem local por organização, e as issues de lacuna como
proxy da leitura que não achou. Isso distingue bem o `zero/alta`, e mal o
`alta/zero` do `zero/zero`. Se um dia incomodar, o menor acréscimo possível
é um endpoint burro — não um banco.

**Cache do CDN.** Módulo recém-mergeado demora alguns minutos para aparecer
no raw. Irrelevante na prática, e vale saber antes de alguém achar que o
merge falhou.

## A rede acontece num lugar só

`scripts/sincronizar.py` baixa o catálogo para o degrau 2 e é o **único**
ponto que toca a rede. A M0 continua lendo arquivo em disco, offline, como
a D15 exigiu.

Sincronização falhando nunca bloqueia: a cópia anterior continua valendo, e
catálogo velho é problema da M3, não da M0 (regra 11).
