# Patch — runx usa a cadeia de falha do módulo como hipótese

Prompt autônomo. Aplique no repositório onde a skill `runx` está
instalada. Trabalhe até o fim, sem perguntar nada.

═══════════════════════════════════════════════════════════════════════
PARTE 1 — CONTRATO
═══════════════════════════════════════════════════════════════════════

Um ponto de contato só: a E1, investigação. E ele tem uma regra que
governa tudo.

A cadeia de falha (seção 11) e o catálogo de erros (seção 12) do módulo
são HIPÓTESES DE CAUSA A COMPROVAR — nunca causa declarada.

A E1 existe para comprovar a causa raiz. Uma lista de suspeitos que
chega com ar de diagnóstico faz a E1 pular a prova, que é a única coisa
que ela faz. O resultado é o pior defeito possível numa ocorrência: a
causa plausível que não era a causa, consertada, com o problema voltando
duas semanas depois.

O que o módulo dá é valioso e é outra coisa: A ORDEM EM QUE VALE OLHAR.

REGRAS DO PATCH
  1. A E1 continua exigindo PROVA para declarar causa raiz. O módulo não
     afrouxa isso em nada.
  2. O plano de fases do módulo NÃO DESCE para a E1. Ela investiga o que
     está quebrado, não constrói.
  3. A E2 (plano) e a E3 (fix) NÃO recebem injeção do modulex. O fix é
     deste sistema, sob TDD estrito.
  4. Nada muda quando o modulex não está instalado.
  5. Nenhum caminho absoluto.

═══════════════════════════════════════════════════════════════════════
PARTE 2 — O QUE ALTERAR
═══════════════════════════════════════════════════════════════════════

1. REFERENCE DA E1 (`references/01-causa.md` ou equivalente)

   Acrescente uma seção "Hipóteses vindas de módulo", no mapeamento da
   base de conhecimento — ANTES do roteiro de comprovação, nunca depois:

   - Quando a ocorrência for sobre integração com um terceiro, consulte
     `/modulex-buscar` e injete por `/modulex-injetar`.
   - A injeção traz SOMENTE as seções 11 (cadeia de falha e armadilhas),
     12 (catálogo de erros) e — se o sintoma for de configuração — 6
     (dependência de stack). O plano de fases não desce.
   - O material chega com rótulo obrigatório:

         Hipoteses do modulo <id> — NAO SAO CAUSA COMPROVADA.
         A E1 exige prova. Isto e a ordem em que vale olhar.

   - REGRA DE OURO, que o módulo zero declara e a E1 já pratica: ISOLE O
     ELO PRIMEIRO, LEIA O CÓDIGO DO ELO DEPOIS. Mudar código antes de
     localizar a quebra é como as tardes desaparecem.
   - O sintoma particiona a busca antes da primeira consulta. No módulo
     zero: recebimento quebra nos elos 3 a 7; envio quebra nos elos 1, 2
     ou na normalização do destinatário. Metade da cadeia sai da mesa.
   - O campo "o que o resultado prova" de cada elo é o que separa
     hipótese de diagnóstico. Ele diz também O QUE NÃO PROVA — e é essa
     metade que impede a conclusão apressada.

2. FRONTMATTER DA CAUSA RAIZ

   Acrescente, no padrão expx-schema v1:

       modulo_consultado: <id | ->
       hipotese_do_modulo: <elo/armadilha citada | ->
       hipotese_confirmada: <true | false | ->

   Chave nunca omitida. A terceira é o registro honesto de quantas vezes
   a hipótese do módulo estava certa — e é o que permite calibrar quanto
   confiar nele.

3. ADVERTÊNCIA SOBRE CÓDIGO DE ERRO

   Na mesma seção, duas cautelas:

   - CÓDIGO DE ERRO DO FORNECEDOR NÃO É CAUSA. Um 401 diz que a
     autenticação falhou; não diz se o cabeçalho está errado, se o
     segredo expirou ou se a conexão foi recriada do outro lado. O
     catálogo lista as três; a E1 prova qual é.
   - ERRO AUSENTE DO CATÁLOGO NÃO É ERRO IMPOSSÍVEL. É erro novo — e
     vira gatilho de verificação no fim da ocorrência.

4. DEVOLUÇÃO AO CATÁLOGO — na E5 (relatório)

   Quando a investigação encontrar armadilha que o módulo não listava, ou
   erro fora do catálogo, dispare `/modulex-verificar` sobre AQUELE CAMPO.

   Uma ocorrência real é a melhor fonte de cadeia de falha que existe:
   ela não descreve o que se imagina que quebre, mas o que quebrou.

   NÃO custe uma verificação completa a quem só quis devolver um achado.
   Atrito aqui mata a única fonte de correção que vem de campo.

5. SKILL.md DO RUNX

   Na tabela de integração, acrescente a linha do modulex: a cadeia de
   falha e o catálogo de erros do módulo entram na E1 como hipótese a
   comprovar. Uma linha, sem seção nova.

═══════════════════════════════════════════════════════════════════════
PARTE 3 — VERIFICAÇÃO E ENTREGA
═══════════════════════════════════════════════════════════════════════

VERIFICAÇÃO
  1. Sem modulex: investigue uma ocorrência e confirme comportamento
     idêntico, sem aviso.
  2. Com módulo injetado: confirme que a causa raiz só foi declarada COM
     PROVA, e que a hipótese do módulo aparece rotulada como hipótese em
     todo o documento.
  3. Confirme que o plano de fases do módulo NÃO entrou na E1.
  4. Confirme que a E2 e a E3 não receberam injeção nenhuma.
  5. Force o caso em que a hipótese do módulo estava ERRADA e confirme
     que `hipotese_confirmada: false` foi registrado, e que a
     investigação seguiu até a causa real.
  6. Grep por caminho absoluto no que foi alterado.

ENTREGA
  Os arquivos alterados com o diff de cada um; a nova seção da E1; as
  chaves novas do frontmatter; e o resultado das seis verificações.
