---
description: Puxa o catalogo do GitHub para a copia local (degrau 2). E o unico ponto do modulex que toca a rede.
argument-hint: [url-base opcional]
---

Use a skill `modulex`, seguindo `references/05-catalogo.md` e `references/07-publicacao.md`.

## O que este comando é

O **único** ponto do modulex que toca a rede. A M0 continua offline: ela lê o arquivo que este comando deixou no disco.

Essa separação é o que permitiu o catálogo em nuvem sem quebrar a promessa de consulta barata (D16). Uma busca que depende do GitHub estar no ar viola a regra 11 toda vez que a rede cai.

## Roteiro

1. **Rode a sincronização.**

   ```bash
   python3 scripts/sincronizar.py
   ```

   Com um remoto diferente do padrão, passe `--remoto <url-base>` ou defina `MODULEX_REMOTO`. `$ARGUMENTS`, se vier preenchido, é o remoto.

2. **Ela usa ETag**: rodar de novo sem mudança no remoto não baixa nada. Rodar à toa é barato.

3. **Confira onde caiu.** O destino padrão é `.expx/modulex/docs/modulos/` — o degrau 2 da cadeia. A próxima consulta vai declarar esse degrau sozinha.

4. **Confirme que a M0 voltou a responder.**

   ```bash
   python3 scripts/buscar.py "<algum problema conhecido>"
   ```

## Quando a rede falha

**Nunca bloqueia.** A cópia local anterior continua valendo, e catálogo velho é problema da M3, não da M0 (regra 11).

Reporte a falha em uma linha e siga. O que **não** fazer: clonar o repositório do catálogo por conta própria, criar `docs/modulos/` vazio no projeto do cliente, ou responder `NAO EXISTE` — "não há catálogo alcançável" e "há catálogo e ele não cobre isto" são respostas diferentes, e só a segunda registra lacuna.

## Espelhos

Por padrão baixa só `modulos.json`, `INDICE.md` e `LACUNAS.md` — é tudo que a M0 precisa.

Os `MODULO.md` espelhados descem com `--com-espelhos`, e só valem a pena se você vai injetar offline depois. A M1 os busca sob demanda; os **artefatos** só descem na F6, task a task.

## Saída

```
Catalogo sincronizado em <pasta> (degrau 2)
Baixados: <n> · sem mudanca: <n>
Modulos: <n> · atualizado_em <data>
Vencidos: <n>
```

Se houver módulo vencido, diga quais: módulo desatualizado é pior que módulo nenhum, porque produz plano confiante e errado (regra 8).
