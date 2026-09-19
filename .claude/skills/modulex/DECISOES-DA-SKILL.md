# Decisões da skill

Ambiguidades encontradas na construção do modulex e como foram resolvidas.
Cada uma registra o que estava em aberto, a decisão tomada, a alternativa
descartada, e o que faria revisá-la.

---

## D1 — Onde vive o catálogo

**Ambiguidade:** o índice (`INDICE.md`, `modulos.json`, `LACUNAS.md`) vive
num repositório irmão do ecossistema, ou em `docs/modulos/` de cada projeto?
O critério é se módulo é ativo do ecossistema ou da casa.

**Os dois lados:**

*Catálogo local, em cada projeto.* A favor: consulta sem rede, sem
dependência externa, e o catálogo carrega só o que aquele projeto usa. É o
desenho do memox, que funciona bem. Contra — e é decisivo: **o módulo não é
conhecimento deste projeto, é conhecimento entre projetos.** O problema que o
modulex resolve é literalmente que o terceiro projeto a integrar a mesma
coisa sai pior que o primeiro. Um catálogo local nasce vazio em todo projeto
novo, que é exatamente onde o módulo mais valeria. E a extração feita no
cliente A nunca chega ao cliente B — o ciclo não fecha, e a skill vira um
memox com outro nome.

*Repositório irmão, catálogo compartilhado.* A favor: o conhecimento
atravessa projetos, que é a razão de a skill existir; uma extração serve
todos os clientes; a verificação é feita uma vez e vale para todos. Contra:
introduz dependência externa, e um catálogo compartilhado que fica fora do ar
não pode bloquear ninguém.

**Decisão: repositório irmão, catálogo compartilhado.**

**Por quê:** módulo é ativo do **ecossistema**, não da casa. O contra do
catálogo compartilhado é mitigável — regra 11: a ausência do modulex nunca
bloqueia nenhuma outra skill, e catálogo inacessível é ausência. O contra do
catálogo local não é mitigável: ele quebra o mecanismo central da skill.

**Como um projeto consome sem clonar tudo:**

- a **consulta (M0)** lê **apenas `modulos.json`** — um arquivo. Nenhum
  repositório de módulo é clonado;
- a **injeção (M1)** puxa **apenas o `MODULO.md`** do módulo escolhido — mais
  um arquivo;
- os **artefatos** só descem na **F6**, task a task, quando forem copiados.

O módulo zero sozinho tem mais de 150 arquivos de artefato. Clonar isso para
responder "existe módulo?" tornaria cara a operação mais frequente da skill —
e a M0 cara é a morte da skill, pelo mesmo raciocínio da triagem do prodx.
Daí a regra: **a consulta é barata e só a injeção puxa peso.**

**O que revisa:** se o `modulos.json` crescer a ponto de a leitura integral
pesar, particione por domínio. **Não** mova a busca para dentro dos
repositórios de módulo — isso reintroduz o custo que o desenho evita.

---

## D2 — Módulo com fatias declaradas

**Ambiguidade:** o contrato permite módulo com núcleo obrigatório e extensões
opcionais, ou todo módulo é monolítico?

**Decisão:** permite, e **exige** — a seção 3 é obrigatória. Módulo pequeno
declara fatia única, chamada `nucleo`.

**Por quê:** isto importa muito para o P4 do prodx. "WhatsApp completo" e "só
receber e responder mensagem" são escopos mínimos de tamanhos muito
diferentes, e essa distinção é **decisão de produto, não técnica** — que é
precisamente o que o P4 decide. Sem fatias, o P4 dimensionaria o escopo
mínimo pelo módulo inteiro e o veredito sairia grande demais, quando o
núcleo resolvia.

**A trava:** toda fatia opcional passa no teste de **poder não ser instalada
sem quebrar o núcleo**. Se não pode, é núcleo — e declará-la opcional é
mentira que a F3 descobre no meio da execução.

**Alternativa descartada:** um módulo por fatia (seis módulos de WhatsApp).
Descartada porque quebra a chave de busca: quem procura "atender por
WhatsApp" acharia seis resultados parciais e teria de reconstruir sozinho
qual combinação resolve. A fatia é uma subdivisão **dentro** do problema,
não um problema à parte.

---

## D3 — Plano se adapta, artefato se copia

**Ambiguidade:** o módulo entrega plano, não código — mas o `wa-implement`
do módulo zero diz, com todas as letras, para copiar os artefatos e nunca
reescrevê-los de memória. As duas coisas parecem se contradizer.

