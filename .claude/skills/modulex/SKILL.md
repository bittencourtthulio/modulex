---
name: modulex
description: Use quando o trabalho envolver integrar um terceiro — WhatsApp, pagamento, storage, email transacional, nota fiscal, assinatura digital, mensageria — ou qualquer problema que outro projeto desta casa provavelmente já resolveu. Responde "existe módulo pronto para isso?" a partir de um catálogo de problemas resolvidos, carrega o conhecimento do módulo na base de um trabalho em andamento, e transforma feature entregue em módulo novo. Não escolhe fornecedor, não implementa e não estima: carrega conhecimento pronto para que o sprintx e o runx não redescubram a mesma integração a cada projeto. Use mesmo sem a palavra modulex, módulo ou catálogo por nome.
---

# modulex

A camada de conhecimento de módulos do ecossistema Expx: o lugar onde o que já foi resolvido volta antes de ser redescoberto.

## Princípio central

**O módulo é um problema resolvido, não uma biblioteca.**

A chave de busca é o problema na linguagem de quem pede — "atender cliente por WhatsApp", "cobrar cartão recorrente", "emitir nota fiscal de serviço" — nunca o nome do fornecedor. Buscar por `uazapi` tem que funcionar; buscar por `whatsapp` e por `inbox de atendimento` tem que funcionar **melhor**.

Um módulo pode ter mais de uma implementação possível. Quem escolhe entre elas é o sprintx, com o stackx na mesa. Nunca o modulex, nunca o prodx.

## O problema que o modulex resolve

Toda vez que um projeto precisa de WhatsApp, pagamento, storage, email transacional ou nota fiscal, o sprintx repete a mesma descoberta: ler a documentação da API, descobrir os limites, tropeçar nos mesmos erros conhecidos, e produzir um plano que já foi produzido antes em outro cliente.

Isso queima tokens, queima tempo e produz planos de qualidade desigual — o terceiro projeto que integra a mesma coisa costuma sair **pior** que o primeiro, porque o conhecimento não voltou.

O modulex é o lugar onde esse conhecimento volta.

## O indicador de que está funcionando

**A proporção de features com integração de terceiro que consultaram um módulo antes de planejar, cruzada com quantas delas viraram módulo novo depois.** São dois números, e cada um sozinho mente:

| Leitura | Escrita | Diagnóstico |
|---------|---------|-------------|
| alta | alta | o ciclo está fechado — é o estado saudável |
| alta | zero | o catálogo **apodrece**: é lido, envelhece e ninguém devolve o que aprendeu. Vira enfeite em seis meses |
| zero | alta | a **consulta está cara demais**. Alguém se dá ao trabalho de extrair, mas ninguém consulta antes de planejar |
| zero | zero | a skill não pegou. O problema é anterior à skill |

O `/modulex` reporta os dois números a partir do `INDICE.md` e do `LACUNAS.md`.

Por que este indicador e não "quantos módulos existem": contagem de módulos é vaidade. Um catálogo de quarenta módulos que ninguém consulta custa manutenção e não devolve nada. O que importa é o **ciclo**, e ciclo tem duas metades.

## O que o modulex NÃO faz

- **Não escolhe fornecedor.** Ele mostra o que existe com plano pronto e o que cada um cobre. A escolha é do sprintx (F2 descoberta / F3 sprints), com o stackx.
- **Não implementa.** Quem executa é a F6 do sprintx ou a E3 do runx, sob TDD.
- **Não estima.** Ele carrega a faixa de esforço observada como *referência histórica*; a estimativa é a F3.5 do sprintx e depende do plano real.
- **Não decide se vale a pena.** Isso é prodx.
- **Não impõe stack.** A dependência de stack do módulo é informação. O stackx do projeto de destino tem precedência sobre a convenção do sistema de origem.
- **Não vira dependência.** A ausência do modulex nunca bloqueia nenhuma outra skill — exatamente como a ausência do memox e do legadox não bloqueia o prodx.

## Plano e artefato: a distinção que sustenta o contrato

