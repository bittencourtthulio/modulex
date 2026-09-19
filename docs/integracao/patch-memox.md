# Patch — memox indexa os módulos do modulex

Prompt autônomo. Aplique no repositório onde a skill `memox` está
instalada. Trabalhe até o fim, sem perguntar nada.

═══════════════════════════════════════════════════════════════════════
PARTE 1 — CONTRATO
═══════════════════════════════════════════════════════════════════════

O memox indexa o que as outras camadas gravaram. Os módulos entram nesse
índice, e é isso que faz a CONSULTA DO M0 MELHORAR COM O TEMPO.

A busca do modulex é textual sobre `problema` e `sinonimos`. Ela erra
pelo mesmo motivo que a busca de existência do prodx erra: NOMENCLATURA.
O usuário pergunta com uma palavra, o módulo foi indexado com outra.

REGRAS DO PATCH
  1. O memox continua sendo ÍNDICE INVERTIDO. Sem embedding, sem banco
     vetorial, sem chamada de modelo para ingerir. Indexar módulo não
     muda isso: `problema` e `sinonimos` são texto, e busca textual sobre
     eles é exatamente o caso de uso do índice.
  2. O índice continua derivado e descartável.
  3. NÃO indexar os artefatos de código do módulo. São código de outro
     projeto; entram no índice deste projeto só quando forem copiados na
     F6, e aí como arquivo deste repositório, pela via normal.
  4. NÃO indexar as seções 9 a 12 (plano, inventário, cadeia de falha,
     erros). São grandes, mudam a cada verificação, e a busca do M0 é
     sobre PROBLEMA, não sobre implementação.
  5. A regra 7 do memox (nunca injetar segredo) vale em dobro: módulo é
     conhecimento compartilhado entre projetos, e um segredo que entra
     vaza para todos os destinos.
  6. As regras de ruído continuam valendo.
  7. Nada muda quando o modulex não está instalado.
  8. Nenhum caminho absoluto.

NOTA SOBRE O CAMINHO DO CATÁLOGO
  O catálogo é compartilhado entre projetos e NÃO vive dentro do projeto
  em que a skill roda. Onde este patch escrever `<catalogo>/`, resolva
  pela cadeia de quatro degraus do modulex, parando no primeiro que
  existir:

      1. $MODULEX_CATALOGO/
      2. .expx/modulex/docs/modulos/
      3. docs/modulos/        (dentro do proprio repositorio do catalogo)
      4. nenhum -> catalogo nao alcancavel; siga sem modulo (regra 11)

  Sem rede e sem caminho absoluto. O degrau 4 NUNCA bloqueia, e nao e a
  mesma coisa que "o catalogo nao tem este modulo".

═══════════════════════════════════════════════════════════════════════
PARTE 2 — O QUE ALTERAR
═══════════════════════════════════════════════════════════════════════

1. FONTES INDEXADAS (`references/01-indexacao.md` e a tabela do SKILL.md)

   Acrescente três fontes:

       <catalogo>/INDICE.md          um registro por modulo: id, problema,
                                     fatias, data de verificacao
       MODULO.md (secoes 1 e 2)      problema e sinonimos — as chaves de busca
       <catalogo>/LACUNAS.md         o que foi procurado e nao existe, com
                                     quantas vezes

   Com a nota explícita de que os artefatos de código e as seções 9 a 12
   FICAM DE FORA, e por quê.

2. CHAVES DO ÍNDICE

   Acrescente:

       por modulo    modulos cujo problema ou sinonimos casam com um termo
       por lacuna    termos buscados sem resultado, com contagem

3. SINAL DERIVADO NOVO — termo buscado sem resultado

   Acrescente aos sinais derivados a contagem de repetição por termo do
   `LACUNAS.md`, com O CICLO DE DOIS RAMOS:

       busca falha no M0
         → linha no LACUNAS.md
           → termo repetido SEM modulo   → candidato a extracao (M2)
           → termo repetido COM modulo   → sinonimo faltando na secao 2

   O segundo ramo é o que ninguém lembra de olhar, e é o mais barato de
   consertar: uma linha na seção 2 do `MODULO.md` conserta uma busca que
   falhava havia meses.

   DISTINGUIR OS DOIS RAMOS É TRABALHO MANUAL e vale o esforço:
   confundi-los custa uma extração inteira desnecessária. O memox
   apresenta a contagem e diz que a distinção é humana — não a decide.

4. DE ONDE SAEM OS SINÔNIMOS QUE FALTAM

   Acrescente, na mesma seção: o relatório de uso do runx descreve o
   sistema NA LINGUAGEM DO CLIENTE. É de lá que saem os sinônimos que
   ninguém pensaria em escrever. Cruzar o problema de uma feature com o
   módulo que foi injetado nela devolve o vocabulário real que levou até
   aquele módulo.

5. O INDICADOR DO MODULEX

   Acrescente uma consulta que devolve os dois números do indicador,
   porque só o memox os tem juntos:

       features com integracao de terceiro que consultaram modulo: <n de N>
       dessas, quantas viraram modulo novo: <n>

   O primeiro sai do índice de trabalhos cruzado com `modulo_injetado`
   no frontmatter da base; o segundo, do `INDICE.md` do catálogo.

   Sem essa consulta, o `/modulex` reporta só o que o catálogo sabe e
   avisa que a leitura está parcial. Menos preciso, NÃO BLOQUEANTE.

6. SKILL.md DO MEMOX

   Na tabela de integração com as demais camadas, acrescente a linha do
   modulex: o memox indexa problema, sinônimos e lacunas do catálogo, e é
   o que faz a consulta do M0 melhorar com o tempo. Uma linha.

═══════════════════════════════════════════════════════════════════════
PARTE 3 — VERIFICAÇÃO E ENTREGA
═══════════════════════════════════════════════════════════════════════

VERIFICAÇÃO
  1. Sem catálogo alcançável: reconstrua o índice e confirme que ele sai
     igual ao anterior, sem erro e sem aviso.
  2. Com catálogo: confirme que os artefatos de código do módulo NÃO
     entraram no índice, e que as seções 9 a 12 também não.
  3. Confirme que um termo repetido no `LACUNAS.md` aparece com contagem,
     e que o memox NÃO decide entre os dois ramos — apresenta.
  4. Confirme que a consulta do indicador devolve os dois números.
  5. Confirme que reconstruir o índice do zero continua seguro.
  6. Confirme que nenhum segredo de `MODULO.md` entrou no índice.
  7. Grep por caminho absoluto no que foi alterado.

ENTREGA
  Os arquivos alterados com o diff de cada um; as fontes novas; as chaves
  novas do índice; o sinal derivado novo; e o resultado das sete
  verificações.
