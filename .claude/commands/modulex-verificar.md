---
description: M3 do modulex — revalida um modulo contra a realidade atual da API do fornecedor, corrige o que divergiu e avisa quem planejou com a informacao antiga.
argument-hint: [id-do-modulo]
---

Use a skill `modulex`, estágio **M3**, seguindo `references/03-verificacao.md`.

O módulo está em `$ARGUMENTS`. Se vier vazio, liste os módulos com verificação vencida e pergunte qual.

## Por que isto importa

Módulo desatualizado é **pior que módulo nenhum**. Sem módulo, o sprintx lê a documentação atual e acerta. Com módulo vencido, ele produz um plano confiante e errado — e o erro só aparece na execução, depois de o P4 já ter encolhido o escopo e o cliente já ter recebido um prazo.

## O que revalidar, nesta ordem

Verificação **não é reler o `MODULO.md`**. É confrontá-lo com a realidade externa.

| # | Campo | Contra o quê |
|---|-------|--------------|
| 1 | contrato da API — endpoints, autenticação, payload | documentação atual do fornecedor |
| 2 | pré-requisitos | política atual de conta, plano e runtime |
| 3 | catálogo de erros | documentação + erros novos vistos nos trabalhos desde a última verificação |
| 4 | faixa de esforço | execuções reais novas |
| 5 | lacunas | o que virou documentado sai; o que se descobriu na marra entra |

Para cada endpoint: ainda existe com o mesmo método e caminho? A autenticação mudou de forma ou de nome? O payload ganhou campo obrigatório? Há depreciação anunciada com data?

## Prazos

| Campo | Prazo |
|-------|-------|
| contrato da API, catálogo de erros, pré-requisitos | 6 meses |
| cadeia de falha, plano de fases, stack, faixa de esforço | 12 meses |
| problema, sinônimos, fatias, o que cobre | 24 meses |

`verificado_em` do frontmatter é a **data mais antiga entre os campos** — conservador de propósito.

## Roteiro

1. Leia o `MODULO.md` inteiro e a procedência.
2. Confronte na ordem acima, contra a fonte externa.
3. Registre **cada divergência**, com o que dizia e o que é hoje.
4. Corrija o `MODULO.md`. Correção de campo não reescreve o módulo.
5. Atualize `verificado_em` por bloco e no frontmatter.
6. Acrescente linha à seção 14 (procedência): o que foi verificado, contra o quê, quando, resultado.
7. Atualize `verificado_em` no `modulos.json` — no repositório do catálogo,
   não numa cópia instalada em `.expx/modulex/` (ver `references/05-catalogo.md`).
8. **Avise retroativamente**, se houve divergência de contrato.

## O aviso retroativo

Divergência de contrato significa que os trabalhos que injetaram este módulo desde a última verificação **planejaram com informação errada**. Liste-os pelo `INDICE.md` e pelo memox:

```
Divergencia de contrato no modulo <id>.
<o que mudou>
Trabalhos que injetaram este modulo desde <data da ultima verificacao>:
  <trabalho> — <data> — <fase em que esta>
Quem estiver antes da F6 deve reinjetar; quem passou da F6 deve conferir <o campo>.
```

Sem isso, a verificação conserta o catálogo e deixa os planos errados de pé.

## Regras

- **Sem acesso à documentação do fornecedor, a verificação falha.** Registre "não verificado por falta de acesso" e **mantenha a data antiga**. Nunca carimbe em dia o que não foi conferido.
- **Fornecedor descontinuado**: marque `status: obsoleto`. O módulo **não sai do catálogo** — a M0 continua achando e responde "existiu, está obsoleto, motivo". Apagar faz a próxima pessoa refazer a descoberta.
- **API mudou tanto que o plano não vale**: isto não é verificação, é extração nova. Marque obsoleto, aponte o substituto, e rode M2 sobre um trabalho recente.
- **Divergência trazida por quem estava executando** (gatilho O3): corrija **só aquele campo**, com data nova só nele. Não custe uma verificação completa a quem só quis devolver um achado — atrito aqui mata a única fonte de correção que vem de campo.
- Faixa de esforço nova **não vira média**: registre as duas observações com data. Faixa é intervalo do que se viu.