O módulo carrega dois tipos de conteúdo com regras opostas. Confundi-los é o erro mais caro que se pode cometer com esta skill.

| Tipo | O que é | Como o sprintx trata |
|------|---------|---------------------|
| **Plano** | sprints, fases, gates, ordem de execução | **rascunho a adaptar** — a F3 continua produzindo o plano real deste projeto |
| **Artefato** | migration, edge function, componente que já rodou em produção | **copiar preferencialmente a reescrever** — reescrever perde correções invisíveis |

A regra que sobra: **o plano se adapta, o artefato se copia — e as duas coisas passam pela F5 do sprintx e pelo TDD da F6.**

Um artefato copiado sem teste continua sendo dívida. Copiar não é atalho para o TDD: é atalho para a redescoberta.

**Quando a origem é privada, a segunda metade não existe — e isso é declarado, nunca deduzido.** O modulex extrai a estrutura de que precisa e a cataloga no próprio catálogo; o módulo passa a se bastar e não exige acesso nenhum à origem. Não há artefato para copiar: a F6 constrói a partir do plano e das armadilhas, sob TDD. O que o módulo poupa é a **descoberta**, que é a maior parte do custo. Um módulo assim vale menos que um com artefato e muito mais que nada — o que ele não pode é deixar isso implícito, porque plano que conta com cópia que nunca chega descobre o buraco na metade da execução (D20).

O módulo zero diz isso com todas as letras: *"Copy the packaged artifacts. Never retype an edge function or a migration from memory — they carry fixes for real failure modes that are invisible in a rewrite."* Um `CREATE TABLE` reescrito à mão perde o `GRANT` que existe porque, sem ele, o frontend recebe array vazio sem erro. Essa correção não está visível no código: está na cicatriz.

## Essencial e herdado: a separação que impede a injeção de casa alheia

**Este é o maior risco do modulex inteiro.** Um módulo carrega stack e arquitetura acopladas ao sistema de onde foi extraído. Injetá-las inteiras num projeto que já tem as suas é colocar a casa de origem dentro da casa de destino.

Todo módulo separa, em duas colunas, a dependência de stack:

| Coluna | Definição | Viaja? |
|--------|-----------|--------|
| **Essencial ao problema** | O que quebra se for mudado, porque o terceiro exige | **Sim.** Vale em qualquer projeto |
| **Herdado do sistema de origem** | O que é escolha da casa onde o módulo nasceu | **Não.** Tem que ser traduzido pelo stackx do destino |

No módulo zero: o webhook precisa ser público porque a Uazapi chama de fora — **essencial**. A formatação de telefone precisa preservar o JID de grupo — **essencial**, é regra do WhatsApp. Já o nome `company_id`, o formato de resposta `{success, data}` e a UI em pt-BR são **herança**: o projeto de destino pode chamar de `org_id`, responder em outro formato e falar outro idioma sem que nada quebre.

Módulo que não faz essa separação é módulo que injeta convenção alheia. Campo obrigatório do `MODULO.md`, e o principal ponto do `patch-stackx.md`.

## As três injeções — permissões diferentes em cada ponto

O coração do desenho. O modulex entrega coisas diferentes para cada skill, com **autoridade diferente**. A mesma informação que é rascunho no sprintx é sinal no prodx e hipótese no runx.

