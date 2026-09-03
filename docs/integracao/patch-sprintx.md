# Patch — sprintx consome módulos do modulex

Prompt autônomo. Aplique no repositório onde a skill `sprintx` está
instalada. Trabalhe até o fim, sem perguntar nada.

═══════════════════════════════════════════════════════════════════════
PARTE 1 — CONTRATO
═══════════════════════════════════════════════════════════════════════

O sprintx é o consumidor principal do modulex. CINCO injeções, CINCO
autoridades diferentes. Se as autoridades vazarem uma para a outra, o
módulo deixa de ser conhecimento e vira ordem — e o projeto passa a
construir a casa de origem em vez da sua.

  F1  o MODULO.md das fatias escolhidas   insumo de primeira classe
  F2  as decisões de escopo já fechadas   PAUTA de entrevista
  F3  as fases e gates do plano pronto    RASCUNHO a adaptar
  F5  convenções e armadilhas             critério de prontidão
  F6  os artefatos de produção            copiar > reescrever, SOB TDD

REGRAS DO PATCH
  1. Nada muda quando o modulex não está instalado. As seis fases
     funcionam como sempre funcionaram.
  2. O TDD da F6 NÃO É RELAXADO POR NADA. Artefato copiado sem teste é
     dívida, não atalho.
  3. Convenção herdada do sistema de origem NUNCA sobrepõe o
     `docs/stack/CONVENCOES.md` deste projeto. Conflito é reportado ao
     humano, não resolvido.
  4. Decisão de escopo fechada no módulo entra como PERGUNTA na F2,
     nunca como resposta.
  5. O veredito do prodx tem precedência sobre o escopo do módulo. O
     módulo cobre mais do que foi aprovado; faz-se o que foi aprovado.
  6. A estimativa continua sendo da F3.5, sobre o plano real. A faixa do
     módulo é referência histórica e não entra na estimativa.
  7. Nenhum caminho absoluto.

═══════════════════════════════════════════════════════════════════════
PARTE 2 — O QUE ALTERAR
═══════════════════════════════════════════════════════════════════════

1. REFERENCE DA F1 (`references/01-base.md` ou equivalente)

   Acrescente uma seção "Consulta ao catálogo de módulos", no roteiro de
   ingestão:

   - Quando a feature envolver um terceiro (API externa, provedor,
     serviço), consulte o catálogo por `/modulex-buscar`, com o problema
     em linguagem natural.
   - Achando módulo, injete por `/modulex-injetar` — SÓ AS FATIAS
     ESCOLHIDAS. Sem escolha declarada, pergunte. Injetar o módulo
     inteiro faz a F3 planejar o que foi injetado, e ninguém pediu as
     fatias que vieram junto.
   - O `MODULO.md` entra na base como fonte, com procedência.
   - Os ARTEFATOS DE CÓDIGO NÃO DESCEM na F1. Vem o inventário (a lista),
     não os arquivos. Cópia é F6, task a task.
   - Cada bloco injetado chega marcado com a autoridade que terá adiante.
     Preserve essa marcação ao gravar a base — sem ela, a F3 trata o
     plano do módulo como plano aprovado e a F2 trata as decisões
     fechadas como decididas.

2. FRONTMATTER DA BASE E DO PLANO

   Acrescente, no padrão expx-schema v1:

       modulo_injetado: <id | ->
       modulo_fatias: [<slug>] | []
       modulo_verificado_em: <AAAA-MM-DD | ->

   Chave nunca omitida. `-` e `[]` quando não houver módulo.

3. REFERENCE DA F2 (`references/02-descoberta.md` ou equivalente)

   Acrescente "Decisões herdadas do módulo":

   - Cada linha da seção 5 do `MODULO.md` vira uma PERGUNTA ao usuário.
     O módulo decidiu para a casa dele, com as restrições dela.
   - Exemplo: o módulo zero fechou "credenciais no banco". A pergunta é
     "temos contas distintas por cliente, ou uma conta só?" — e a
     resposta certa MUDA COM O PROJETO.
   - Decisão herdada por omissão é o modo silencioso de a casa de origem
     se instalar aqui. A F2 é a última barreira antes de isso virar plano.
   - Registre a resposta nas decisões da feature, com a alternativa
     descartada — inclusive quando a resposta coincidir com a do módulo.

