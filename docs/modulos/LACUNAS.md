---
kind: modulo_lacunas
schema: expx-schema-v1
criado_em: 2026-09-02
atualizado_em: 2026-09-19
total_buscas_sem_resultado: 2
---

# Lacunas do catálogo

O que o catálogo **não cobre** e já foi pedido.

Toda busca do M0 que termina em `NAO EXISTE` entra aqui. Este arquivo é o que
transforma falha de busca em pauta: termo que aparece três vezes e nunca acha
nada é a próxima extração a fazer — ou o sinônimo que falta num módulo que já
existe.

## Buscas sem resultado

| data | termo procurado | quem/onde | vezes | encaminhamento |
|------|-----------------|-----------|-------|----------------|
| 2026-09-19 | `emitir nota fiscal` | verificação da instalação, a partir do ExpxNFe | 1 | `aguardando` |
| 2026-09-19 | `nfs-e` | verificação da instalação, a partir do ExpxNFe | 1 | `aguardando` |

As duas buscas acima foram feitas na verificação da cadeia de resolução, de
dentro de um projeto que não é o do catálogo. **Não são features**: não contam
no indicador do ciclo, que mede feature com integração de terceiro que consultou
módulo antes de planejar. Contam aqui, porque foram buscas reais que terminaram
em `NAO EXISTE`, e é isso que esta tabela registra.

O encaminhamento é `aguardando` nas duas: não há, ainda, feature entregue de
emissão de nota fiscal que caiba numa extração. Havendo, elas viram `extrair`.

`encaminhamento` assume um de três valores:

| valor | significa |
|-------|-----------|
| `aguardando` | ainda não houve trabalho que resolvesse este problema |
| `extrair` | já existe feature entregue que vira módulo; rodar M2 |
| `sinonimo:<id>` | o módulo existe, faltava o termo na seção 2. Corrigido lá |

## Os dois ramos

```
busca falha no M0
  → linha aqui
    → termo repetido SEM modulo    → candidato a extracao (M2)
    → termo repetido COM modulo    → sinonimo faltando (corrige a secao 2 do MODULO.md)
```

O segundo ramo é o que ninguém lembra de olhar, e é o mais barato de consertar:
uma linha na seção 2 conserta uma busca que falhava havia meses. Distinguir os
dois ramos é trabalho manual e vale o esforço — confundi-los custa uma extração
inteira desnecessária.

## Lacunas conhecidas do próprio catálogo

O que se sabe que falta, independentemente de alguém ter buscado.

| Lacuna | Impacto | O que fecharia |
|--------|---------|----------------|
| **domínios inteiros sem nenhum módulo**: pagamento, storage, email transacional, nota fiscal, assinatura digital | são os problemas que a especificação da skill nomeia como recorrentes, e o catálogo não cobre nenhum. Nota fiscal já foi procurada de verdade, duas vezes, e não estava lá | uma extração (M2) por domínio, a partir de feature já entregue |
| **nenhum módulo tem faixa de esforço observada** | o P4 do prodx dimensiona só pelas fatias, sem ordem de grandeza | a primeira implantação cronometrada sob o método Expx fecha, se alguém registrar as datas |
| **o único módulo do catálogo é de uma stack só** (React + Supabase) | não há evidência de como o contrato se comporta com módulo de outra stack — a separação essencial × herdada nunca foi testada num segundo caso | a segunda extração, preferencialmente de stack diferente |
| **nenhum módulo publicou `erros.json`** | a E1 do runx não pode cruzar código de erro com o log sem ler prosa | produzir o do `whatsapp-uazapi` na próxima verificação |