| Skill | Etapa | O que o modulex entrega | Autoridade |
|-------|-------|------------------------|-----------|
| **prodx** | P3 existência | Um sexto lugar de busca: "existe módulo pronto para este problema?" | **Sinal apenas.** Não é evidência de que o sistema já faz — é evidência de que fazer é mais barato do que parece |
| **prodx** | P4 avaliação | A faixa de esforço de referência e as **fatias** do módulo, para dimensionar o escopo mínimo | **Sinal apenas.** Encolhe o escopo; não nomeia tecnologia no briefing |
| **prodx** | P5 briefing | Uma linha `modulo_disponivel: <id>` na seção de contexto | **Contexto, nunca instrução** — mesma regra da seção "Contexto para quem vai investigar". A regra 10 do prodx continua valendo |
| **sprintx** | F1 ingestão | O `MODULO.md` inteiro entra na base de conhecimento da feature | Insumo de primeira classe |
| **sprintx** | F2 descoberta | As decisões de escopo já fechadas do módulo viram **perguntas** ao usuário, não respostas | Pauta de entrevista |
| **sprintx** | F3 sprints | As fases e gates do plano pronto viram **rascunho** de sprints e tasks | Rascunho a adaptar. As fases do módulo não conhecem este projeto |
| **sprintx** | F5 auditoria | As convenções invioláveis e as armadilhas do módulo viram itens de auditoria | Critério de prontidão |
| **sprintx** | F6 execução | Os artefatos de produção do módulo | Copiar preferencialmente a reescrever — **sempre sob o TDD da F6** |
| **runx** | E1 investigação | A cadeia de falha e o catálogo de erros do módulo | Hipótese de causa a **comprovar**, nunca causa declarada. A E1 exige prova |
| **buildx** | B3 decomposição | Quais features do `MAPA.md` já têm módulo pronto, e com quais fatias | **Informação sobre o recorte, nunca o recorte.** Os três testes do B3 continuam decidindo onde cortar |
| **buildx** | B5/B6 fechamento | Quais features entregues deveriam virar módulo | Candidatura a extração — oferecida, nunca executada |
| **stackx** | consulta | A dependência de stack do módulo, separada entre essencial e herdada | O stackx do projeto **tem precedência**. Conflito é reportado, não resolvido pelo modulex |

Detalhamento por skill em `references/integracao/`.

O buildx tem uma particularidade que nenhuma outra tem: ele faz **uma única pergunta** ao humano, no começo, e o M1 por desenho **pergunta** quais fatias injetar (D13). Os dois não podem estar certos ao mesmo tempo. A resolução é o buildx responder no lugar do humano, derivando a escolha do `MAPA.md` e registrando-a como premissa antes de usar — e, não sendo derivável, injetar **só o núcleo**. Injetar tudo é proibido: errar para menos custa uma injeção a mais depois; errar para mais custa um projeto inteiro planejado a mais.

### Por que a autoridade muda de ponto para ponto

No prodx, um módulo pronto **não** é evidência de que o sistema já faz aquilo. Se fosse tratado como tal, o P3 devolveria `ja_existe` para uma funcionalidade que ninguém instalou — e o cliente recebe um texto dizendo onde encontrar uma tela que não existe. O que o módulo prova é outra coisa: que o custo é menor do que parece, o que muda o P4, não o P3.

No runx, a cadeia de falha do módulo é a melhor lista de suspeitos que existe — e continua sendo suspeita. A E1 exige prova, e "o módulo diz que costuma ser o GRANT" não é prova. É onde olhar primeiro.

## Estágios em resumo

| Estágio | Nome | Saída | Quando roda |
|---------|------|-------|-------------|
| M0 | Consulta | resposta no chat | a operação mais frequente |
| M1 | Injeção | base de conhecimento do trabalho em curso | dentro da F1 do sprintx ou da E1 do runx |
| M2 | Extração | `MODULO.md` novo + linha no `INDICE.md` | feature entregue que integrou um terceiro |
| M3 | Verificação | `MODULO.md` revalidado, `verificado_em` novo | por vencimento ou sob suspeita |

A **esteira** atravessa a M2: um hook enfileira candidato ao fim do trabalho, e a M2 promove. Ela não é um estágio novo — é a M2 com a metade mecânica já preenchida.

### M0 — Consulta

**A operação mais frequente e a que precisa ser barata.** Dado um problema em linguagem natural, existe módulo, e que fatias ele tem? Responde no chat, **não gera arquivo**.

É o que o P3 do prodx e a F1 do sprintx chamam. Se esta operação for cara, ninguém usa e a skill morre — mesmo raciocínio da triagem do prodx.

