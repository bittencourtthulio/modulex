# Integração — sprintx

O consumidor principal do modulex. **Cinco injeções, cinco autoridades diferentes.**

Este é o desenho inteiro em um lugar. Se as autoridades vazarem uma para a outra, o módulo deixa de ser conhecimento e vira ordem — e o projeto de destino passa a construir a casa de origem.

| Fase | O que recebe | Autoridade |
|------|--------------|-----------|
| **F1** ingestão | o `MODULO.md` inteiro, das fatias escolhidas | insumo de primeira classe |
| **F2** descoberta | as decisões de escopo já fechadas | **pauta de entrevista**, não respostas |
| **F3** sprints | as fases e gates do plano pronto | **rascunho** a adaptar |
| **F5** auditoria | convenções invioláveis e armadilhas | critério de prontidão |
| **F6** execução | os artefatos de produção | copiar preferencialmente a reescrever, **sob TDD** |

## F1 — ingestão

A F1 consulta o catálogo (M0) sempre que a feature envolver um terceiro, e injeta (M1) o que achar. O `MODULO.md` entra na base de conhecimento como fonte, com procedência.

**Só as fatias escolhidas.** Sem escolha declarada, a F1 pergunta. Injetar o módulo inteiro faz a F3 planejar o que foi injetado, e ninguém pediu IA nem gestão de grupos.

O que a F1 ganha e não teria de outro jeito: as bordas do problema (seção 4), as armadilhas de quem já fez (11), e o que a documentação oficial não responde (13).

## F2 — descoberta: decisão fechada vira pergunta

**A injeção mais delicada.** O módulo carrega decisões que alguém tomou, para outra casa, com outras restrições. Elas entram na F2 como **pauta de entrevista**, jamais como resposta (regra 4).

O módulo zero fechou: só Uazapi, multi-tenancy por `company_id` com RLS, credenciais no banco e não em secrets, webhook público único. Cada uma dessas vira uma pergunta:

| Decisão do módulo | Pergunta da F2 |
|-------------------|----------------|
| só um fornecedor | vamos ficar preso a este fornecedor, ou precisamos de troca? |
| credenciais no banco | temos contas distintas por cliente, ou uma conta só? |
| tenancy por coluna dedicada | como este projeto separa clientes, se separa? |
| webhook público único | temos domínio público disponível para receber chamada de fora? |

Note que a resposta certa **muda com o projeto**. Credencial no banco é obrigatória em multi-tenant com contas distintas e é complicação desnecessária em tenant único. O módulo não sabe qual é o caso aqui — quem sabe é o usuário, e é a F2 que pergunta.

Decisão herdada por omissão é o modo silencioso de a casa de origem se instalar na casa de destino. A F2 é a última barreira antes de isso virar plano.

## F3 — sprints: o plano é rascunho

As fases e gates do módulo viram **rascunho** de sprints e tasks. Rascunho quer dizer: ponto de partida com valor real, que ainda assim não conhece este projeto.

O que o rascunho traz de bom:

- a **ordem** de construção, que costuma ser não óbvia e cara de descobrir (fundação → conexão → mensageria → o resto);
- os **gates**, que são resultados observáveis já testados em campo;
- as dependências entre fases, que o código não mostra.

O que o rascunho não sabe:

- o que este projeto já tem pronto (metade da fundação pode existir);
- as convenções deste repositório (isso é o stackx);
- o escopo mínimo aprovado no veredito do prodx;
- o que a F2 acabou de decidir diferente.

Regra prática: **a F3 pode reordenar, fundir, cortar e renomear fases do módulo. Não pode cortar gate sem substituir por outro.** O gate é a parte do plano que veio da cicatriz, não da opinião.

## F5 — auditoria: as convenções viram itens verificáveis

As convenções invioláveis e as armadilhas do módulo entram na auditoria como critério de prontidão.

Do módulo zero saem itens como: toda tabela nova tem GRANT; nenhuma policy faz subquery direta em tabela com RLS; toda tabela que a interface acompanha ao vivo está publicada no realtime; o webhook responde 2xx em todo caminho, inclusive no ignorado.

Três perguntas que a F5 faz com o módulo na mão:

1. Cada convenção inviolável do módulo virou item verificável no plano, **ou** está justificada como não aplicável a este projeto?
2. Cada elo da cadeia de falha tem gate que o cobre?
3. Cada decisão de escopo fechada do módulo foi **decidida neste projeto na F2**, ou entrou por omissão?

A terceira pega mais coisa que as outras duas juntas.

**Cuidado:** convenção **herdada** do sistema de origem não vira item de auditoria se o stackx do destino diz outra coisa (regra 3). Auditar contra a convenção da casa alheia é reprovar um plano por não parecer com outro projeto.

## F6 — execução: artefato se copia, sob TDD

Os artefatos de produção do módulo são para **copiar preferencialmente a reescrever** (regra 2).

O motivo está escrito no próprio módulo zero: *"Never retype an edge function or a migration from memory — they carry fixes for real failure modes that are invisible in a rewrite."* Um `CREATE TABLE` reescrito à mão perde o `GRANT` que existe porque, sem ele, o frontend recebe array vazio sem erro. A correção não está visível no código: está na cicatriz.

**E copiar não dispensa o TDD.** A F6 continua sendo a F6:

- o teste vem antes, e ele testa **o comportamento neste projeto**, não o artefato;
- o artefato copiado que não passa no teste é adaptado, não aceito;
- artefato copiado sem teste é **dívida**, não atalho.

O que se adapta ao copiar, e só isso: nomes de tenancy, caminhos de import, os pontos de adaptação declarados pelo módulo. Melhoria de gosto não entra — se der vontade de melhorar a função, anote e siga.

Artefato marcado como **exemplo** no inventário (e não como "rodou em produção") não tem esse privilégio: lê-se e escreve-se do zero, sob TDD, como qualquer código novo.

## O que não muda no sprintx

- Sem módulo no catálogo, todas as seis fases funcionam como sempre funcionaram.
- O TDD da F6 não é relaxado por nada.
- O veredito do prodx continua tendo precedência sobre o escopo do módulo: o módulo cobre mais coisa do que foi aprovado, e o que foi aprovado é o que se faz.
- A estimativa continua sendo da F3.5, sobre o plano real — nunca a faixa do módulo.

## Devolução: a F6 alimenta o catálogo

Duas obrigações no fim do trabalho, e são elas que fecham o ciclo:

1. **Divergência encontrada** entre o módulo e a realidade (endpoint que mudou, erro novo, armadilha que não estava lá) → dispara M3 sobre aquele campo. É o gatilho O3 da verificação, e é a única fonte de correção que vem de quem estava com a mão na massa.
2. **Feature que integrou terceiro sem módulo no catálogo** → dispara M2 na entrega.

Sem essas duas, o catálogo só é lido, envelhece e apodrece.
