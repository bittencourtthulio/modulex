# AGENTS.md

Repositório da skill **modulex** — a camada de conhecimento de módulos do
ecossistema Expx.

## O que é

O modulex é o lugar onde o conhecimento de integração volta, em vez de ser
redescoberto a cada projeto. Ele não planeja, não implementa e não decide:
carrega conhecimento pronto sobre problemas que já foram resolvidos antes.

Ele responde a pergunta que as skills irmãs não fazem: **alguém já resolveu
isso antes?**

## Estrutura deste repositório

```
.claude/skills/modulex/   a skill (SKILL.md, references, assets)
.claude/commands/         os sete comandos, para Claude Code
.opencode/commands/       os mesmos sete, conteudo identico, para OpenCode
.github/assets/           os SVGs estaticos: badges, banner e os dois
                          diagramas claro/escuro que o README nao usa mais
.github/assets/anim/      as doze animacoes que o README usa hoje
docs/integracao/          prompts de patch: prodx, sprintx, runx, stackx, memox, buildx
docs/modulos/             o catalogo: INDICE.md, modulos.json, LACUNAS.md
exemplos/                 o MODULO.md do modulo zero, whatsapp-uazapi
```

A skill vive **apenas** em `.claude/skills/`, que os dois harnesses leem
nativamente. Não crie `.opencode/skills/`: nomes de skill precisam ser únicos
entre todas as localizações.

## Ao trabalhar neste repositório

- **Comandos duplicados devem permanecer idênticos.** Ao alterar um arquivo
  em `.claude/commands/`, copie-o para `.opencode/commands/` e confirme com
  `diff -r` entre as duas pastas.
- **Sem caminho absoluto** em qualquer arquivo. O catálogo se endereça pela
  cadeia de quatro degraus de `references/05-catalogo.md` — nunca por caminho
  fixo. Ao alterar a cadeia, altere nos seis lugares que a repetem: o
  reference, o `SKILL.md`, os comandos `/modulex` e `/modulex-buscar`, a nota
  comum dos patches em `docs/integracao/`, e o `README.md`.
- **Sem prefixo `expx`** em nomes de arquivo, comando ou skill.
- **Frontmatter no padrão expx-schema v1**: chaves em `snake_case` sem acento,
  enums minúsculos sem acento, datas em ISO, chave nunca omitida.
- **Ao alterar as regras invioláveis**, altere no `SKILL.md` (fonte da
  verdade), e verifique se os references, os templates, os patches e o README
  continuam coerentes.
- **Nenhum SVG leva `<script>`, `<foreignObject>` nem fonte externa.** Nos
  badges e no banner de `.github/assets/`, também não há animação nem
  `prefers-color-scheme`.

- **As animações vivem só em `.github/assets/anim/`, e são arquivo único.**
  Elas animam por SMIL (`<animate>`, `<animateMotion>`) e por `@keyframes`
  num `<style>` interno — nada mais. Não existe par claro/escuro nem
  `<picture>` para elas: cada animação desenha o próprio cartão sobre fundo
  `#0d1117`, e por isso lê igual nos dois temas do GitHub. A paleta é a do
  GitHub escuro, com o azul `#1f6feb` / `#58a6ff` como acento do modulex.

  O README referencia as animações por **caminho relativo**
  (`.github/assets/anim/<nome>.svg`) e os badges por URL `raw`. Ao mexer numa
  animação, confirme que o XML continua válido e que nenhum texto vaza do
  cartão:

  ```bash
  python3 -c "import xml.dom.minidom,glob;[xml.dom.minidom.parse(f) for f in glob.glob('.github/assets/anim/*.svg')]"
  ```

  Um `class` duplicado no mesmo elemento quebra o parse e passa despercebido
  no editor — foi o defeito mais frequente ao construir as doze.
