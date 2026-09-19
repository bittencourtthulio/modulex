---
description: Publica um MODULO.md no catalogo via PR, com o gate de segredo rodando na maquina antes de qualquer push.
argument-hint: [caminho/MODULO.md]
---

Use a skill `modulex`, seguindo `references/07-publicacao.md` e o contrato em `references/04-contrato.md`.

O módulo a publicar está em `$ARGUMENTS`. Se vier vazio, pergunte qual `MODULO.md` deve subir e pare.

## Antes de tudo: isto é irreversível

Repositório público é forkado, indexado e cacheado. O que vazou **não volta** com force-push.

Por isso a ordem abaixo não se negocia, e ela para no primeiro erro.

## Roteiro

1. **Gate de publicação, local, antes de qualquer push.**

   ```bash
   python3 scripts/gate_publicacao.py <caminho/MODULO.md>
   ```

   Acusou segredo? **Pare e gire o segredo.** Push protection do GitHub bloqueia no remoto, mas a essa altura o dado já saiu da máquina, já passou por proxy e já está em log. Não contorne, não force, não "publique só essa parte".

2. **Validação do contrato.**

   ```bash
   python3 scripts/validar_modulo.py <caminho/MODULO.md>
   ```

   As 14 seções, o frontmatter no schema, o `não cobre` preenchido em toda fatia, as duas colunas da seção 6, todo `NAO DETERMINADO` com contrapartida na seção 13, e o `status` coerente com as seções abertas.

3. **Confirme o `status`.** `ativo` com seção de julgamento em aberto reprova — e está certo que reprove. Se ainda falta julgamento, é `candidato`: ele sobe buscável e marcado, e **não** é injetável na F3 nem na F6 (regra 13).

4. **Confirme o `namespace`.** `publico` para o catálogo compartilhado; o slug da organização para o catálogo privado. A chave global é `<namespace>/<id>`.

5. **Espelho, reindexação e PR.**

   ```bash
   python3 scripts/publicar.py <caminho/MODULO.md> --catalogo <pasta-do-catalogo> --confirmar
   ```

   Sem `--confirmar` nada é enviado — rode assim primeiro e leia o que ele faria.

6. **O PR é o gate humano.** O CI confere contrato, gate e índice. O que o PR pede a uma pessoa é o que nenhum script decide: **se as 14 seções dizem a verdade, e se o "não cobre" está honesto.**

## Regras

- **O que sobe é extraído, nunca raspado** (regra 12). Se você está tentado a colar um arquivo do projeto de origem inteiro, pare: a coluna *herdado* da seção 6 é exatamente o que não deve viajar literal. `company_id` vira `{{coluna_tenancy}}`.
- **Artefato bruto não sobe por padrão.** O que viaja é o `MODULO.md`; o código fica no repositório do módulo, apontado pelo campo `repo`.
- **Catálogo público é decisão de produto, não default técnico.** Código de cliente costuma estar sob contrato. Na dúvida, publique no catálogo privado da organização e diga isso ao humano.
- Nenhum caminho absoluto, em lugar nenhum.

## Saída

```
Modulo publicado: <namespace>/<id>
Status: <ativo | candidato>
Gate: <n> erro(s), <n> aviso(s)
Contrato: 14 secoes · NAO DETERMINADO em <quais>
PR: <url>
```

Se o gate barrou, reporte **o que ele achou e onde**, e não siga adiante. Segredo encontrado antes do push é a melhor notícia possível do dia.
