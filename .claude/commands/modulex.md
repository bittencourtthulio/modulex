---
description: Roteador do modulex — estado do catalogo de modulos: quantos existem, quantos estao com verificacao vencida, e as lacunas mais pedidas.
---

Use a skill `modulex`.

Comando de entrada. Não executa estágio nenhum por conta própria: identifica o estado e encaminha.

## 1. Verifique o estado

Nesta ordem:

1. `docs/modulos/modulos.json` existe?
2. `docs/modulos/INDICE.md` existe?
3. Há módulos com `verificado_em` fora do prazo de `references/03-verificacao.md`?
4. Há termos repetidos no `LACUNAS.md`?

## 2. Se o catálogo não existir

Diga em três linhas o que ele é — o lugar onde o conhecimento de integração volta, em vez de ser redescoberto a cada projeto — e o que o cria: a M2, sobre uma feature já entregue que integrou um terceiro.

**Não crie arquivo vazio.** Catálogo sem módulo não serve para nada; o que serve é a primeira extração. Pergunte se há feature entregue que caiba, e ofereça `/modulex-extrair`.

## 3. Se existir, mostre o painel

```
modulex — catalogo de modulos
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

## 4. Comente os indicadores

- **Verificação vencida acima de zero** — diga quais módulos e lembre que módulo desatualizado é pior que módulo nenhum: produz plano confiante e errado. Ofereça `/modulex-verificar`.
- **Leitura alta e escrita zero** — o catálogo está apodrecendo: é lido, envelhece, e ninguém devolve o que aprendeu. Aponte as features entregues que deveriam ter virado módulo.
- **Leitura zero e escrita alta** — a consulta está cara demais, ou ninguém sabe que ela existe. Investigue qual dos dois.
- **Lacuna com três ou mais ocorrências** — é a próxima extração, ou é sinônimo faltando num módulo que já existe. Diga qual dos dois, não os confunda.

## 5. Encaminhe

Diga qual comando roda: `/modulex-buscar`, `/modulex-injetar`, `/modulex-extrair` ou `/modulex-verificar`.

Não escolha módulo por ninguém: se houver mais de um candidato, liste e pergunte (regra 1).