**Decisão:** distinção explícita entre dois tipos de conteúdo, com regras
opostas. O **plano** é rascunho a adaptar; o **artefato de produção** é para
copiar preferencialmente a reescrever. E **ambos passam pela F5 e pelo TDD da
F6**.

**Por quê:** a contradição é aparente. O plano é conhecimento **sobre este
projeto** que ainda não existe — só a F3 pode produzi-lo, porque só ela
conhece o projeto. O artefato é conhecimento **sobre o terceiro**, que não
muda de projeto para projeto: um `CREATE TABLE` reescrito à mão perde o
`GRANT` que existe porque, sem ele, o frontend recebe array vazio sem erro. A
correção não está visível no código — está na cicatriz.

**A trava:** copiar não é atalho para o TDD. O teste vem antes, e testa o
comportamento **neste** projeto, não o artefato. Artefato copiado sem teste é
dívida, não atalho. E artefato marcado "exemplo" no inventário não tem o
privilégio: escreve-se do zero.

---

## D4 — Autoridade diferente em cada ponto de injeção

**Ambiguidade:** o modulex entrega a mesma informação para prodx, sprintx e
runx. Ela vale igual nos três?

**Decisão:** não. A autoridade muda por ponto, e está declarada na tabela das
três injeções. Sinal no prodx, insumo/pauta/rascunho/critério no sprintx,
hipótese no runx.

**Por quê:** cada skill responde uma pergunta diferente, e a mesma informação
tem peso diferente em cada uma.

- No **prodx**, tratar "existe módulo" como "já existe no sistema" faria o P3
  devolver `ja_existe` para funcionalidade que ninguém instalou — e o cliente
  receberia um texto explicando onde encontrar uma tela que não está lá. Esse
  erro volta pelo atendimento, que é o caminho mais caro.
- No **runx**, a cadeia de falha lida como diagnóstico faz a E1 pular a
  prova, que é a única coisa que a E1 faz. Produz a causa plausível que não
  era a causa, consertada, com o problema voltando em duas semanas.
- No **sprintx**, o plano lido como decidido faz a F2 não perguntar o que
  precisa perguntar, e a casa de origem entra por omissão.

**Alternativa descartada:** uma autoridade única ("o módulo é referência").
Descartada porque referência é vago o bastante para virar qualquer coisa sob
pressão — e sob pressão a tendência é sempre tratar o que está pronto como
decidido.

---

## D5 — A consulta não lê o `MODULO.md`

**Ambiguidade:** a M0 precisa responder fatias, esforço, o que não cobre e
stack. Isso está no `MODULO.md`. Ela lê o `MODULO.md` de cada módulo?

**Decisão:** não. A M0 lê **apenas `modulos.json`**, e tudo que ela precisa
responder é **duplicado** ali.

**Por quê:** a M0 é a operação mais frequente da skill. Ler N arquivos
`MODULO.md` — cada um com catorze seções, tabelas de erro e planos de fase —
para responder "existe módulo?" é caro por definição, e cresce com o
catálogo. A duplicação custa um índice a manter; a alternativa custa a
adoção da skill.

**O custo aceito:** `modulos.json` e os `MODULO.md` podem divergir. A regra
de desempate está no contrato: **o `MODULO.md` manda, e o índice é
corrigido.** A M0 reporta a divergência quando a percebe, e não tenta
consertá-la — reindexar é trabalho da M2/M3.

**O que revisa:** se a divergência virar frequente, o índice precisa ser
gerado, não escrito à mão. Aí a M2 e a M3 passam a emitir o `modulos.json` a
partir dos `MODULO.md`, em vez de editá-lo.

---

## D6 — O módulo zero nasce sem faixa de esforço

**Ambiguidade:** o contrato exige a faixa de esforço (seção 8). O
`whatsapp-uazapi-integration` não declara esforço em lugar nenhum, e foi
extraído de um sistema em produção sem cronometragem da construção original.

**Decisão:** `esforco: NAO DETERMINADO` no frontmatter, `NAO DETERMINADO` em
**todas as seis fatias**, e entrada na seção 13 com o que fecharia a lacuna.

**Isto é o comportamento correto, não um defeito da extração.** Valida a
regra 11 do prodx aplicada aqui: campo não verificável vira `NAO
DETERMINADO`, nunca chute.

**Por quê:** módulo com faixa de esforço inventada é **pior que módulo
inexistente**, porque o P4 do prodx encolhe escopo com base em ficção — e o
escopo encolhido vira prazo, e o prazo vira promessa ao cliente. Sem faixa, o
P4 dimensiona pelas fatias, que é menos preciso e é honesto.

