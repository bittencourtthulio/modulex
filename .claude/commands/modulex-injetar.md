---
description: M1 do modulex — carrega um modulo (ou so as fatias escolhidas) na base de conhecimento do trabalho em andamento, com a autoridade certa em cada bloco.
argument-hint: [id-do-modulo] [fatias]
---

Use a skill `modulex`, estágio **M1**, seguindo `references/01-injecao.md`.

O módulo e as fatias estão em `$ARGUMENTS`. Se vier vazio, rode `/modulex-buscar` primeiro — injetar sem consultar é injetar às cegas.

## Antes de injetar

1. **Identifique o destino**: F1 do sprintx, E1 do runx, ou F5 (auditoria com módulo descoberto depois). O destino muda o que desce.
2. **Confirme as fatias.** Sem indicação, **pergunte** — não injete o módulo inteiro por padrão. Injetar as seis sprints do módulo zero num projeto que só quer receber e responder mensagem enche a base de IA, ligações e grupos que ninguém pediu, e a F3 planeja o que foi injetado.

## O que desce

| Desce | Não desce |
|-------|-----------|
| `MODULO.md`, fatias escolhidas | os artefatos de código |
| o inventário de artefatos (a lista) | os arquivos do inventário |

Artefato só é copiado na F6, sob TDD, quando a task chegar.

## Por destino

**F1 do sprintx** — o `MODULO.md` inteiro das fatias escolhidas, com cada bloco marcado pela autoridade que terá adiante:

| Seção | Vai para | Como |
|-------|----------|------|
| 5 decisões fechadas | F2 | **pauta de entrevista**, nunca resposta |
| 9 plano de fases | F3 | **rascunho** a adaptar |
| 6 dependência de stack | F3 + stackx | o stackx local vence |
| 11 cadeia de falha + convenções | F5 | itens de auditoria |
| 10 inventário | F6 | copiar preferencialmente a reescrever, sob TDD |
| 4 o que NÃO cobre | F2 e F3 | fronteira do escopo |

**E1 do runx** — **apenas** as seções 11, 12 e (se o sintoma for de configuração) a 6. O plano não desce: a E1 investiga, não constrói. Rótulo obrigatório:

```
Hipoteses do modulo <id> — NAO SAO CAUSA COMPROVADA.
A E1 exige prova. Isto e a ordem em que vale olhar.
```

**F5 do sprintx** — seções 5, 11 e as convenções invioláveis, como checklist de prontidão.

## Stack, sempre em duas colunas

```
Stack essencial (viaja):
  <item> — <por que o terceiro exige>

Stack herdada (traduzir pelo stackx do destino):
  <item do modulo> → <equivalente neste projeto, ou PENDENTE>
```

Com `docs/stack/CONVENCOES.md`: preencha a direita e marque o que conflita.
Sem ele: `PENDENTE`, e diga que o stackx não rodou. **Nunca preencha por analogia** — inventar o equivalente reintroduz a convenção alheia com outro nome.

Conflito na coluna **essencial** é reportado e para (regra 3):

```
CONFLITO — modulo <id>
  Modulo (essencial): <convencao>
  Projeto (CONVENCOES.md:<linha>): <convencao>
  Nao ha traducao. Isto e decisao humana, na F2 do sprintx.
```

## Regras

- Cada bloco entregue leva **rótulo de autoridade** e **procedência** (`<id>, verificado em <data>`).
- Nenhum artefato de código é copiado aqui.
- Módulo vencido: o aviso é repetido em **cada bloco**, não só na consulta.
- Seção obrigatória faltando no `MODULO.md`: injete o que existe e reporte a falta como lacuna do módulo. Não preencha por inferência.
- Dois módulos escolhidos: injete os dois como alternativas, **sem fundir os planos**.