A consulta lê `modulos.json` e nada mais. Ela **nunca** clona repositório de módulo, nunca abre artefato, nunca lê o `MODULO.md` inteiro. O módulo zero sozinho tem mais de 150 arquivos de artefato: clonar isso para responder "existe módulo?" é o tipo de custo que faz uma skill ser contornada no primeiro dia.

Roteiro: `references/00-consulta.md`.

### M1 — Injeção

Carregar o `MODULO.md` — e **só as fatias escolhidas** — na base de conhecimento de um trabalho em andamento, no formato que o sprintx (F1) ou o runx (E1) espera receber.

É aqui, e só aqui, que o peso é puxado. A injeção sabe o destino e filtra: o runx recebe cadeia de falha e catálogo de erros, não o plano de sprints.

Roteiro: `references/01-injecao.md`.

### M2 — Extração

O caminho inverso, e **o que faz o catálogo crescer**. Uma feature terminada que integrou um terceiro vira módulo novo, a partir dos artefatos que o sprintx e o mergex já produziram: plano, QA, relatório de uso, erros encontrados.

Este é o estágio que fecha o ciclo. Sem ele o catálogo só é lido, envelhece e apodrece.

O módulo zero foi extraído exatamente assim, à mão, de um CRM em produção — é a referência de qualidade do que a extração deve produzir.

Roteiro: `references/02-extracao.md`.

### M3 — Verificação

**O módulo envelhece.** API muda, limite muda, erro some, endpoint é descontinuado.

Módulo desatualizado é **pior que módulo nenhum**: sem módulo, o sprintx lê a documentação atual e acerta. Com módulo vencido, ele produz um plano confiante e errado, e o erro só aparece na execução — depois de o P4 já ter encolhido o escopo com base numa faixa de esforço que não vale mais.

Roteiro: `references/03-verificacao.md`.

## A esteira: o catálogo cresce até a metade sozinho

A M2 é o estágio que fecha o ciclo e é o que ninguém roda — não por preguiça, por custo. No fim de uma entrega, ninguém tem apetite para preencher catorze seções à mão.

A esteira resolve a metade barata desse custo. Ela **não extrai módulo: enfileira candidato** (D17).

| O script observa sozinho | Só sai com julgamento |
|---|---|
| **8** faixa de esforço — datas reais de commit | **13** lacunas: o que se descobriu na marra |
| **10** inventário — arquivos e contagem | **4** o que **não** cobre |
| **7** pré-requisitos — variáveis de credencial | **6** essencial × herdado |
| **12** erros — códigos tratados no código | **5** decisões, com alternativa descartada |
| **14** procedência — branch, commit, data | **1** o problema na linguagem de quem pede |

A divisão é limpa porque o script é bom exatamente onde o humano é ruim. E ela fecha a D6: o módulo zero nasceu sem faixa de esforço porque ninguém cronometrou — com a esteira, esse passa a ser o campo mais confiável do arquivo.

**O que a janela do git prova é calendário, não esforço.** O candidato grava a janela com o aviso junto; a seção 8 só nasce quando um humano confirma que a janela corresponde ao trabalho. Número de máquina não é mais verdadeiro que número de humano — é só mais fácil de acreditar.

**Candidato não é módulo.** Ele aparece na M0 marcado, porque a informação mais valiosa dele é que *ele existe*: manda conversar com quem fez, em vez de começar do zero. O que ele não pode é virar rascunho de sprint na F3 nem artefato copiado na F6 (regra 13).

Roteiro: `references/06-esteira.md`.

## O catálogo em nuvem é o GitHub, e nada além dele

Sem servidor, sem banco, sem serviço próprio (D16). Em três pontos o primitivo nativo é melhor que a alternativa construída:

- **o PR é o gate** — a aprovação humana deixa de ser fluxo inventado e vira o caminho de menor resistência;
- **a issue é a lacuna global**, com 👍 como voto: em meses, a fila de extração está priorizada por demanda real e pública;
- **o Actions é o cron do vencimento**, que transforma "alguém devia revisar" em issue com nome e prazo.

O contrato passa a ser **teste que reprova merge**, não norma escrita. A rede acontece num lugar só — `scripts/sincronizar.py` deixa o catálogo no degrau 2, e a M0 continua offline.

