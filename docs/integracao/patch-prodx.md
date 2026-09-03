# Patch — prodx consulta o catálogo do modulex

Prompt autônomo. Aplique no repositório onde a skill `prodx` está
instalada. Trabalhe até o fim, sem perguntar nada.

═══════════════════════════════════════════════════════════════════════
PARTE 1 — CONTRATO
═══════════════════════════════════════════════════════════════════════

O prodx decide SE há trabalho. O modulex informa QUANTO CUSTA o trabalho
que existe pronto. São perguntas diferentes, e a distinção entre elas é
o que este patch protege.

Três pontos de contato, todos com autoridade de SINAL:

  P3  um sexto lugar de busca
  P4  faixa de esforço e fatias, para dimensionar o escopo mínimo
  P5  a linha `modulo_disponivel` na seção de contexto do briefing

REGRAS DO PATCH
  1. MÓDULO PRONTO NÃO É EVIDÊNCIA DE QUE O SISTEMA JÁ FAZ. Ninguém
     instalou nada. O achado do modulex NUNCA produz desfecho `existe`
     nem `existe_parcial` no P3.
  2. Nada muda quando o modulex não está instalado. O P3 procura nos
     cinco lugares e o P4 dimensiona como sempre dimensionou.
  3. A regra 10 do prodx continua valendo integralmente: o briefing não
     contém decisão técnica.
  4. A regra 5 (evidência) continua valendo: o achado do modulex é
     evidência de que o MÓDULO existe, não de que a FUNCIONALIDADE
     existe.
  5. O P0 (triagem) NÃO consulta o modulex. Nenhum dos oito gatilhos
     depende do catálogo, e acrescentar consulta ao caminho curto
     encarece a etapa que existe para ser barata.
  6. Não existe veredito novo. Os quatro continuam os mesmos.
  7. Nenhum caminho absoluto.

═══════════════════════════════════════════════════════════════════════
PARTE 2 — O QUE ALTERAR
═══════════════════════════════════════════════════════════════════════

1. REFERENCE DO P3 (`references/03-existencia.md`)

   Acrescente um sexto lugar de busca, DEPOIS dos cinco existentes, com
   uma advertência que não pode ser suavizada:

     ### 6. O catálogo do modulex

     Responde uma pergunta DIFERENTE dos cinco anteriores. Os cinco
     respondem "o sistema já faz isso?". Este responde "alguém já
     resolveu isso antes, em outro projeto?".

     Módulo pronto NÃO é evidência de existência. Se o P3 tratasse o
     achado como existência, devolveria `ja_existe` para uma
     funcionalidade que ninguém instalou — e o cliente receberia um
     texto explicando onde encontrar uma tela que não está lá.

     O que o módulo prova é outra coisa: fazer é mais barato do que
     parece. Isso não muda o desfecho do P3; muda o P4.

     Consulte por `/modulex-buscar`, com o problema em linguagem natural.
     Registre o achado como ACHADO À PARTE, e siga determinando o
     desfecho pelos cinco lugares originais.

   Na tabela "onde foi procurado", acrescente a sexta linha:

     | 6 | Catalogo do modulex | <id>, fatias <n>, esforco <faixa> — NAO e evidencia de existencia |

   NÃO altere os três desfechos, nem a tabela de EXISTE vs EXISTE
   PARCIAL, nem a definição de evidência.

2. TEMPLATE DO `02-existencia.md` (`assets/TEMPLATE-existencia.md`)

   Na seção 2, acrescente a sexta linha da tabela, com o mesmo texto de
   advertência. Acrescente ao frontmatter:

       tem_modulex: <true | false>
       modulo_encontrado: <id | ->

   Chave nunca omitida; `-` quando não houver.

3. REFERENCE DO P4 (`references/04-avaliacao.md`)

   Na seção de escopo mínimo, acrescente:

     ### Fatias do módulo, quando houver

     "WhatsApp completo" e "só receber e responder mensagem" são escopos
     mínimos de tamanhos muito diferentes, e a distinção é decisão de
     produto — que é o que o P4 decide.

     Quando o P3 achou módulo, use as FATIAS dele para dimensionar: o
     núcleo costuma resolver o pedido, e as extensões respondem a coisas
     que o cliente não pediu.

     Duas travas:
     - a faixa de esforço é REFERÊNCIA HISTÓRICA, não estimativa. Entra
       como ordem de grandeza — "isso é dia ou é mês" — e nunca como
       número no veredito. Estimar continua sendo a F3.5 do sprintx.
     - o escopo mínimo NÃO NOMEIA TECNOLOGIA. Escreva "receber e
       responder mensagem de WhatsApp num inbox compartilhado", nunca
       "instalar o módulo whatsapp-uazapi".

     Faixa `NAO DETERMINADO`: use as fatias e IGNORE o esforço. Fatia sem
     esforço ainda dimensiona escopo; esforço inventado envenena o
     veredito.

4. TEMPLATE DO BRIEFING (`assets/TEMPLATE-BRIEFING.md`)

   Na seção "Contexto para quem vai investigar" — e SOMENTE nela —
   acrescente uma linha opcional:

       modulo_disponivel: <id do modulo> — <fatias que cobrem o escopo minimo aprovado>

   Com a nota, no mesmo bloco de advertência que já existe ali:

     <Contexto, nunca instrução. Pode dizer que existe módulo e qual é;
     NÃO pode dizer para usá-lo, nem citar tabela, biblioteca, endpoint,
     arquitetura ou fornecedor como decisão tomada. Regra 10.>

5. SKILL.md DO PRODX

   Na tabela de integração, acrescente uma linha: o modulex oferece um
   sexto lugar de busca no P3 e as fatias que dimensionam o escopo mínimo
   no P4, sempre como sinal. Uma linha, sem seção nova.

   Na lista "O que o prodx NÃO faz", nada muda.

═══════════════════════════════════════════════════════════════════════
PARTE 3 — VERIFICAÇÃO E ENTREGA
═══════════════════════════════════════════════════════════════════════

VERIFICAÇÃO
  1. Sem modulex instalado: rode um P3 completo e confirme comportamento
     idêntico ao anterior, sem aviso.
  2. Com módulo no catálogo que casa com o pedido: confirme que o
     desfecho do P3 NÃO virou `existe` nem `existe_parcial` por causa
     dele, e que o achado aparece como linha 6, rotulada.
  3. Confirme que um P4 com módulo achado produz escopo mínimo SEM nome
     de fornecedor e SEM número de estimativa.
  4. Com módulo de faixa `NAO DETERMINADO` (o módulo zero): confirme que
     o P4 dimensiona pelas fatias e não inventa esforço.
  5. Confirme que a triagem (P0) não ganhou consulta nenhuma.
  6. Confirme que o briefing gerado tem `modulo_disponivel` apenas na
     seção de contexto, e nenhuma decisão técnica em nenhuma seção.
  7. Grep por caminho absoluto no que foi alterado.

ENTREGA
  Os arquivos alterados com o diff de cada um; a nova seção do P3; as
  chaves novas do frontmatter; a linha nova do briefing; e o resultado
  das sete verificações.