4. REFERENCE DA F3 (`references/03-sprints.md` ou equivalente)

   Acrescente "Plano do módulo como rascunho":

   - As fases e gates do módulo são RASCUNHO. Ponto de partida com valor
     real, que ainda assim não conhece este projeto.
   - O rascunho traz de bom: a ORDEM de construção (não óbvia e cara de
     descobrir), os GATES (resultados observáveis já testados em campo),
     e as dependências entre fases.
   - O rascunho não sabe: o que este projeto já tem pronto, as convenções
     deste repositório, o escopo mínimo aprovado no veredito, e o que a
     F2 acabou de decidir diferente.
   - REGRA: a F3 pode reordenar, fundir, cortar e renomear fases do
     módulo. NÃO PODE CORTAR GATE SEM SUBSTITUIR POR OUTRO — o gate é a
     parte do plano que veio da cicatriz, não da opinião.
   - A seção 6 do módulo (dependência de stack) é cruzada com o
     `CONVENCOES.md`. O stackx local vence. Ver `patch-stackx.md`.

5. REFERENCE DA F5 (`references/05-auditoria.md` ou equivalente)

   Acrescente três itens de auditoria, quando houver módulo injetado:

   - cada convenção inviolável do módulo virou item verificável no plano,
     OU está justificada como não aplicável a este projeto?
   - cada elo da cadeia de falha do módulo tem gate que o cobre?
   - cada decisão de escopo fechada do módulo foi DECIDIDA NESTE PROJETO
     na F2, ou entrou por omissão?

   O terceiro pega mais coisa que os outros dois juntos.

   ADVERTÊNCIA obrigatória na mesma seção: convenção HERDADA do sistema
   de origem NÃO vira item de auditoria se o stackx deste projeto diz
   outra coisa. Auditar contra a convenção da casa alheia é reprovar um
   plano por não parecer com outro projeto.

6. REFERENCE DA F6 (`references/06-execucao.md` ou equivalente)

   Acrescente "Artefatos do módulo":

   - Artefato marcado "rodou em produção" no inventário é para COPIAR,
     preferencialmente a reescrever. Ele carrega correções para modos de
     falha reais, invisíveis numa reescrita.
   - O QUE SE ADAPTA AO COPIAR, e só isso: nomes de tenancy, caminhos de
     import, os pontos de adaptação declarados pelo módulo. Melhoria de
     gosto não entra — se der vontade de melhorar, anote e siga.
   - COPIAR NÃO DISPENSA O TDD. O teste vem antes, e testa o
     comportamento NESTE projeto, não o artefato. Artefato copiado que
     não passa é adaptado, não aceito. Artefato copiado sem teste é
     dívida.
   - Artefato marcado "exemplo" NÃO tem esse privilégio: lê-se e
     escreve-se do zero, sob TDD, como qualquer código novo.

7. DEVOLUÇÃO AO CATÁLOGO — no fim do trabalho

   Duas obrigações, e são elas que fecham o ciclo:

   - divergência encontrada entre o módulo e a realidade (endpoint que
     mudou, erro novo, armadilha que não estava lá) → dispare
     `/modulex-verificar` sobre AQUELE CAMPO. É a única fonte de correção
     que vem de quem estava com a mão na massa.
   - feature que integrou terceiro sem módulo no catálogo → dispare
     `/modulex-extrair` na entrega.

   Sem essas duas, o catálogo só é lido, envelhece e apodrece.

8. SKILL.md DO SPRINTX

   Na tabela de integração, acrescente a linha do modulex: catálogo de
   problemas já resolvidos, consultado na F1, com autoridade diferente em
   cada fase. Uma linha, sem seção nova.

═══════════════════════════════════════════════════════════════════════
PARTE 3 — VERIFICAÇÃO E ENTREGA
═══════════════════════════════════════════════════════════════════════

VERIFICAÇÃO
  1. Sem modulex: planeje uma feature do zero e confirme comportamento
     idêntico, sem aviso.
  2. Com módulo injetado: confirme que a F2 produziu PERGUNTAS a partir
     da seção 5, e não decisões já tomadas.
  3. Confirme que a F3 tratou o plano do módulo como rascunho — e que
     nenhum gate foi cortado sem substituto.
  4. Confirme que a F6 exigiu teste antes de cada artefato copiado.
  5. Confirme que uma convenção herdada do módulo, conflitante com o
     `CONVENCOES.md`, NÃO virou item de auditoria na F5.
  6. Confirme que a estimativa da F3.5 não usou a faixa do módulo.
  7. Confirme que nenhum artefato de código desceu na F1.
  8. Grep por caminho absoluto no que foi alterado.

ENTREGA
  Os arquivos alterados com o diff de cada um; as seções novas de F1, F2,
  F3, F5 e F6; as chaves novas do frontmatter; e o resultado das oito
  verificações.