**O que se perde, declarado:** telemetria de leitura agregada. A escrita se mede pelos PRs; a leitura, não.

Roteiro: `references/07-publicacao.md`.

## Fatias

Um módulo grande é fatiável, e a fatia é **decisão de produto, não técnica**. "WhatsApp completo" e "só receber e responder mensagem" são escopos mínimos de tamanhos muito diferentes, e essa distinção governa o P4 do prodx.

Todo módulo declara um **núcleo obrigatório** e **extensões opcionais**, cada uma com seu próprio "o que cobre / o que não cobre" e sua própria faixa de esforço.

No módulo zero: núcleo (instâncias, webhook, mensageria, inbox) × organização, IA, ligações, grupos e notificações. Quem quer "atender cliente por WhatsApp" precisa do núcleo; quem quer "atendimento com fila e etiqueta" precisa do núcleo mais organização.

Uma fatia opcional só existe se puder ser **não instalada sem quebrar o núcleo**. Se não puder, ela é núcleo — e declarar como opcional é mentira que a F3 descobre no meio da execução.

## O contrato: o que é um módulo

Um módulo é um repositório que atende ao contrato do modulex. O contrato é um arquivo **`MODULO.md` na raiz do repositório do módulo**, com frontmatter **expx-schema v1** (chaves em `snake_case` sem acento, enums minúsculos sem acento, datas em ISO, chave nunca omitida), `kind: modulo`, um `namespace` e um `status` entre `ativo`, `candidato` e `obsoleto`.

E ele é **testado**: `scripts/validar_modulo.py` cobra as 14 seções, o `não cobre` não-vazio, as duas colunas da seção 6, a contrapartida de todo `NAO DETERMINADO` na seção 13, e a coerência do `status`. Roda na máquina e no PR.

As catorze seções obrigatórias:

| # | Seção | Por que existe |
|---|-------|----------------|
| 1 | Problema que resolve | Na linguagem de quem pede. É o campo que o P3 do prodx e a F1 do sprintx procuram |
| 2 | Sinônimos e termos de busca | Português e inglês, o nome do fornecedor, e os termos que a casa usa. Sem isso a busca falha como falha a busca de existência do prodx por nomenclatura |
| 3 | Fatias | Núcleo obrigatório e extensões opcionais, cada uma com seu recorte |
| 4 | O que cobre e o que NÃO cobre | Por fatia. A segunda metade é a mais valiosa e a que todo mundo esquece |
| 5 | Decisões de escopo já fechadas | Marcadas como **pauta da F2**, jamais como fato consumado |
| 6 | Dependência de stack | Em duas colunas: essencial ao problema × herdada do sistema de origem |
| 7 | Pré-requisitos | Conta, token, runtime, DNS. Cada um marcado como bloqueante ou não |
| 8 | Faixa de esforço observada | Por fatia, com data e origem. Referência histórica, **não estimativa** |
| 9 | Plano de fases com gates | Rascunho da F3, critério da F5 |
| 10 | Inventário de artefatos | O que existe de código pronto, quantos e onde, com a marca de "rodou em produção" ou "exemplo" |
| 11 | Cadeia de falha e armadilhas | Os elos onde quebra e o sintoma observável de cada um. Hipótese da E1 |
| 12 | Catálogo de erros | Códigos, causa, retry. Preferencialmente também em formato máquina |
| 13 | Lacunas | O que a documentação oficial não responde e foi descoberto na marra |
| 14 | Procedência | De onde veio, de qual sistema, quando foi verificado, contra qual versão da API |

**Regra dura:** campo não verificável vira `NAO DETERMINADO` e entra nas lacunas — mesma regra 11 do prodx. Módulo com faixa de esforço inventada é **pior** que módulo inexistente, porque o prodx encolhe escopo com base em ficção.

Template: `assets/TEMPLATE-MODULO.md`. Contrato detalhado, campo a campo: `references/04-contrato.md`.

### A seção 13 é o ativo