- **Ao alterar o contrato do `MODULO.md`**, altere em **quatro** lugares e
  confirme que continuam coerentes: `references/04-contrato.md` (a norma),
  `assets/TEMPLATE-MODULO.md` (a forma), `exemplos/MODULO.whatsapp-uazapi.md`
  (o caso que valida) e `scripts/validar_modulo.py` (o teste). Um contrato que
  o módulo zero não consegue preencher é um contrato mal desenhado — e isso vai
  para `DECISOES-DA-SKILL.md`, não para o template. Um contrato que o validador
  não cobra é um contrato que não acontece.

  Confirme com:

  ```bash
  python3 scripts/validar_modulo.py --todos
  python3 scripts/reindexar.py --conferir
  ```

- **Os scripts usam só a biblioteca padrão do Python.** Eles rodam em GitHub
  Actions e na máquina de quem extrai; `pip install` na esteira é custo que
  ninguém paga duas vezes. Saída de script sem acento, como as saídas do
  catálogo; prosa em Markdown com acento.

- **Ao mexer no gate ou no detector, teste contra isca e contra o próprio
  repositório.** Gate que nunca acusa nada está quebrado, e detector que acusa
  documentação enche a fila de candidato que ninguém promove. As duas falhas
  já aconteceram durante a construção e estão registradas em
  `references/06-esteira.md`. O teste de regressão do detector roda sobre um
  intervalo só de documentação; sobre `scripts/` ele dispara de propósito,
  porque o código do gate contém os padrões que procura.

## As 13 regras invioláveis

Estão no `SKILL.md`, uma por linha. As três que mais restringem o
comportamento do agente:

1. **O modulex não escolhe fornecedor.** Havendo dois módulos para o mesmo
   problema, ele lista os dois e cala. A escolha é do sprintx, com o stackx.
2. **Convenção herdada do sistema de origem nunca sobrepõe o stackx do
   projeto de destino.** Conflito é reportado ao humano, nunca resolvido pelo
   modulex.
3. **Faixa de esforço sem observação real é `NAO DETERMINADO`.** O agente
   nunca estima, nunca infere por analogia, nunca usa sensação — o P4 do prodx
   encolhe escopo com base nesse número. Vale também para número vindo de
   máquina: a janela do git é calendário, não esforço, e só vira seção 8 com
   confirmação humana.

E as duas que governam a publicação:

4. **O que sobe é extraído, nunca raspado, e nada sobe sem aprovação humana
   no PR.** Anonimizar falha aberto; extrair falha fechado. O gate roda na
   máquina **antes** do push — push protection no remoto já é tarde.
5. **Candidato não é módulo.** Seção de julgamento em aberto significa
   buscável e marcado, nunca rascunho de sprint na F3 nem artefato copiado na
   F6.

## O que nunca fazer neste repositório

- **Preencher campo de `MODULO.md` por inferência.** Campo não verificável é
  `NAO DETERMINADO` e vai para as lacunas. Módulo com campo inventado é pior
  que módulo inexistente.
- **Fazer a consulta (M0) ler algo além do `modulos.json`.** A operação mais
  frequente da skill tem que ser barata; se ela encarecer, a skill morre.
- **Gravar segredo, domínio de cliente ou telefone real num `MODULO.md`.**
  Módulo é conhecimento compartilhado entre projetos: o que entra aqui vaza
  para todos os destinos. Em catálogo público, vaza para sempre — repositório
  público é forkado, indexado e cacheado, e não volta com force-push.

- **Publicar a fila de candidatos.** `.expx/modulex/fila/` é local de
  propósito: ela carrega nome de branch, caminho e host que identificam o
  cliente. É evidência para quem vai extrair, não conteúdo de catálogo.

- **Fazer o hook extrair módulo.** Ele enfileira candidato e mais nada. Script
  sem humano preenche os catorze campos por inferência, e módulo plausível é
  mais perigoso que módulo ausente, porque ninguém desconfia dele.

- **Fazer o script falhar o trabalho de quem está entregando.** O detector sai
  com código 0 mesmo quando quebra, e fala só quando tem o que dizer. Regra 11
  vale para a esteira também.

## Para usar a skill

Veja o `README.md`. Os comandos são `/modulex`, `/modulex-buscar`,
`/modulex-injetar`, `/modulex-extrair`, `/modulex-verificar`,
`/modulex-publicar` e `/modulex-sincronizar`.
