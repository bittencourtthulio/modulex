# Patch — stackx tem precedência sobre a convenção do módulo

Prompt autônomo. Aplique no repositório onde a skill `stackx` está
instalada. Trabalhe até o fim, sem perguntar nada.

**Este é o patch mais delicado do conjunto.** É aqui que se decide se o
módulo traz conhecimento ou se traz a casa de origem inteira junto.

═══════════════════════════════════════════════════════════════════════
PARTE 1 — CONTRATO
═══════════════════════════════════════════════════════════════════════

O RISCO, DITO SEM RODEIO

Um módulo é extraído de um sistema real. Ele carrega, grudadas no
conhecimento útil, as convenções da casa onde nasceu: como as tabelas são
nomeadas, como o erro é sinalizado, em que idioma a interface fala, como
o cliente é identificado.

Injetar isso num projeto que JÁ TEM AS SUAS convenções é colocar a casa
de origem dentro da casa de destino. O código sai funcionando e sai
errado: errado no dialeto — que é exatamente o que o stackx existe para
proteger.

E o modo como isso acontece quase nunca é uma decisão. É OMISSÃO:
ninguém escolheu chamar a coluna de `company_id`; ela veio junto.

A SEPARAÇÃO OBRIGATÓRIA

Todo `MODULO.md` declara a dependência de stack em DUAS COLUNAS:

  essencial ao problema   quebra se mudar, porque o terceiro exige  VIAJA
  herdada da origem       escolha da casa onde nasceu               NÃO VIAJA

O teste, item a item: SE EU TROCAR ISTO, O TERCEIRO PARA DE FUNCIONAR?

No módulo zero: o endpoint precisa ser público porque o fornecedor chama
de fora — ESSENCIAL. A formatação do destinatário precisa preservar o
identificador de grupo — ESSENCIAL, é regra do WhatsApp. Já o nome
`company_id`, o formato de resposta `{success, data}` e a interface em
pt-BR são HERANÇA: o destino pode chamar de `org_id`, responder em outro
formato e falar outro idioma sem que nada quebre.

REGRAS DO PATCH
  1. O STACKX LOCAL VENCE. SEMPRE. Convenção herdada do sistema de origem
     nunca sobrepõe o `docs/stack/CONVENCOES.md` deste projeto.
  2. Convenção de módulo NUNCA entra no `CONVENCOES.md`. Se uma
     convenção essencial precisar valer aqui, entra como PROPOSTA, com
     confirmação humana, como qualquer outra proposta.
  3. Conflito na coluna ESSENCIAL é REPORTADO, não resolvido. O modulex
     não escolhe entre a regra de segurança do projeto e a exigência do
     fornecedor.
  4. Conflito na coluna HERDADA não é conflito: é tradução, e a injeção a
     faz.
  5. O stackx NÃO passa a consultar o modulex para detectar convenção. A
     detecção continua sendo sobre o repositório real, com evidência de
     arquivo e linha.
  6. Sem `CONVENCOES.md`, nada bloqueia.
  7. Nenhum caminho absoluto.

═══════════════════════════════════════════════════════════════════════
PARTE 2 — O QUE ALTERAR
═══════════════════════════════════════════════════════════════════════

1. SKILL.md DO STACKX — seção de precedência

   A tabela de precedência com o legadox ganha uma terceira linha,
   ABAIXO das duas existentes:

       legadox   o que existe hoje, na area tocada de projeto legado
       stackx    o que deve ser seguido daqui pra frente, neste projeto
       modulex   o que outro projeto fez, como INFORMACAO

   Com a regra: em projeto legado, o padrão local da área tocada manda; o
   stackx governa código novo em arquivo novo; e o módulo é insumo dos
   dois — NUNCA autoridade sobre nenhum.