As lacunas — o que a documentação oficial não responde e foi descoberto na marra — são o que justifica o módulo existir. Documentação de API qualquer um lê. Que o webhook precisa responder 2xx mesmo para payload ignorado, sob pena de o provedor reenfileirar para sempre, ninguém lê: descobre-se depois de uma tarde perdida.

Um `MODULO.md` com a seção 13 vazia é um resumo de documentação, e resumo de documentação não vale o custo de manter.

## Estrutura em disco

O catálogo vive num **repositório irmão do ecossistema**, e não em cada projeto:

```
docs/modulos/
  INDICE.md              índice legível, uma linha por módulo, append-only
  modulos.json           índice máquina: id, namespace, problema, sinonimos,
                         fatias, repo, stacks, esforco, verificado_em
  LACUNAS.md             o que o catálogo não cobre e já foi pedido
  mod/<ns>/<id>/         espelho derivado do MODULO.md, para a M1 ler de uma vez
scripts/                 gate, validador, detector, busca, reindexação, publicação
.github/workflows/       o contrato como teste: valida no PR, reindexa, cobra M3
```

E, no projeto que consome:

```
.expx/modulex/docs/modulos/   cópia sincronizada do catálogo (degrau 2)
.expx/modulex/fila/<slug>.json  candidatos detectados — LOCAL, nunca publicado
```

E, em cada repositório de módulo:

```
MODULO.md                o contrato
```

O projeto consome o catálogo sem cloná-lo inteiro: a consulta lê apenas `modulos.json`, e só a injeção puxa o `MODULO.md` do módulo escolhido. Nenhum artefato desce antes da F6.

### Como o projeto encontra o catálogo

Estando dentro de um projeto qualquer, o endereço se resolve por uma **cadeia de quatro degraus**. Pare no primeiro que existir:

| # | Onde | Quando |
|---|------|--------|
| 1 | `$MODULEX_CATALOGO/modulos.json` | override explícito de quem sabe o que está fazendo |
| 2 | `.expx/modulex/docs/modulos/modulos.json` | instalado no projeto pelo `npx expxdev init` |
| 3 | `docs/modulos/modulos.json` | você está dentro do próprio repositório do catálogo |
| 4 | nenhum | **catálogo não alcançável** — segue sem módulo, regra 11 |

Sem rede e sem caminho absoluto. O primeiro degrau vence inteiro: catálogo **não se funde**, porque dois `modulos.json` com o mesmo `id` e conteúdos diferentes é a ambiguidade que a skill existe para não criar.

Duas consequências que não são óbvias:

- **Toda saída da M0 declara de qual degrau leu e qual é o `atualizado_em`.** Custa uma linha e torna visível o defeito mais provável da skill depois de instalada: consultar catálogo velho sem saber.
- **Catálogo não alcançável não é `NAO EXISTE`.** A primeira não registra lacuna, a segunda registra. Confundi-las polui o arquivo que decide a próxima extração com buscas que nunca foram feitas.

Roteiro completo: `references/05-catalogo.md`. O raciocínio, com os lados descartados, está em `DECISOES-DA-SKILL.md` (D1 e D15).

## Máquina de estados

