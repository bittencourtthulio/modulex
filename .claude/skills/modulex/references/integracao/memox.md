# Integração — memox

O memox indexa o que as outras camadas gravaram. Os módulos entram nesse índice, e é isso que faz a **consulta do M0 melhorar com o tempo**.

Vale a mesma assimetria de sempre: são camadas independentes, e nenhuma bloqueia a outra.

## O que o memox indexa do modulex

| Fonte | O que se extrai |
|-------|-----------------|
| `docs/modulos/INDICE.md` | um registro por módulo: id, problema, fatias, data de verificação |
| `MODULO.md` de cada módulo, seções 1 e 2 | problema e sinônimos — as chaves de busca |
| `docs/modulos/LACUNAS.md` | o que foi procurado e não existe, com quantas vezes |

**Não indexa** os artefatos de código do módulo. Eles são código de outro projeto: entram no índice deste projeto só quando forem efetivamente copiados na F6, e aí como arquivo deste repositório, pela via normal.

Nem indexa as seções 9 a 12 (plano, inventário, cadeia de falha, erros). Elas são grandes, mudam a cada verificação, e a busca do M0 é sobre **problema**, não sobre implementação. Indexá-las encheria o índice de conteúdo que ninguém procura por ali.

## Como a consulta melhora com o tempo

A busca do M0 é textual sobre `problema` e `sinonimos`. Ela erra pelo mesmo motivo que a busca de existência do prodx erra: **nomenclatura**. O usuário pergunta com uma palavra, o módulo foi indexado com outra.

O memox corrige isso porque acumula três coisas que o `modulos.json` sozinho não tem:

1. **Os termos que a casa realmente usa.** O relatório de uso do runx descreve o sistema na linguagem do cliente. É de lá que saem os sinônimos que ninguém pensaria em escrever.
2. **As buscas que falharam.** Cada `NAO EXISTE` do M0 vira linha no `LACUNAS.md`. Termo que aparece três vezes e nunca acha nada é sinal de duas coisas possíveis: falta o módulo, ou falta o sinônimo num módulo que já existe. **Distinguir os dois é trabalho manual e vale o esforço** — o segundo caso é uma linha de correção que evita uma extração inteira desnecessária.
3. **Qual módulo foi injetado em qual trabalho.** Cruzando com o problema da feature, aparece o vocabulário real que levou até aquele módulo.

## O ciclo de melhoria

```
busca falha no M0
  → linha no LACUNAS.md
    → memox conta as repeticoes
      → termo repetido sem modulo    → candidato a extracao (M2)
      → termo repetido com modulo    → sinonimo faltando (corrige a secao 2)
```

O segundo ramo é o que ninguém lembra de olhar, e é o mais barato de consertar: uma linha na seção 2 do `MODULO.md` conserta uma busca que falhava havia meses.

## O indicador vem daqui

O indicador do modulex — features com integração de terceiro que consultaram um módulo antes de planejar, cruzado com quantas viraram módulo novo depois — precisa de dois números que só o memox tem juntos:

- **quantas features integraram terceiro** (do índice de trabalhos);
- **quantas consultaram e quantas extraíram** (do índice de módulos e do `LACUNAS.md`).

Sem memox, o `/modulex` reporta só o que está no `INDICE.md` do catálogo — quantos módulos, quantos vencidos, quais lacunas mais pedidas — e diz que a metade da leitura está indisponível. Menos preciso, não bloqueante.

## O que não muda no memox

- O memox continua sendo índice invertido, sem embedding e sem chamada de modelo. Indexar módulo não muda isso: `problema` e `sinonimos` são texto, e busca textual sobre eles é exatamente o caso de uso do índice.
- O índice continua derivado e descartável: reconstruir do zero é sempre seguro.
- As regras de ruído continuam valendo. Um problema que casa com quatro módulos informa a contagem em vez de despejar os quatro.
- A regra 7 do memox (nunca injetar segredo) vale em dobro aqui: módulo é conhecimento compartilhado entre projetos, e um segredo que entra vaza para todos os destinos. A sanitização é obrigação da M2, na extração.
