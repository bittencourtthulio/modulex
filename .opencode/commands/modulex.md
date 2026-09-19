---
description: Roteador do modulex — estado do catalogo de modulos: quantos existem, quantos estao com verificacao vencida, e as lacunas mais pedidas.
---

Use a skill `modulex`.

Comando de entrada. Não executa estágio nenhum por conta própria: identifica o estado e encaminha.

## 1. Verifique o estado

Nesta ordem:

1. Algum degrau da cadeia de `references/05-catalogo.md` resolve?
   `$MODULEX_CATALOGO` → `.expx/modulex/docs/modulos/` → `docs/modulos/`
2. No degrau resolvido, `modulos.json` e `INDICE.md` existem?
3. Há módulos com `verificado_em` fora do prazo de `references/03-verificacao.md`?
4. Há termos repetidos no `LACUNAS.md`?

## 2. Se nenhum degrau resolver

Diga que o catálogo não está alcançável a partir daqui, liste os três degraus
tentados com o estado de cada um, e diga como ligar: definir
`MODULEX_CATALOGO` ou rodar `npx expxdev init`. **Não crie `docs/modulos/` no
projeto do cliente** — catálogo local nasce vazio em todo projeto novo e é
exatamente o desenho que a D1 descartou.

## 3. Se o catálogo existir e estiver vazio

Diga em três linhas o que ele é — o lugar onde o conhecimento de integração volta, em vez de ser redescoberto a cada projeto — e o que o cria: a M2, sobre uma feature já entregue que integrou um terceiro.

**Não crie arquivo vazio.** Catálogo sem módulo não serve para nada; o que serve é a primeira extração. Pergunte se há feature entregue que caiba, e ofereça `/modulex-extrair`.

## 4. Se existir com módulos, mostre o painel

```
modulex — catalogo de modulos
Catalogo: <caminho> (degrau <n>) · atualizado_em <data>
Modulos: <n> (<n> ativos, <n> obsoletos)
Verificacao vencida: <n>
Sem faixa de esforco: <n>

MODULOS
| id | problema | fatias | esforco | verificado_em | status |
|----|----------|--------|---------|---------------|--------|

LACUNAS MAIS PEDIDAS
| termo | vezes | encaminhamento |
|-------|-------|----------------|

CICLO
Features com integracao de terceiro que consultaram modulo: <n de N>
Dessas, quantas viraram modulo novo: <n>
```

Os dois números do ciclo vêm do memox. Sem memox, reporte o que o `INDICE.md` sabe e diga em uma linha que a leitura está parcial — não bloqueie.

## 5. Comente os indicadores

- **Verificação vencida acima de zero** — diga quais módulos e lembre que módulo desatualizado é pior que módulo nenhum: produz plano confiante e errado. Ofereça `/modulex-verificar`.
- **Leitura alta e escrita zero** — o catálogo está apodrecendo: é lido, envelhece, e ninguém devolve o que aprendeu. Aponte as features entregues que deveriam ter virado módulo.
- **Leitura zero e escrita alta** — a consulta está cara demais, ou ninguém sabe que ela existe. Investigue qual dos dois.
- **Lacuna com três ou mais ocorrências** — é a próxima extração, ou é sinônimo faltando num módulo que já existe. Diga qual dos dois, não os confunda.

## 6. Encaminhe

Diga qual comando roda: `/modulex-buscar`, `/modulex-injetar`, `/modulex-extrair` ou `/modulex-verificar`.

Não escolha módulo por ninguém: se houver mais de um candidato, liste e pergunte (regra 1).