| Situação | Próximo passo |
|----------|---------------|
| nenhum degrau da cadeia resolve | M0 responde **catálogo não alcançável**, diz como ligar, e segue. Não registra lacuna e não cria `docs/modulos/` no projeto |
| catálogo alcançável e vazio | M0 responde "catálogo vazio" e oferece a extração. Nunca bloqueia |
| pergunta é "existe módulo para X?" | M0 consulta |
| M0 achou, e há trabalho em curso | M1 injeção, com as fatias escolhidas |
| M0 achou, mas `verificado_em` vencido | M0 responde **com o aviso de vencimento**, e oferece M3 |
| M0 não achou | registra a busca em `LACUNAS.md` e responde que não há. Nunca inventa módulo |
| M1 pedida sem fatia indicada | pergunta quais fatias; não injeta o módulo inteiro por padrão |
| feature entregue integrou terceiro, sem módulo no catálogo | M2 extração |
| M2 pedida sem plano, QA ou relatório do trabalho | extrai o que dá, e o que faltar vira `NAO DETERMINADO` nas lacunas |
| módulo com `verificado_em` vencido | M3 verificação |
| M3 encontrou divergência com a API atual | atualiza o `MODULO.md`, registra na procedência, e avisa quem consumiu o módulo desde a última verificação |
| hook detectou integração de terceiro no fim do trabalho | enfileira **candidato** em `.expx/modulex/fila/`. Não extrai, não publica, não fala se não achou nada |
| M2 rodada sobre candidato com julgamento ainda aberto | grava `status: candidato`. Ele é buscável e marcado, e não entra na F3 nem na F6 |
| publicação pedida e o gate acusou segredo | **para**. Gira o segredo primeiro; nada é enviado. Push protection no remoto é tarde demais |
| catálogo do GitHub fora do ar na sincronização | a cópia local anterior continua valendo. Rede fora nunca bloqueia (regra 11) |
| colisão de `id` entre catálogo privado e público | resolve por `<namespace>/<id>`, com o privado vencendo, e **declara** qual venceu (D18) |

- **Vários módulos possíveis para a mesma busca**: lista com fatias e esforço, e **não escolhe** — a escolha é do sprintx.
- **Estágio adiantado**: a skill explica o que falta e executa o pendente.

## As 13 regras invioláveis

1. **O modulex não escolhe fornecedor nem decide se usa; ele informa.** Módulo no catálogo é oferta, não recomendação. Quando há mais de um, lista os dois e cala.
2. **O plano do módulo é rascunho a adaptar; o artefato de produção é para copiar, não reescrever** — e ambos passam pela auditoria da F5 e pelo TDD da F6. Artefato copiado sem teste é dívida, não atalho.
3. **Convenção herdada do sistema de origem nunca sobrepõe o stackx do projeto de destino.** Conflito é reportado ao humano, nunca resolvido pelo modulex.
4. **Decisão de escopo fechada no módulo entra como pauta da F2, nunca como fato consumado.** O módulo decidiu para a casa dele.
5. **Nada entra num `MODULO.md` sem procedência e data de verificação.** De onde veio, de qual sistema, contra qual versão da API.
6. **Faixa de esforço é referência histórica, jamais estimativa.** Sem observação real, é `NAO DETERMINADO`. Estimar é a F3.5 do sprintx.
7. **O que o módulo não cobre é campo obrigatório e nunca fica vazio.** Módulo que não declara suas bordas produz plano que descobre o buraco na metade da execução.
8. **Módulo com verificação vencida é sinalizado na consulta e não silencia.** Módulo desatualizado é pior que módulo nenhum: produz plano confiante e errado.
9. **No prodx o módulo é sinal, nunca decisão técnica no briefing.** A regra 10 do prodx continua valendo integralmente.
10. **No runx a armadilha é hipótese, nunca causa comprovada.** A E1 exige prova; o catálogo de erros diz onde olhar primeiro.
11. **A ausência do modulex nunca bloqueia nenhuma outra skill.** Sem catálogo, o sprintx planeja do zero como sempre planejou.
12. **O que sobe para o catálogo é extraído, nunca raspado — e nada sobe sem aprovação humana.** Anonimizar falha aberto: o que o padrão não pegou vai junto, e em repositório público não volta. Extrair falha fechado: o que não está no schema nunca foi copiado. O gate de segredo é a última linha, não a primeira, e roda na máquina **antes** do push.
13. **Candidato não é módulo.** Módulo com seção de julgamento em aberto é buscável e marcado, e não vira rascunho de sprint na F3 nem artefato copiado na F6. Hook enfileira; quem extrai é a M2, com humano.

Regra transversal: caminhos sempre relativos. Nenhum caminho absoluto em artefato ou saída.

## Integração com as outras skills