2. NOVA ETAPA DE CONSULTA — `references/06-modulo.md` (arquivo novo)

   Etapa de consulta dirigida: recebe a seção 6 de um `MODULO.md` e
   devolve a tradução e os conflitos.

   ROTEIRO

   a) Para cada item da coluna ESSENCIAL: confronte com o
      `CONVENCOES.md`. Compatível → registre e siga. Incompatível →
      CONFLITO (item c).

   b) Para cada item da coluna HERDADA: traduza para o equivalente deste
      projeto, com evidência de arquivo e linha do `CONVENCOES.md`.

         <item do modulo> → <equivalente neste projeto>   CONVENCOES.md:<linha>

      Sem equivalente detectado: `PENDENTE`. NUNCA PREENCHA POR ANALOGIA
      — inventar o equivalente reintroduz a convenção alheia com outro
      nome.

   c) Formato do conflito, quando o item ESSENCIAL não tem tradução:

         CONFLITO — modulo <id>
           Modulo (essencial): <convencao>
           Projeto (CONVENCOES.md:<linha>): <convencao>
           Nao ha traducao. Isto e decisao humana, na F2 do sprintx.

      Exemplo real: o módulo zero exige um endpoint público sem
      autenticação de sessão. Um projeto cujo `CONVENCOES.md` diz "toda
      rota exige token autenticado" tem um conflito verdadeiro — e ele
      não é do stackx nem do modulex resolver. Mostre os dois e cale.

   d) Se a coluna HERDADA do módulo estiver VAZIA, sinalize: é suspeita.
      Praticamente todo módulo extraído carrega convenção da casa de
      origem, e não enxergá-la é o caminho mais curto para injetá-la.

3. ETAPA 4 (`references/04-check.md` ou equivalente) — verificação de
   aderência

   Acrescente uma advertência:

   - Código copiado de módulo é verificado contra o `CONVENCOES.md`
     DESTE projeto, como qualquer outro código.
   - Divergência em item TRADUZÍVEL (coluna herdada) é violação normal:
     aponte, com a correção sugerida.
   - Divergência em item ESSENCIAL do módulo NÃO é violação a corrigir:
     é o conflito do item 2c, e vai ao humano. Corrigi-lo "para ficar no
     padrão" quebra a integração.

   Esta distinção é obrigatória. Sem ela, a verificação de aderência
   conserta o dialeto e quebra o funcionamento.

4. SKILL.md DO STACKX — tabela de comandos e etapas

   Acrescente a etapa 6 na tabela de etapas, apontando para
   `references/06-modulo.md`. Uma linha.

5. O QUE NÃO MUDAR

   - a regra de evidência (arquivo e linha) — vale igual;
   - a distinção convenção × PROPOSTA;
   - a precedência com o legadox entre si;
   - a detecção das etapas 1 e 2: ela varre o repositório, não o catálogo.

═══════════════════════════════════════════════════════════════════════
PARTE 3 — VERIFICAÇÃO E ENTREGA
═══════════════════════════════════════════════════════════════════════

VERIFICAÇÃO
  1. Sem modulex: rode a detecção e a verificação de aderência e confirme
     comportamento idêntico.
  2. Com um `MODULO.md` cuja coluna herdada nomeia `company_id` e um
     `CONVENCOES.md` que usa outro nome: confirme que a saída é TRADUÇÃO,
     com evidência de linha, e não conflito.
  3. Com um `MODULO.md` cujo item essencial é "endpoint público sem
     autenticação de sessão" e um `CONVENCOES.md` que exige token em toda
     rota: confirme que a saída é CONFLITO, no formato do item 2c, e que
     o stackx NÃO escolheu um dos dois.
  4. Sem `CONVENCOES.md`: confirme `PENDENTE` na coluna traduzida e que
     nada bloqueou.
  5. Confirme que nenhuma convenção de módulo entrou no `CONVENCOES.md`
     — nem como convenção, nem como proposta automática.
  6. Confirme que a verificação de aderência não sugeriu "corrigir" um
     item essencial do módulo.
  7. Grep por caminho absoluto no que foi alterado.

ENTREGA
  Os arquivos alterados com o diff de cada um; o `references/06-modulo.md`
  novo; a linha nova da tabela de precedência; e o resultado das sete
  verificações — com destaque para a 3 e a 6, que são as que protegem a
  integração de ser "consertada" até quebrar.
