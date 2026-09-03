# M1 — Injeção

Carrega o `MODULO.md` — e **só as fatias escolhidas** — na base de conhecimento de um trabalho em andamento, no formato que o destino espera receber.

É aqui, e só aqui, que o peso é puxado. A M0 responde de graça; a M1 baixa o contrato.

## Pré-requisitos

- A M0 já rodou e devolveu um módulo. Injetar sem consultar é injetar às cegas.
- **As fatias estão escolhidas.** Sem indicação, pergunte — não injete o módulo inteiro por padrão. Injetar as seis sprints do módulo zero num projeto que só quer receber e responder mensagem enche a base de conhecimento com IA, ligações e grupos que ninguém pediu, e a F3 planeja o que foi injetado.
- Há trabalho em curso com destino identificado: F1 do sprintx, ou E1 do runx.

## O que desce e o que não desce

| Desce | Não desce |
|-------|-----------|
| `MODULO.md` do módulo | os artefatos (migrations, funções, componentes) |
| as fatias escolhidas, inteiras | as fatias não escolhidas |
| o inventário de artefatos (a lista, não os arquivos) | o repositório do módulo |

Os artefatos são referenciados pelo inventário e **só são copiados na F6**, quando a task correspondente for executada sob TDD. Baixar 150 arquivos na F1 é encher a base de conhecimento de código que ninguém vai ler antes da execução.

## O que cada destino recebe

A mesma informação com autoridade diferente. Este filtro é o desenho, não uma otimização.

### Destino F1 do sprintx — insumo de primeira classe

Recebe o `MODULO.md` inteiro, das fatias escolhidas, e ele entra na base de conhecimento da feature como fonte, com procedência.

Ao entregar, marque cada bloco com a autoridade que ele terá nas fases seguintes:

| Seção do módulo | Vai para | Como |
|-----------------|----------|------|
| 5 decisões de escopo já fechadas | **F2** | como **pauta de entrevista**, nunca como resposta |
| 9 plano de fases com gates | **F3** | como **rascunho** de sprints e tasks |
| 6 dependência de stack | **F3**, cruzada com o stackx | o stackx local vence |
| 11 cadeia de falha + 5 convenções invioláveis | **F5** | como itens de auditoria |
| 10 inventário de artefatos | **F6** | copiar preferencialmente a reescrever, sob TDD |
| 4 o que NÃO cobre | **F2 e F3** | fronteira do escopo; o que está fora aqui não vira task por acidente |

Sem essa marcação, a F3 trata o plano do módulo como plano aprovado e a F2 trata as decisões fechadas como decididas — que é exatamente o que as regras 2 e 4 proíbem.

### Destino E1 do runx — hipótese a comprovar

Recebe **apenas** as seções 11 (cadeia de falha e armadilhas) e 12 (catálogo de erros), mais a 6 (dependência de stack) quando o sintoma for de configuração.

**Não** recebe o plano de fases: a E1 investiga o que está quebrado, não constrói. Injetar o plano ali é oferecer à investigação um roteiro de construção que não tem nada a ver com a pergunta.

Entregue com o rótulo de hipótese, explícito:

```
Hipoteses do modulo <id> — NAO SAO CAUSA COMPROVADA.
A E1 exige prova. Isto e a ordem em que vale olhar.

Elo <n>: <sintoma observavel> → <o que checar> → <o que o resultado prova>
```

Regra 10. Um catálogo de erros lido como diagnóstico faz a E1 pular a prova, que é a única coisa que a E1 existe para fazer.

### Destino F5 do sprintx — critério de prontidão

Quando a injeção é pedida já na auditoria (plano pronto, módulo descoberto depois), entregue só as seções 5, 11 e as convenções invioláveis, como checklist:

- cada convenção inviolável do módulo virou item verificável no plano, ou está justificada como não aplicável a este projeto?
- cada elo da cadeia de falha tem gate que o cobre?
- cada decisão de escopo fechada do módulo foi decidida **neste** projeto na F2, ou entrou por omissão?

A terceira é a que pega mais coisa. Decisão herdada por omissão é o modo silencioso de a casa de origem entrar na casa de destino.

## Tradução de stack, na entrega

A seção 6 nunca é entregue crua. Ela vem sempre em duas colunas, e a coluna herdada vem **marcada como pendente de tradução**:

```
Stack essencial (viaja):
  <item> — <por que o terceiro exige>

Stack herdada (traduzir pelo stackx do destino):
  <item do modulo> → <equivalente neste projeto, ou PENDENTE>
```

Se o projeto tem `docs/stack/CONVENCOES.md`, preencha a coluna da direita com o que o stackx já detectou e marque o que conflita. Se não tem, deixe `PENDENTE` e diga que o stackx não rodou — nunca preencha por analogia.

Conflito entre a convenção do módulo e a do projeto é **reportado, não resolvido** (regra 3). Ver `references/integracao/stackx.md`.

## Formato de entrega

Um bloco por seção injetada, cada um com:

1. o rótulo de autoridade (insumo / pauta / rascunho / critério / hipótese);
2. o conteúdo;
3. a procedência: `<id do modulo>, verificado em <data>`.

A procedência em cada bloco não é cerimônia: seis meses depois, quem lê a base de conhecimento precisa saber que aquele plano veio de fora e quando foi conferido pela última vez.

## Critério de saída

- As fatias injetadas são as escolhidas, e as demais não entraram.
- Cada bloco tem rótulo de autoridade e procedência.
- Nenhum artefato de código foi copiado (isso é F6).
- A seção 6 saiu em duas colunas, com a herdada marcada como pendente ou traduzida.
- Se o módulo estava vencido: o aviso foi repetido na injeção, não só na consulta.

## Quando falha

| Falha | O que fazer |
|-------|-------------|
| O `MODULO.md` não tem alguma seção obrigatória | Injete o que existe e reporte a seção faltante como lacuna do módulo. Não preencha por inferência |
| A fatia pedida não existe no módulo | Liste as fatias reais e pergunte. Não aproxime |
| O módulo está vencido e o usuário quer injetar mesmo assim | Injete, com o aviso repetido em cada bloco. É decisão do humano, e ela fica registrada |
| Dois módulos foram escolhidos para o mesmo problema | Injete os dois, marcados como alternativas. **Não funda** os planos: a escolha é da F2/F3 |
| Destino é a E1 e o usuário pediu o módulo inteiro | Entregue as seções 11, 12 e 6, e explique em uma linha por que o plano não desce. Se insistir, entregue — marcado como fora de uso na investigação |
| O projeto não tem `docs/stack/CONVENCOES.md` | Deixe a coluna traduzida como `PENDENTE` e sinalize que rodar o stackx antes da F3 evita retrabalho. Não bloqueie |
