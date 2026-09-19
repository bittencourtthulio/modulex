---
description: M0 do modulex — consulta por problema em linguagem natural: existe modulo pronto para isto, e que fatias ele tem? Responde no chat, sem criar arquivo.
argument-hint: [problema em linguagem natural]
---

Use a skill `modulex`, estágio **M0**, seguindo `references/00-consulta.md`.

O que procurar está em `$ARGUMENTS`. Se vier vazio, peça o problema em uma frase de quem pede e pare.

## A regra de custo

Resolva o endereço do catálogo pela cadeia de `references/05-catalogo.md`,
parando no primeiro degrau que existir:

1. `$MODULEX_CATALOGO/modulos.json`
2. `.expx/modulex/docs/modulos/modulos.json`
3. `docs/modulos/modulos.json` — dentro do próprio repositório do catálogo
4. nenhum → **catálogo não alcançável**. Diga isso, diga como ligar, e siga.
   Não é `NAO EXISTE` e não registra lacuna.

Resolvido o degrau, leia **o `modulos.json` e nada mais.**

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

## O atalho determinístico

A busca também existe como script, e ela lê **apenas** o `modulos.json` — nunca clona repositório, nunca abre `MODULO.md`, nunca toca a rede:

```bash
python3 scripts/buscar.py "<problema em linguagem natural>"
```

Ele já imprime o degrau do catálogo, o `atualizado_em`, o `não cobre` de cada fatia e o aviso de vencimento.

## Candidato aparece marcado

Módulo com `status: candidato` é resultado legítimo da busca e vem **marcado como tal**. A informação mais valiosa dele é que *ele existe*: alguém aqui já integrou isso e o registro está pela metade, o que manda conversar com quem fez em vez de começar do zero.

O que ele **não** pode é virar rascunho de sprint na F3 nem artefato copiado na F6 (regra 13). Diga isso na resposta, sempre — candidato apresentado como módulo pronto é a forma mais rápida de produzir plano confiante e errado.

## Quando não acha

Registre no `LACUNAS.md` e, se o catálogo for compartilhado, abra a issue de lacuna:

```bash
python3 scripts/lacuna_issue.py "<termo procurado>"
```

Sem `--confirmar` ele só mostra o que faria. Termo repetido é o que prioriza a próxima extração — e distinguir "não existe módulo" de "faltava sinônimo num módulo que já existe" é trabalho manual que vale o esforço.

**Catálogo inalcançável não registra lacuna.** "Não há catálogo" e "há catálogo e ele não cobre isto" são respostas diferentes (D15).