**O que fecharia:** uma execução real cronometrada por fatia, ou o relatório
de entrega do mergex de uma implantação. A primeira implantação deste módulo
sob o método Expx fecha a lacuna sozinha, se alguém registrar as datas.

**O que revisa:** nada. Enquanto não houver observação real, o campo
permanece `NAO DETERMINADO`. Não há prazo que transforme ausência de dado em
dado.

---

## D7 — Os outros campos que o módulo zero não preencheu

**Ambiguidade:** a especificação manda registrar todo campo do template que o
módulo zero não conseguiu preencher, e dizer se o problema é do **campo mal
desenhado** ou de uma **lacuna real do módulo**.

**Resultado da validação — sete campos ficaram `NAO DETERMINADO`:**

| # | Campo | Diagnóstico |
|---|-------|-------------|
| 1 | faixa de esforço, nas seis fatias | **lacuna real do módulo.** Ver D6 |
| 2 | catálogo de códigos de erro do fornecedor | **lacuna real.** A seção 12 saiu montada dos modos de falha observados na origem, não da documentação. Não há `erros.json`. Fecha lendo a documentação atual da Uazapi |
| 3 | limites de taxa do fornecedor | **lacuna real.** O módulo não implementa limitação nem espera entre envios, e não se sabe qual é o teto |
| 4 | política de banimento de número | **lacuna real e provavelmente permanente.** Sabe-se que enviar para quem não tem relação causa banimento; o limiar não é publicado pelo WhatsApp. Só observação de campo fecha |
| 5 | comportamento sob volume | **lacuna real.** O módulo rodou num CRM; não há medida de quantas mensagens por segundo o desenho suporta |
| 6 | custo do fornecedor | **lacuna real, e fácil de fechar.** Basta consultar o plano vigente |
| 7 | conformidade legal | **lacuna real e séria.** A Uazapi é provedor **não oficial**; a implicação legal e contratual não foi avaliada pela extração. É avaliação jurídica do cliente de destino, não da skill |

**Nenhum dos sete é campo mal desenhado.** Os catorze campos do contrato
foram preenchíveis a partir do repositório de origem, e os sete acima faltam
porque **a informação não existe**, não porque o campo pediu a coisa errada.

**Dois achados sobre o desenho do contrato, ainda assim:**

1. **O campo 7 (conformidade) não existia no contrato e foi parar nas
   lacunas.** "A Uazapi é provedor não oficial" é o tipo de informação que
   decide se o módulo pode ser usado — e não tinha lugar próprio. Ele entrou
   na seção 4 (o que NÃO cobre, primeira linha do núcleo) e na 13. Fica o
   registro: se aparecer um segundo módulo com restrição legal ou de
   conformidade, isso vira seção própria, e não mais uma borda.

2. **A seção 10 precisou de ressalva de procedência.** As migrações "rodaram
   em produção", mas foram **consolidadas** na extração — cada tabela virou um
   `CREATE TABLE` único — e o `GRANT` foi **acrescentado**, porque as
   originais não o emitiam. O SQL final não é byte a byte o que rodou. A marca
   binária "rodou em produção" × "exemplo" não capturava isso, e a ressalva
   foi escrita em prosa. **Se isso se repetir, a marca vira ternária:**
   `producao` · `producao_consolidado` · `exemplo`. Um módulo não é evidência
   suficiente para mudar o contrato.

---

## D8 — Prazos de verificação por tipo de campo

**Ambiguidade:** a especificação exige definir o que torna um módulo
obsoleto, mas não dá prazo.

**Decisão:** prazos diferentes por campo — 6 meses para contrato da API,
catálogo de erros e pré-requisitos; 12 para cadeia de falha, plano, stack e
esforço; 24 para problema, sinônimos, fatias e cobertura. O `verificado_em`
do frontmatter é a **data mais antiga entre os campos**.

**Por quê:** um prazo único erra nos dois sentidos — revalida cedo demais o
que não muda e tarde demais o que muda toda semana. O problema do usuário
("atender cliente por WhatsApp") é praticamente estável; o endpoint da API
não é.

**Por que a data mais antiga, e não a mais recente:** é conservador de
propósito. Um módulo cujo contrato tem sete meses está vencido, ainda que a
seção de fatias tenha sido revista ontem — e é o contrato que quebra o plano.

**O que revisa:** os prazos são chute calibrado, não medida. Se, com uma
dúzia de módulos verificados, as divergências de contrato aparecerem
sistematicamente antes dos 6 meses, o prazo aperta.