| Skill | Relação |
|-------|---------|
| **prodx** | P3 ganha um sexto lugar de busca; P4 usa fatias e faixa de esforço para dimensionar escopo mínimo; P5 leva `modulo_disponivel` como contexto |
| **sprintx** | O consumidor principal: F1 ingere, F2 pauta, F3 rascunha, F5 audita, F6 copia sob TDD |
| **runx** | E1 usa cadeia de falha e catálogo de erros como hipótese a comprovar |
| **stackx** | Consulta cruzada: a dependência de stack do módulo × as convenções detectadas. O stackx local vence |
| **memox** | Indexa os módulos junto com os demais artefatos, e é o que faz a consulta do M0 melhorar com o tempo |
| **buildx** | B3 consulta o catálogo com o `MAPA.md` fechado e antes de gravado; B5/B6 devolve as features que deveriam virar módulo. É o melhor gatilho de extração da suíte |
| **mergex** | O relatório de entrega é insumo da M2: é dele que sai a faixa de esforço observada |

**A ausência de qualquer uma nunca bloqueia o modulex** — e a ausência do modulex nunca bloqueia nenhuma delas.

## References e templates

| Arquivo | Conteúdo |
|---------|----------|
| `references/00-consulta.md` | M0: como buscar barato, ordem de busca, formato da resposta, o aviso de vencimento |
| `references/01-injecao.md` | M1: o que cada destino recebe, filtro por fatia, formato de entrega |
| `references/02-extracao.md` | M2: de quais artefatos sai cada seção do `MODULO.md`, e o que fazer com o que falta |
| `references/03-verificacao.md` | M3: o que torna um módulo obsoleto, prazos por tipo de campo, o que revalidar |
| `references/04-contrato.md` | O contrato campo a campo, com o que é obrigatório e o que aceita `NAO DETERMINADO` |
| `references/05-catalogo.md` | A cadeia de resolução do endereço do catálogo, a fusão por namespace, e o degrau que não bloqueia |
| `references/06-esteira.md` | A esteira: detecção, fila de candidatos, e a fronteira entre o que o script observa e o que exige julgamento |
| `references/07-publicacao.md` | O catálogo no GitHub: o PR como gate, o CI, a issue de lacuna, e o que se perde |
| `references/integracao/prodx.md` | P3, P4 e P5 — sinal, fatias, e o campo do briefing |
| `references/integracao/sprintx.md` | F1, F2, F3, F5 e F6 — as cinco injeções e suas autoridades |
| `references/integracao/runx.md` | E1 — cadeia de falha como hipótese |
| `references/integracao/stackx.md` | Essencial × herdado, e por que o stackx local vence |
| `references/integracao/buildx.md` | B3 e B5/B6 — o recorte que o módulo não faz, e o conflito com a pergunta única |
| `references/integracao/memox.md` | O que é indexado e como a consulta melhora com o tempo |
| `assets/TEMPLATE-MODULO.md` | Template do `MODULO.md` — as 14 seções |
| `assets/TEMPLATE-INDICE.md` | Template do `INDICE.md` do catálogo |
| `assets/TEMPLATE-modulos.json.md` | Formato do índice máquina |
| `assets/TEMPLATE-LACUNAS.md` | Template do `LACUNAS.md` do catálogo |

## Comandos

| Comando | Função |
|---------|--------|
| `/modulex` | Roteador: estado do catálogo, quantos módulos, quantos com verificação vencida, e as lacunas mais pedidas |
| `/modulex-buscar` | M0: consulta por problema em linguagem natural, via `$ARGUMENTS` |
| `/modulex-injetar` | M1: carrega um módulo (ou fatias dele) na base de conhecimento do trabalho atual |
| `/modulex-extrair` | M2: cria um módulo novo a partir de uma feature já entregue |
| `/modulex-verificar` | M3: revalida um módulo contra a realidade atual da API |
| `/modulex-publicar` | Publica um `MODULO.md` no catálogo, via PR, com o gate rodando antes do push |
| `/modulex-sincronizar` | Puxa o catálogo do GitHub para a cópia local (degrau 2). É o único ponto que toca a rede |

## Ambiguidades

Decisões tomadas na ausência de informação estão registradas em `DECISOES-DA-SKILL.md`.
