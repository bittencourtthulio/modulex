# M2 — Extração

O caminho inverso, e **o que faz o catálogo crescer**. Uma feature terminada que integrou um terceiro vira módulo novo.

Este é o estágio que fecha o ciclo. Sem ele o catálogo só é lido, envelhece e apodrece — a metade do indicador que fica em zero.

## Quando extrair

| Extraia | Não extraia |
|---------|-------------|
| A feature integrou um terceiro (API externa, provedor, serviço) | A feature é regra de negócio da casa |
| O problema se repete entre clientes | O problema é de um cliente só |
| A entrega passou pelo QA e está em produção | A entrega ainda não rodou de verdade |
| Existe plano, relatório e erros registrados | Não existe artefato nenhum do trabalho |

Feature entregue e nunca usada em produção não vira módulo: vira exemplo. O que dá valor ao módulo são as cicatrizes, e código que não rodou não tem nenhuma.

## De onde sai cada seção

A extração é, sobretudo, **costura de artefatos que já existem**. Quase nada é escrito do zero; o que for escrito do zero é suspeito.

| Seção do `MODULO.md` | Fonte | Se a fonte não existe |
|----------------------|-------|-----------------------|
| 1 problema que resolve | `BRIEFING.md` do prodx (o problema, não a solução), ou a descrição da feature na F1 | pergunte ao humano em uma frase de quem pede |
| 2 sinônimos e termos de busca | o pedido original, o relatório de uso do runx/mergex, o nome do fornecedor | escreva os que souber e marque a seção como fraca |
| 3 fatias | os sprints do plano da F3, agrupados pelo que pode ser não instalado | sem plano, `NAO DETERMINADO` — módulo sem fatias declara fatia única |
| 4 o que cobre / NÃO cobre | escopo e **não-objetivos** do plano; o que a F2 decidiu deixar de fora | o "não cobre" **nunca** fica vazio (regra 7): sem fonte, liste o que você sabe que não foi feito |
| 5 decisões de escopo fechadas | `00-DECISOES.md` do trabalho, com alternativa descartada e motivo | as decisões visíveis no código, marcadas como inferidas |
| 6 dependência de stack | `docs/stack/CONVENCOES.md` do projeto de origem + o que o terceiro exige | separe pelo teste da seção seguinte |
| 7 pré-requisitos | `.env.example`, o que o gate da primeira fase exigiu, o que travou a execução | o que a execução real precisou e não tinha |
| 8 faixa de esforço | relatório de entrega do mergex, datas de abertura e fechamento no `INDICE.md` | **`NAO DETERMINADO`** (regra 6). Nunca estime |
| 9 plano de fases com gates | o `ORQUESTRADOR.md` da feature, quase literal | sem plano, `NAO DETERMINADO` — e o módulo perde muito valor |
| 10 inventário de artefatos | `ENTREGA.md` do mergex: arquivos, contagem, caminho | liste do repositório, marcando o que rodou em produção |
| 11 cadeia de falha e armadilhas | ocorrências do runx sobre a feature, QA reprovado, `00-CAUSA-RAIZ.md` | o que quebrou durante a execução, se estiver registrado |
| 12 catálogo de erros | tratamento de erro no código + o que a documentação do fornecedor lista | os códigos que o código trata explicitamente |
| 13 lacunas | `00-LACUNAS.md` da base de conhecimento do trabalho | **o ativo mais caro**: sem lacunas registradas, pergunte ao humano o que ele descobriu na marra |
| 14 procedência | o próprio trabalho: sistema, data, versão da API na época | nunca `NAO DETERMINADO` — sem procedência não há módulo (regra 5) |

## O teste de essencial × herdado

A separação da seção 6 é o campo que mais exige julgamento e o que mais protege o projeto de destino. Para cada item da stack do sistema de origem, pergunte:

> **Se eu trocar isto, o terceiro para de funcionar?**

- **Sim** → essencial. Viaja.
- **Não, só o sistema de origem é que faz assim** → herdado. Tem que ser traduzido pelo stackx do destino.

Exemplos do módulo zero, para calibrar:

| Item | Teste | Coluna |
|------|-------|--------|
| webhook público, sem autenticação de sessão | a Uazapi chama de fora; com JWT ela recebe 401 | **essencial** |
| webhook sempre responde 2xx | não respondendo, o provedor reenfileira para sempre | **essencial** |
| JID de grupo preservado, sem `replace(/\D/g,"")` | é regra do WhatsApp, não da casa | **essencial** |
| dois headers de auth distintos (criar × operar) | é o contrato do fornecedor | **essencial** |
| coluna de tenancy chamada `company_id` | qualquer nome serve; é o CRM de origem que chama assim | **herdado** |
| resposta `{success, data}` | convenção da casa | **herdado** |
| UI em pt-BR | escolha de produto da origem | **herdado** |
| credenciais no banco em vez de secrets | **é decisão de escopo (seção 5)**, não dependência de stack: nasce de multi-tenant com contas distintas por cliente. Num projeto de tenant único, secrets servem | **decisão, pauta da F2** |

A última linha é a mais instrutiva: nem tudo que parece stack é stack. Quando o item é uma escolha que o projeto de destino pode refazer, ele pertence à seção 5 e vira pergunta da F2 — não à seção 6.

## Roteiro

1. **Identifique o trabalho de origem.** Slug da feature, PD-ID se houver, ocorrências relacionadas.
2. **Reúna os artefatos**: plano, orquestrador, decisões, lacunas, QA, entrega, relatório de uso, ocorrências posteriores.
3. **Preencha as 14 seções** pela tabela acima, na ordem, sem pular.
4. **Marque `NAO DETERMINADO` o que não tiver fonte** — e leve cada um para a seção 13. Regra dura: campo não verificável nunca é chutado.
5. **Aplique o teste de essencial × herdado** item a item na seção 6.
6. **Declare as fatias** e teste cada opcional: ela pode não ser instalada sem quebrar o núcleo? Se não pode, é núcleo.
7. **Escreva o "não cobre"** de cada fatia. É a seção que todo mundo esquece e a que evita descobrir o buraco no meio da execução.
8. **Grave o `MODULO.md`** na raiz do repositório do módulo.
9. **Acrescente ao catálogo**: linha no `INDICE.md`, entrada no `modulos.json`, e remova do `LACUNAS.md` a lacuna que este módulo fecha.

## O padrão de qualidade

O módulo zero — `whatsapp-uazapi-integration` — foi extraído exatamente assim, à mão, de um CRM em produção. Ele é a referência do que a extração deve produzir:

- três camadas de conhecimento (contrato da API, plano de execução, diagnóstico), não um resumo de documentação;
- um plano de sprints com gate por fase, no formato que o sprintx produz;
- artefatos que rodaram em produção, numerados na ordem de aplicação;
- decisões de escopo declaradas numa tabela, com o valor escolhido;
- convenções invioláveis herdadas, listadas e nomeadas como herdadas;
- uma cadeia de falha com o sintoma observável de cada elo.

E ele nasce com uma lacuna real: **não declara faixa de esforço**. Isso é o comportamento correto — `NAO DETERMINADO` e entrada nas lacunas, nunca chute. Ver `DECISOES-DA-SKILL.md`, D6.

## Critério de saída

- As 14 seções existem. Nenhuma foi omitida — as sem fonte estão como `NAO DETERMINADO`.
- Todo `NAO DETERMINADO` tem entrada correspondente na seção 13.
- A seção 4 tem "não cobre" preenchido em **todas** as fatias (regra 7).
- A seção 6 está em duas colunas, e cada item passou pelo teste.
- A seção 14 nomeia sistema de origem, data e versão da API (regra 5).
- Toda fatia opcional passou no teste de "pode não ser instalada".
- O catálogo foi atualizado nos três arquivos.

## Quando falha

| Falha | O que fazer |
|-------|-------------|
| O trabalho não tem plano nem relatório | Extraia do código e do histórico, marcando as seções 8 e 9 como `NAO DETERMINADO`. Um módulo só com contrato de API e armadilhas ainda vale — só vale menos |
| Não dá para saber quanto tempo levou | `NAO DETERMINADO` na seção 8. **Nunca estime** (regra 6): o prodx encolhe escopo com base nesse número |
| A feature integrou dois terceiros | Dois módulos, não um. O critério é o problema resolvido; dois fornecedores para problemas distintos são dois problemas |
| Já existe módulo para este problema | Não crie o segundo por reflexo. Compare: se é o mesmo problema com outro fornecedor, pode ser um módulo com duas implementações, ou dois módulos irmãos com o mesmo `problema` e sinônimos cruzados. Decida com o humano e registre |
| O código tem credencial, domínio ou telefone real | Sanitize **antes** de gravar. O módulo é conhecimento compartilhado entre projetos: um segredo que entra aqui vaza para todos os destinos |
| O humano não lembra o que descobriu na marra | Pergunte pelo que deu errado, não pelo que aprendeu. "O que quebrou e demorou para achar?" produz a seção 13; "quais foram as lições?" produz vazio |