---

## D9 — Módulo obsoleto não é apagado

**Ambiguidade:** fornecedor descontinuado, API que mudou por completo. O
módulo sai do catálogo?

**Decisão:** não. `status: obsoleto`, e a M0 continua achando — respondendo
"existiu, está obsoleto, motivo".

**Por quê:** mesma lógica da regra 9 do prodx (pedido recusado não é
apagado). A descoberta de que aquele caminho não serve **é** conhecimento, e
custou alguém. Apagar faz a próxima pessoa refazer a descoberta e,
provavelmente, chegar à mesma conclusão pelo caminho caro.

---

## D10 — A triagem do prodx não consulta o catálogo

**Ambiguidade:** o modulex entra no P3, P4 e P5. E no P0?

**Decisão:** não. Nenhum dos oito gatilhos depende do catálogo, e a triagem
não consulta.

**Por quê:** a triagem existe para ser barata — é a diferença entre uma
ferramenta que o dev usa e uma que ele contorna. Acrescentar consulta ao
caminho curto encarece exatamente a etapa desenhada para não custar nada. E
não muda desfecho nenhum: saber que existe módulo não faz um gatilho disparar
nem deixar de disparar.

**O que revisa:** se aparecerem pedidos triados como triviais que eram, na
verdade, integração de terceiro inteira, o problema é o gatilho **G8**
(esforço maior que alguns dias) estar frouxo — não a ausência de consulta.

---

## D11 — O indicador tem dois números, não um

**Ambiguidade:** o prodx mede uma coisa (a taxa de pedidos que não viram
trabalho). Qual é o equivalente honesto aqui?

**Decisão:** dois números — a proporção de features com integração de
terceiro que **consultaram** um módulo antes de planejar, cruzada com quantas
delas **viraram módulo novo** depois.

**Por quê:** cada número sozinho mente, e mente de um jeito diferente.

- Leitura alta com escrita zero: o catálogo **apodrece**. É lido, envelhece,
  e ninguém devolve o que aprendeu. Vira enfeite em seis meses, e pior que
  enfeite — vira fonte de plano confiante e errado.
- Escrita alta com leitura zero: a **consulta está cara demais**. Alguém se
  dá ao trabalho de extrair, e ninguém consulta antes de planejar. É o
  sintoma que a regra de custo da M0 existe para prevenir.

**Alternativa descartada:** contagem de módulos no catálogo. Descartada por
ser vaidade: quarenta módulos que ninguém consulta custam manutenção e não
devolvem nada. O que importa é o **ciclo**, e ciclo tem duas metades.

**Dependência:** os dois números exatos precisam do memox. Sem ele, o
`/modulex` reporta o que o catálogo sabe e declara a leitura parcial — menos
preciso, nunca bloqueante.

---

## D12 — O que é stack e o que é decisão

**Ambiguidade:** "credenciais no banco, não em variáveis de ambiente" é
dependência de stack (seção 6) ou decisão de escopo (seção 5)?

**Decisão:** é **decisão** (seção 5), e portanto vira **pauta da F2**.

**Por quê:** o teste da seção 6 é *se eu trocar isto, o terceiro para de
funcionar?* — e a Uazapi não sabe nem se importa com onde a credencial está
guardada. A escolha nasce de o sistema de origem ser multi-tenant com contas
distintas por cliente. Num projeto de tenant único, variável de ambiente é
mais simples e mais segura.

**Por que importa:** classificar isso como stack faria a M1 entregá-lo como
tradução (`banco → banco`), e o projeto de destino herdaria a complexidade
sem nunca ter sido perguntado se precisa dela. Classificando como decisão, a
F2 pergunta — e a resposta certa muda com o projeto.

**A regra que sobra, escrita em `references/02-extracao.md`:** nem tudo que
parece stack é stack. Quando o item é uma escolha que o projeto de destino
pode legitimamente refazer, ele pertence à seção 5.

---

## D13 — Injeção sem fatia declarada não injeta tudo

**Ambiguidade:** a M1 é chamada sem indicação de fatias. Injeta o módulo
inteiro, ou pergunta?

**Decisão:** pergunta. Nunca injeta o módulo inteiro por padrão.

**Por quê:** injetar as seis fatias do módulo zero num projeto que só quer
receber e responder mensagem enche a base de conhecimento com IA, ligações e
grupos que ninguém pediu — e a F3 **planeja o que foi injetado**. O custo de
uma pergunta é uma linha; o de um plano com três sprints a mais é uma
conversa difícil com o cliente.

