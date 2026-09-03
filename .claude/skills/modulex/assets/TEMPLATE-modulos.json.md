# Formato do `modulos.json`

O **indice maquina** do catalogo. E o unico arquivo que a consulta (M0) le.

A regra de custo do M0 depende disto: tudo que a consulta precisa responder
tem que caber aqui. Se faltar, a consulta seria obrigada a abrir o `MODULO.md`
de cada modulo — e a operacao mais frequente da skill vira a mais cara.

Se algo estiver faltando neste arquivo, o defeito e do indice, nao da consulta.
Corrija na M2 ou na M3.

## Estrutura

```json
{
  "schema": "expx-schema-v1",
  "kind": "modulo_indice_maquina",
  "atualizado_em": "AAAA-MM-DD",
  "modulos": [
    {
      "id": "<slug-do-modulo>",
      "problema": "<uma linha, na linguagem de quem pede>",
      "sinonimos": {
        "pt": ["<termo>"],
        "en": ["<termo>"],
        "fornecedores": ["<nome>"],
        "casa": ["<termo que a casa usa>"]
      },
      "fatias": [
        {
          "slug": "nucleo",
          "tipo": "obrigatoria",
          "problema": "<o que a fatia sozinha resolve>",
          "cobre": "<meia linha>",
          "nao_cobre": "<meia linha — nunca vazio>",
          "esforco": "<faixa | NAO DETERMINADO>"
        }
      ],
      "repo": "<url>",
      "stack_essencial": ["<item>"],
      "stack_herdada": ["<item>"],
      "pre_requisitos_bloqueantes": ["<item>"],
      "verificado_em": "AAAA-MM-DD",
      "verificado_contra": "<versao | doc publica em AAAA-MM-DD>",
      "extraido_de": "<sistema de origem>",
      "status": "ativo"
    }
  ]
}
```

## Campos, e por que cada um esta aqui

| Campo | Serve a |
|-------|---------|
| `problema` | a busca do M0, ramo 1 |
| `sinonimos` | a busca do M0, ramo 2 — os quatro grupos, sempre presentes |
| `fatias[].problema` | a busca do M0, ramo 3: a fatia resolve mesmo que o modulo inteiro seja maior que o pedido |
| `fatias[].nao_cobre` | o desfecho EXISTE PARCIAL, sem abrir o `MODULO.md` |
| `fatias[].esforco` | o P4 do prodx, para dimensionar o escopo minimo |
| `stack_essencial` / `stack_herdada` | o cruzamento com o stackx, ja na consulta |
| `pre_requisitos_bloqueantes` | responder "da para comecar hoje?" sem abrir o modulo |
| `verificado_em` | o aviso de vencimento (regra 8) |
| `status` | modulo obsoleto continua sendo achado, com o motivo |

## Regras

- **Chave nunca omitida** (expx-schema v1). Sem valor, `NAO DETERMINADO` ou lista vazia.
- `verificado_em` e a **data da verificacao mais antiga entre os campos** do modulo, nao a mais recente. E conservador de proposito.
- `nao_cobre` de cada fatia **nunca e vazio** (regra 7).
- O arquivo e derivado dos `MODULO.md`. Divergencia entre os dois: o `MODULO.md` manda, e o indice e corrigido.
- Nenhum caminho absoluto.

## Sobre o custo

Um catalogo de cem modulos com quatro fatias cada cabe em algumas centenas de
kilobytes de JSON — barato de ler inteiro a cada consulta. Se um dia deixar de
ser, o proximo passo e particionar por dominio, **nao** mover a busca para
dentro dos repositorios de modulo. A consulta barata e o que mantem a skill viva.
