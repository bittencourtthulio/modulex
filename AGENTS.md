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
.claude/commands/         os cinco comandos, para Claude Code
.opencode/commands/       os mesmos cinco, conteudo identico, para OpenCode
.github/assets/           os SVGs do README: banner, badges e os dois diagramas
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
- **Os SVGs não levam `<script>`, `<foreignObject>`, animação, fonte externa
  nem `prefers-color-scheme`.** A troca de tema é feita no README, com dois
  arquivos e o elemento `<picture>`.
- **Ao alterar o contrato do `MODULO.md`**, altere em três lugares e confirme
  que continuam coerentes: `references/04-contrato.md` (a norma),
  `assets/TEMPLATE-MODULO.md` (a forma) e
  `exemplos/MODULO.whatsapp-uazapi.md` (o caso que valida). Um contrato que o
  módulo zero não consegue preencher é um contrato mal desenhado — e isso vai
  para `DECISOES-DA-SKILL.md`, não para o template.

## As 11 regras invioláveis

Estão no `SKILL.md`, uma por linha. As três que mais restringem o
comportamento do agente:

1. **O modulex não escolhe fornecedor.** Havendo dois módulos para o mesmo
   problema, ele lista os dois e cala. A escolha é do sprintx, com o stackx.
2. **Convenção herdada do sistema de origem nunca sobrepõe o stackx do
   projeto de destino.** Conflito é reportado ao humano, nunca resolvido pelo
   modulex.
3. **Faixa de esforço sem observação real é `NAO DETERMINADO`.** O agente
   nunca estima, nunca infere por analogia, nunca usa sensação — o P4 do prodx
   encolhe escopo com base nesse número.

## O que nunca fazer neste repositório

- **Preencher campo de `MODULO.md` por inferência.** Campo não verificável é
  `NAO DETERMINADO` e vai para as lacunas. Módulo com campo inventado é pior
  que módulo inexistente.
- **Fazer a consulta (M0) ler algo além do `modulos.json`.** A operação mais
  frequente da skill tem que ser barata; se ela encarecer, a skill morre.
- **Gravar segredo, domínio de cliente ou telefone real num `MODULO.md`.**
  Módulo é conhecimento compartilhado entre projetos: o que entra aqui vaza
  para todos os destinos.

## Para usar a skill

Veja o `README.md`. Os comandos são `/modulex`, `/modulex-buscar`,
`/modulex-injetar`, `/modulex-extrair` e `/modulex-verificar`.