**Alternativa descartada:** injetar só o núcleo por padrão. Descartada porque
o núcleo nem sempre é o que se quer — um projeto que já tem WhatsApp
funcionando e quer só a camada de organização precisa exatamente do
contrário.

---

## D14 — Divergência achada em campo corrige só um campo

**Ambiguidade:** a F6 ou a E1 descobre que o módulo diverge da realidade. Isso
dispara uma verificação completa (M3) do módulo?

**Decisão:** não. Corrige **só aquele campo**, com data nova só nele. É o
gatilho O3 da verificação.

**Por quê:** quem está executando ou investigando tem uma tarefa em curso e
não pode pagar uma revalidação completa para devolver um achado. Se o preço
de devolver for alto, ninguém devolve — e a correção vinda de campo é a mais
valiosa que existe, porque é a única que veio de quem estava com a mão na
massa.

**O contrapeso:** o `verificado_em` do frontmatter continua sendo a data mais
antiga entre os campos (D8). Corrigir um campo não renova o prazo do módulo,
e não esconde que o resto está velho.

---

## D15 — Como o projeto consumidor encontra o catálogo compartilhado

**Ambiguidade:** a D1 decidiu que o catálogo vive num repositório irmão,
compartilhado entre projetos. Ela não disse **como** um projeto qualquer
chega até lá. Toda instrução operacional da skill nasceu lendo o caminho
relativo `docs/modulos/modulos.json` — que, dentro de um projeto de cliente,
resolve para o próprio projeto e devolve catálogo vazio. A D1 estava correta e
inaplicável ao mesmo tempo.

**Os três lados:**

*Convenção única, `.expx/modulex/`.* A favor: um caminho só, trivial de
documentar, e o `.expx/` já existe como pasta de instalação do método no
projeto — é onde o marketplace das skills já aterrissa. Contra: amarra a skill
ao `npx expxdev init`. Quem instalou a skill à mão, como o próprio README
ensina, fica sem catálogo e sem explicação.

*Fetch da URL crua com cache.* A favor: sempre atualizado, e a M3 perde
urgência. Contra — e é decisivo: introduz rede na operação mais frequente da
skill. O README promete "sem rede, sem banco, sem chamada externa", e essa
promessa é o que torna a M0 barata. Uma consulta que depende do GitHub estar
no ar viola a regra 11 toda vez que a rede cai.

*Cadeia de resolução.* A favor: cobre os três modos de instalação que já
existem (variável para quem sabe o que faz, `.expx/` para quem usou o CLI,
caminho relativo para quem está dentro do próprio repositório do catálogo),
sem rede e sem caminho absoluto. Contra: quatro degraus é mais para explicar
do que um, e catálogo achado em degrau errado é um defeito novo, possível.

**Decisão: cadeia de resolução de quatro degraus,** na ordem
`$MODULEX_CATALOGO` → `.expx/modulex/docs/modulos/` → `docs/modulos/` →
nenhum. O primeiro que existir vence; os outros não são consultados.

**Por quê:** o contra da cadeia é mitigável e o dos outros dois não é. O
excesso de explicação se resolve num arquivo — `references/05-catalogo.md` — e
o risco de ler o catálogo errado se resolve com uma linha na saída: **toda
consulta declara de qual degrau leu e qual é o `atualizado_em`**. O contra da
convenção única (deixar sem catálogo quem instalou à mão) e o da rede (quebrar
a promessa de custo zero) atingem o mecanismo central.

**O que a cadeia não faz:** não funde catálogos. Dois `modulos.json` com o
mesmo `id` e conteúdos diferentes é exatamente a ambiguidade que a skill
existe para não criar. O primeiro degrau vence inteiro, e a existência do
segundo é reportada em uma linha, nunca mesclada.

**O quarto degrau é ausência, não `NAO EXISTE`.** "Não há catálogo alcançável"
e "há catálogo e ele não cobre isto" são respostas diferentes: a segunda
registra lacuna, a primeira não. Confundi-las polui o `LACUNAS.md` com
buscas que nunca chegaram a ser feitas, e o `LACUNAS.md` é o que decide a
próxima extração.

**O que revisa:** se aparecer um quarto modo de instalação — catálogo em
pacote publicado, por exemplo — ele entra como degrau, não substitui a cadeia.
Se a variável de ambiente se provar o degrau usado em 100% dos casos reais,
os degraus 2 e 3 viram legado e a cadeia encolhe.
