---
kind: modulo_lacunas
schema: expx-schema-v1
criado_em: <AAAA-MM-DD>
atualizado_em: <AAAA-MM-DD>
total_buscas_sem_resultado: 0
---

# Lacunas do catalogo

O que o catalogo **nao cobre** e ja foi pedido.

Toda busca do M0 que termina em `NAO EXISTE` entra aqui. Este arquivo e o que
transforma falha de busca em pauta: termo que aparece tres vezes e nunca acha
nada e a proxima extracao a fazer — ou o sinonimo que falta num modulo que ja
existe.

## Buscas sem resultado

| data | termo procurado | quem/onde | vezes | encaminhamento |
|------|-----------------|-----------|-------|----------------|

<`encaminhamento` assume um de tres valores:
  `aguardando`        — ainda nao houve trabalho que resolvesse este problema
  `extrair`           — ja existe feature entregue que vira modulo; rodar M2
  `sinonimo:<id>`     — o modulo existe, faltava o termo na secao 2. Corrigido la.>

## Os dois ramos

```
busca falha no M0
  → linha aqui
    → termo repetido SEM modulo    → candidato a extracao (M2)
    → termo repetido COM modulo    → sinonimo faltando (corrige a secao 2 do MODULO.md)
```

O segundo ramo e o que ninguem lembra de olhar, e e o mais barato de consertar:
uma linha na secao 2 conserta uma busca que falhava havia meses. Distinguir os
dois ramos e trabalho manual e vale o esforco — confundi-los custa uma extracao
inteira desnecessaria.

## Lacunas conhecidas do proprio catalogo

<O que se sabe que falta, independentemente de alguem ter buscado.
Ex.: dominio inteiro sem nenhum modulo; modulo que existe mas sem plano;
modulo sem faixa de esforco observada.>

| Lacuna | Impacto | O que fecharia |
|--------|---------|----------------|
