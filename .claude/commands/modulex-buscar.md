---
description: M0 do modulex — consulta por problema em linguagem natural: existe modulo pronto para isto, e que fatias ele tem? Responde no chat, sem criar arquivo.
argument-hint: [problema em linguagem natural]
---

Use a skill `modulex`, estágio **M0**, seguindo `references/00-consulta.md`.

O que procurar está em `$ARGUMENTS`. Se vier vazio, peça o problema em uma frase de quem pede e pare.

## A regra de custo

Leia **`docs/modulos/modulos.json` e nada mais.**

Não clone repositório de módulo, não abra artefato, não leia o `MODULO.md` inteiro, não chame a API de fornecedor nenhum. O módulo zero sozinho tem mais de 150 arquivos de artefato: clonar isso para responder "existe módulo?" é o custo que faz uma ferramenta ser contornada no primeiro dia.

Só a injeção puxa peso.

## Roteiro

Procure o **problema**, não o fornecedor. Nesta ordem:

1. `problema` de cada módulo
2. `sinonimos` — pt, en, fornecedores, termos da casa
3. `fatias[].problema` — a fatia pode resolver mesmo que o módulo inteiro seja maior que o pedido
4. `LACUNAS.md` — já foi pedido antes e não existe
5. Índice do memox, por termo — módulo extraído mas ainda não indexado; achado fraco, reporte como tal

## Saída

```
Modulo — "<o que foi procurado>"
Termos: <termos e sinonimos tentados, inclusive os que nao acharam>
Desfecho: <EXISTE | EXISTE (VENCIDO) | EXISTE PARCIAL | NAO EXISTE>

<id> — <problema>
Fatias:
  <fatia>  <nucleo|opcional>  <esforco>  <o que cobre>
Stack essencial: <o que viaja>
Stack herdada: <o que o stackx do destino traduz>
Verificado em: <data>
Repo: <url>

Nao cobre: <as bordas relevantes ao que foi pedido>
```

Se vencido, o aviso vai na **primeira linha**, antes do conteúdo:

```
ATENCAO: verificado pela ultima vez em <data> (<n> meses). Modulo desatualizado
produz plano confiante e errado. Rode /modulex-verificar antes de injetar.
```

## Regras

- **Não escolha fornecedor** (regra 1). Dois módulos para o mesmo problema: liste os dois lado a lado e cale. A escolha é da F2/F3 do sprintx, com o stackx.
- **Não invente módulo.** Não sugira "o fornecedor X provavelmente serve" — isso é escolha de fornecedor.
- **`NAO EXISTE` registra a busca no `LACUNAS.md`**, com termo e data. É assim que o catálogo aprende o que falta.
- **Módulo pronto não é evidência de que o sistema já faz.** Se quem chamou foi o P3 do prodx, diga isso explicitamente: é sinal de custo menor, não de existência.
- Registre também os termos que **não** acharam nada — é o que impede a próxima pessoa de repetir a busca.
- Pedido vago demais: peça o problema em uma frase. Não busque por adivinhação — termo errado devolve `NAO EXISTE` falso, e falso negativo aqui custa um plano inteiro.
