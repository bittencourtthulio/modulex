---
kind: modulo_indice
schema: expx-schema-v1
criado_em: <AAAA-MM-DD>
atualizado_em: <AAAA-MM-DD>
total_modulos: 0
ativos: 0
obsoletos: 0
vencidos: 0
---

# Indice de modulos

Append-only. Uma linha por modulo. E a versao legivel do `modulos.json` —
a consulta do M0 le o JSON; humano le este arquivo.

Modulo obsoleto **nunca e apagado**: a M0 continua achando e responde
"existiu, esta obsoleto, motivo". Apagar faz a proxima pessoa refazer a
descoberta.

| id | problema | fatias | fornecedor | esforco | verificado_em | status |
|----|----------|--------|------------|---------|---------------|--------|

## Indicadores

<Recalcule ao acrescentar linhas.>

- **Modulos no catalogo:** <n>
- **Ativos:** <n> · **Obsoletos:** <n>
- **Com verificacao vencida:** <n> — modulo vencido produz plano confiante e errado
- **Sem faixa de esforco:** <n> — o P4 do prodx dimensiona so pelas fatias nesses casos

## O indicador do modulex

<Dois numeros, e cada um sozinho mente. Precisam do memox para serem exatos;
sem ele, reporte o que o catalogo sabe e diga que a leitura esta parcial.>

- **Features com integracao de terceiro que consultaram um modulo antes de planejar:** <n de N>
- **Dessas, quantas viraram modulo novo depois:** <n>

| Leitura | Escrita | Diagnostico |
|---------|---------|-------------|
| alta | alta | ciclo fechado — estado saudavel |
| alta | zero | o catalogo apodrece: e lido, envelhece, ninguem devolve |
| zero | alta | a consulta esta cara demais |
| zero | zero | a skill nao pegou |
