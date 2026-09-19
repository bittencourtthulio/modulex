# Patch — buildx consulta o catálogo ao decompor, e devolve ao fechar

Prompt autônomo. Aplique no repositório onde a skill `buildx` está
instalada. Trabalhe até o fim, sem perguntar nada.

═══════════════════════════════════════════════════════════════════════
PARTE 1 — CONTRATO
═══════════════════════════════════════════════════════════════════════

O buildx é a única camada que vê o projeto INTEIRO antes de existir uma
linha de código. Isso o torna o melhor lugar da suíte para consultar o
catálogo — e o pior lugar para deixá-lo decidir qualquer coisa.

Dois pontos de contato, um em cada ponta:

  B3 DECOMPOSIÇÃO — ao recortar o projeto em features, saber quais
                    delas integram terceiro que já tem módulo pronto.
  B5/B6 FECHAMENTO — feature entregue que integrou terceiro e não tinha
                    módulo é candidata a extração.

E um ponto que NÃO é de contato, e é o que mais tenta sê-lo: o B2,
fundação. Ver a regra 3 abaixo.

REGRAS DO PATCH
  1. O MÓDULO NÃO RECORTA O PROJETO. Os três testes do B3 — vertical,
     enunciável, demonstrável — continuam sendo os únicos que decidem
     onde cortar. A fatia de um módulo é CANDIDATA a fronteira, nunca a
     fronteira. Módulo recortando feature é a casa de origem decidindo a
     arquitetura da casa de destino.
  2. O buildx NÃO ESCOLHE FORNECEDOR, exatamente como o modulex não
     escolhe (regra 1 do modulex). Dois módulos para o mesmo problema
     entram no `MAPA.md` como dois candidatos, e a escolha acontece na
     F2/F3 do sprintx, dentro do B4.
  3. NO B2 O MÓDULO É INFORMAÇÃO, NUNCA STACK. A stack herdada de um
     módulo não entra no `CONVENCOES.md` do projeto novo. Projeto novo
     não tem convenção ainda, e é justamente aí que a herança alheia
     entra sem resistência — o risco é maior no greenfield, não menor.
     O stackx decide a stack; o módulo informa o que é ESSENCIAL ao
     terceiro (o que quebra se mudar) e isso entra como RESTRIÇÃO
     TÉCNICA a respeitar, não como convenção a adotar.
  4. O modulex não muda a pergunta única. O buildx continua fazendo
     EXATAMENTE UMA pergunta, no começo. Ver a PARTE 2, item 3 — é o
     ponto mais delicado deste patch.
  5. Nada muda quando o modulex não está instalado, e catálogo não
     alcançável é ausência de modulex.
  6. Nenhum caminho absoluto.

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

1. REFERENCE DO B3 (`references/04-decomposicao.md`)

   Acrescente uma seção "Features que já têm módulo", DEPOIS dos três
   testes e da faixa de tamanho — nunca antes. A ordem importa: o
   recorte se decide pelos testes, e só então se pergunta o que já
   existe pronto.

   - Terminado o recorte, para cada feature que integra um terceiro
     (pagamento, WhatsApp, storage, email transacional, nota fiscal,
     assinatura digital, mensageria), rode `/modulex-buscar` com o
     PROBLEMA da feature, na linguagem do `MAPA.md` — nunca com o nome
     do fornecedor.
   - O desfecho entra na feature como campo, não como texto solto:

         modulo_disponivel: <id | ->
         fatias_candidatas: <slugs | ->

   - NÃO REFAÇA O RECORTE para casar com as fatias do módulo. Se as
     fatias sugerirem fronteira diferente, ANOTE a divergência e siga
     com o seu recorte. As fatias do módulo conhecem o sistema de onde
     ele saiu; os três testes conhecem este projeto.
   - EXCEÇÃO ÚNICA, e ela é estreita: quando uma fatia opcional do
     módulo cobrir exatamente uma feature que o seu recorte tinha
     GRUDADO em outra, e separá-las passar nos três testes, separe. O
     ganho é real e o risco é zero — você está usando o módulo como
     evidência de que a fronteira existe, não como razão para criá-la.

2. ORDEM DENTRO DO B3

   A consulta ao catálogo acontece DEPOIS de o `MAPA.md` estar fechado
   e ANTES de ele ser gravado. Nessa janela ela é barata e não
   contamina; fora dela, ou não influencia nada (tarde demais) ou
   decide o recorte (cedo demais).

