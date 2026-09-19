---
description: M2 do modulex — cria um modulo novo a partir de uma feature ja entregue que integrou um terceiro, costurando os artefatos que o sprintx e o mergex ja produziram.
argument-hint: [feature-entregue]
---

Use a skill `modulex`, estágio **M2**, seguindo `references/02-extracao.md` e o contrato em `references/04-contrato.md`.

O trabalho de origem está em `$ARGUMENTS`. Se vier vazio, pergunte qual feature entregue deve virar módulo e pare.

## Antes de extrair, confirme que cabe

| Extraia | Não extraia |
|---------|-------------|
| integrou um terceiro | é regra de negócio da casa |
| o problema se repete entre clientes | é de um cliente só |
| passou pelo QA e rodou em produção | ainda não rodou de verdade |

Feature entregue e nunca usada em produção não vira módulo: vira exemplo. O que dá valor ao módulo são as cicatrizes, e código que não rodou não tem nenhuma.

## Roteiro

1. **Reúna os artefatos do trabalho**: plano, orquestrador, `00-DECISOES.md`, `00-LACUNAS.md`, QA, `ENTREGA.md`, relatório de uso, ocorrências posteriores.
2. **Preencha as 14 seções** pela tabela de fontes de `references/02-extracao.md`, na ordem, sem pular.
3. **`NAO DETERMINADO` no que não tiver fonte** — e cada um vai para a seção 13, com o que seria preciso para preenchê-lo.
4. **Aplique o teste de essencial × herdado** item a item na seção 6: *se eu trocar isto, o terceiro para de funcionar?* Cuidado com o que parece stack mas é **decisão** — esse vai para a seção 5.
5. **Declare as fatias** e teste cada opcional: pode não ser instalada sem quebrar o núcleo? Se não pode, é núcleo.
6. **Escreva o "não cobre"** de cada fatia. Nunca vazio (regra 7).
7. **Sanitize**: nenhum token, domínio de cliente, telefone real ou referência de projeto. Módulo é conhecimento compartilhado — segredo que entra aqui vaza para todos os destinos.
8. **Grave o `MODULO.md`** na raiz do repositório do módulo, de `assets/TEMPLATE-MODULO.md`.
9. **Atualize o catálogo**: linha no `INDICE.md`, entrada no `modulos.json`, e remova do `LACUNAS.md` a lacuna que este módulo fecha.

   A M2 **escreve**, e escrita exige degrau gravável. Se o catálogo resolveu
   no degrau 2 — uma cópia instalada em `.expx/modulex/` — grave no
   repositório do catálogo, não na cópia: o próximo `expxdev init` a
   sobrescreve. Não sabendo onde ele está, **pergunte**; não grave na cópia
   "por enquanto".

## Regras

- **Faixa de esforço sem observação real é `NAO DETERMINADO`** (regra 6). Nunca estime, nunca infira por analogia, nunca use sensação. O P4 do prodx encolhe escopo com base nesse número.
- **Procedência é obrigatória** (regra 5): sistema de origem, data, versão da API. Sem procedência não há módulo.
- **A seção 13 é o ativo.** Se estiver vazia, o módulo é um resumo de documentação — e resumo de documentação não vale o custo de manter. Pergunte ao humano **pelo que deu errado**, não pelo que ele aprendeu: "o que quebrou e demorou para achar?" produz a seção 13; "quais foram as lições?" produz vazio.
- Artefato só é marcado "rodou em produção" quando se **sabe** que rodou. Na dúvida, "exemplo".
- Já existe módulo para este problema: **não crie o segundo por reflexo.** Decida com o humano entre um módulo com duas implementações e dois módulos irmãos com sinônimos cruzados, e registre.
- A feature integrou dois terceiros para problemas distintos: são dois módulos.

## Saída

Ao terminar, reporte:

```
Modulo extraido: <id>
Origem: <trabalho>, <sistema>, <data>
Fatias: <n> (<nucleo> + <n> opcionais)
Secoes NAO DETERMINADO: <quais, e o que fecharia cada uma>
Catalogo atualizado: INDICE.md, modulos.json, LACUNAS.md
```

A lista de `NAO DETERMINADO` não é vergonha: é o mapa do que o módulo ainda precisa. Módulo com lacuna declarada é honesto; módulo com campo inventado é perigoso.
