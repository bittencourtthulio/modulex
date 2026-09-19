# Integração — buildx

O buildx é a única camada que vê o **projeto inteiro** antes de existir uma linha de código. Isso o torna o melhor lugar da suíte para consultar o catálogo — e o pior lugar para deixá-lo decidir qualquer coisa.

Dois pontos de contato, um em cada ponta do processo, e um terceiro que parece ponto de contato e não é.

| Etapa | O que o modulex entrega | Autoridade |
|-------|------------------------|-----------|
| **B3** decomposição | quais features do `MAPA.md` já têm módulo pronto, e quais fatias | **Informação sobre o recorte, nunca o recorte** |
| **B5/B6** fechamento | quais features entregues deveriam virar módulo | Candidatura a extração — oferecida, nunca executada |
| **B2** fundação | nada. Ver "O B2 é o risco", abaixo | — |

## O módulo não recorta o projeto

O B3 é a única etapa que faz algo que nenhuma outra camada faz: quebrar um sistema em features. Ele decide por três testes — vertical, enunciável, demonstrável — e **esses continuam sendo os únicos que decidem onde cortar**.

A fatia de um módulo é **candidata** a fronteira, nunca a fronteira. As fatias do módulo zero conhecem o CRM de onde ele saiu; os três testes conhecem este projeto.

Ordem obrigatória dentro do B3: a consulta acontece com o `MAPA.md` **fechado** e **antes de gravado**. Fora dessa janela ela não serve — antes, decide o recorte; depois, não influencia nada.

Divergência entre a fatia e o recorte se **anota**, não se resolve refazendo o recorte. Se um módulo recortasse a feature, a casa de origem estaria decidindo a arquitetura da casa de destino — que é a regra 3 por outro caminho.

**A exceção, e ela é estreita:** quando uma fatia opcional cobre exatamente uma feature que o recorte tinha grudado em outra, e separá-las passa nos três testes, separe. Aí o módulo é evidência de que a fronteira existe, não razão para criá-la.

## O B2 é o risco

O ponto que mais tenta ser ponto de contato, e não é.

Projeto novo **não tem convenção ainda**. É justamente aí que a herança alheia entra sem resistência: não há `CONVENCOES.md` prévio para dizer não. O risco da regra 3 é **maior** no greenfield, não menor — e é o contrário do que a intuição diz.

A separação se mantém inteira:

| Do módulo | Vai para o B2? |
|-----------|----------------|
| stack **essencial** ao problema — o que quebra se mudar, porque o terceiro exige | entra como **restrição técnica a respeitar** |
| stack **herdada** do sistema de origem | **não entra.** Quem decide a stack é o stackx |

No módulo zero: "endpoint público alcançável pela internet, sem autenticação de sessão" é restrição — a Uazapi chama de fora e não há como contornar. Já `company_id`, o formato `{success, data}` e a UI em pt-BR são herança, e um projeto novo não herda nada disso por acidente.

## O conflito com a pergunta única

**O ponto mais delicado desta integração.**

O buildx faz exatamente uma pergunta, no começo. Depois dela, nenhuma outra chega ao usuário, em nenhum dos dois modos. O M1 do modulex, por desenho (D13), não injeta módulo inteiro por padrão: sem fatia indicada, ele **pergunta** quais fatias.

Os dois não podem estar certos ao mesmo tempo, e a resolução usa o mecanismo que o buildx já tem para a F2 do sprintx: **ele responde no lugar do humano e registra a premissa.**

1. A escolha de fatias se deriva do **escopo da feature no `MAPA.md`** — fonte legítima, porque saiu do `PROJETO.md` e das premissas.
2. A escolha vira premissa em `docs/projeto/PREMISSAS.md` **antes de ser usada**, com o que assume e o que a invalidaria.
3. Não sendo derivável qual fatia serve, a premissa é **só o núcleo obrigatório**.

**Injetar tudo é proibido**, e é a saída preguiçosa que esta seção existe para fechar. Injetar seis fatias numa feature que só quer receber e responder mensagem enche a base da F1 com IA, ligações e grupos que ninguém pediu — e a F3 planeja o que foi injetado. No modo autônomo ninguém percebe a tempo.

Errar para menos custa uma injeção a mais depois. Errar para mais custa um projeto inteiro planejado a mais. Por isso o padrão é o núcleo.

## O B4 não faz nada de novo

O B4 chama o sprintx, e o sprintx já sabe o que fazer: F1 ingere, F2 pauta, F3 rascunha, F5 audita, F6 copia sob TDD. Ver `references/integracao/sprintx.md`.

O buildx só passa adiante o `modulo_disponivel` e as `fatias_candidatas` da feature. **Não duplique a injeção aqui.** Sprintx sem o patch aplicado significa buildx seguindo sem módulo — e isso é correto, não é falha.

## A devolução é a metade que sempre falta

No fechamento, toda feature entregue que integrou um terceiro e tinha `modulo_disponivel: -` é candidata a extração.

**O buildx é o melhor gatilho de M2 da suíte inteira.** Ele acabou de construir um sistema com o plano, o QA e o relatório de entrega ainda na mão — que são exatamente as fontes das 14 seções do `MODULO.md`. Seis meses depois esse material está frio, e a extração custa dez vezes mais. É o lado "escrita" do indicador, e é o que impede o catálogo de apodrecer.

Dois limites:

- **Ofereça, não execute.** A extração publica conhecimento entre clientes e exige sanitização — nenhum token, domínio de cliente ou telefone real. Isso não roda em modo autônomo.
- **Feature nunca usada em produção não vira módulo**, vira exemplo. O que dá valor ao módulo são as cicatrizes, e código que não rodou não tem nenhuma. Registre a candidatura e diga que falta produção.

## O que não muda no buildx

- A pergunta única continua sendo **uma**.
- O recorte do B3 continua saindo dos três testes.
- O `CONVENCOES.md` continua sendo do stackx.
- Dois módulos para o mesmo problema entram como **dois candidatos**; a escolha é da F2/F3 dentro do B4 (regra 1).
- Sem modulex instalado, ou com catálogo não alcançável, o buildx decompõe como sempre decompôs.