3. O CONFLITO COM A PERGUNTA ÚNICA — resolva assim

   O M1 do modulex, por desenho (D13), NÃO injeta um módulo inteiro por
   padrão: sem fatia indicada, ele PERGUNTA quais fatias. O buildx não
   pode perguntar: depois da pergunta única, nenhuma outra chega ao
   usuário, em nenhum dos dois modos.

   Resolução, pelo mesmo mecanismo que o buildx já usa para a F2 do
   sprintx (`respondido_por: buildx`):

   - O buildx RESPONDE A ESCOLHA DE FATIAS NO LUGAR DO HUMANO, derivando-a
     do escopo da feature no `MAPA.md` — que é a fonte legítima, porque
     saiu do `PROJETO.md` e das premissas.
   - A escolha vira PREMISSA registrada em `docs/projeto/PREMISSAS.md`
     ANTES de ser usada, no formato de sempre: o que assume, o que a
     invalidaria.

         Premissa: a feature FT-04 usa as fatias <nucleo> do modulo
         <id>. Assume que <o que o MAPA.md diz que a feature entrega>.
         Invalidada se o usuario quiser <o que as fatias descartadas
         cobrem>.

   - INJETAR TUDO É PROIBIDO, e é a saída preguiçosa que este item
     existe para fechar. Injetar as seis fatias de um módulo grande numa
     feature que só quer receber e responder mensagem enche a base da F1
     com IA, ligações e grupos que ninguém pediu — e a F3 planeja o que
     foi injetado. No modo autônomo ninguém vai perceber a tempo.
   - Não sendo derivável do `MAPA.md` qual fatia serve, a premissa é
     **só o núcleo obrigatório**. Núcleo é o mínimo que o módulo declara
     como indivisível; errar para menos custa uma injeção a mais depois,
     errar para mais custa um projeto inteiro planejado a mais.

4. B4 CONSTRUÇÃO — nada novo a fazer

   O B4 chama o sprintx, e o sprintx já foi alterado pelo
   `patch-sprintx.md`: a F1 ingere, a F2 pauta, a F3 rascunha, a F5
   audita, a F6 copia sob TDD. O buildx só passa adiante o
   `modulo_disponivel` e as `fatias_candidatas` da feature.

   NÃO DUPLIQUE a lógica de injeção aqui. Se o sprintx não estiver
   alterado, o buildx segue sem módulo — e isso é correto, não é falha.

5. B5/B6 — A DEVOLUÇÃO, que é a metade que sempre falta

   No fechamento, para cada feature entregue que integrou um terceiro e
   tinha `modulo_disponivel: -`, registre-a como CANDIDATA A EXTRAÇÃO e
   ofereça `/modulex-extrair`.

   O buildx é o melhor gatilho de extração da suíte inteira, porque
   acabou de construir um sistema com o plano, o QA e o relatório de
   entrega ainda na mão — que são exatamente as fontes das 14 seções do
   `MODULO.md`. Seis meses depois esse material está frio e a extração
   custa dez vezes mais.

   Dois limites:
   - **Ofereça, não execute.** A extração publica conhecimento entre
     clientes e exige sanitização — nenhum token, domínio de cliente ou
     telefone real. Isso não roda em modo autônomo.
   - **Feature entregue e nunca usada em produção não vira módulo.** O
     que dá valor ao módulo são as cicatrizes, e código que ainda não
     rodou de verdade não tem nenhuma. Registre como candidata e diga
     que falta produção.

6. FRONTMATTER DO `MAPA.md`

   Acrescente ao contrato da feature, no padrão expx-schema v1, chave
   nunca omitida:

       modulo_disponivel: <id | ->
       fatias_candidatas: <slugs separados por virgula | ->

7. SKILL.md DO BUILDX

   Na tabela de camadas ("O que faz pelo buildx"), acrescente a linha:

       | `modulex` | responde se a feature já tem módulo pronto (B3);
                     recebe de volta o que virou módulo novo (B5/B6) |

   E uma linha na máquina de estados do B3 dizendo que a consulta
   acontece com o `MAPA.md` fechado e antes de gravado. Sem seção nova.

═══════════════════════════════════════════════════════════════════════
PARTE 3 — VERIFICAÇÃO E ENTREGA
═══════════════════════════════════════════════════════════════════════

VERIFICAÇÃO
  1. Sem modulex instalado: decomponha um projeto e confirme
     comportamento idêntico, sem aviso e sem campo vazio sobrando.
  2. Com catálogo alcançável e um módulo que casa: confirme que o
     `MAPA.md` traz `modulo_disponivel` preenchido E que o recorte das
     features é o MESMO que seria sem o módulo. Este é o teste central
     do patch — se o recorte mudou, a regra 1 foi violada.
  3. Confirme que o `CONVENCOES.md` do B2 não recebeu NENHUMA convenção
     herdada do módulo, e que o que entrou (se entrou) está rotulado
     como restrição técnica essencial ao terceiro.
  4. Rode o modo AUTÔNOMO ponta a ponta e confirme que o usuário foi
     interrompido exatamente UMA vez, e que a escolha de fatias está em
     `PREMISSAS.md` antes do primeiro uso.
  5. Force o caso de feature sem fatia derivável do `MAPA.md` e confirme
     que a premissa registrada foi **só o núcleo**, nunca o módulo
     inteiro.
  6. Force dois módulos para o mesmo problema e confirme que os dois
     entraram como candidatos e que o buildx NÃO escolheu.
  7. Feche um projeto com feature de terceiro sem módulo e confirme que
     ela foi registrada como candidata a extração, e que a extração foi
     OFERECIDA e não executada.
  8. Grep por caminho absoluto no que foi alterado.

ENTREGA
  Os arquivos alterados com o diff de cada um; a nova seção do B3; as
  chaves novas do contrato da feature; e o resultado das oito
  verificações — com destaque para a 2 e a 4, que são as que protegem o
  recorte e a pergunta única.
