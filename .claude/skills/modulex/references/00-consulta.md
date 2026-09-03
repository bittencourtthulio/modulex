# M0 — Consulta

**A operação mais frequente da skill e a que decide se ela sobrevive.**

Dado um problema em linguagem natural, existe módulo pronto, e que fatias ele tem? Responde **no chat**, não gera arquivo.

É o que o P3 do prodx e a F1 do sprintx chamam. Se esta operação for cara, ninguém usa e a skill vira enfeite — mesmo raciocínio da triagem do prodx.

## A regra de custo

A consulta lê **`docs/modulos/modulos.json` e nada mais.**

Ela nunca:

- clona repositório de módulo;
- abre artefato (migration, função, componente);
- lê o `MODULO.md` inteiro;
- chama a API do fornecedor para conferir alguma coisa.

O módulo zero sozinho tem mais de 150 arquivos de artefato. Clonar isso para responder "existe módulo?" é o custo que faz uma ferramenta ser contornada no primeiro dia. **Só a M1 puxa peso**, e só depois de alguém decidir usar o módulo.

Se o `modulos.json` não tiver o que a consulta precisa responder, o defeito está no índice, não na consulta: corrija o índice na M2 ou na M3.

## O que você está procurando

**O problema, não o fornecedor.**

Quem pergunta chega com uma frase de quem pede: "o cliente quer atender pelo WhatsApp", "precisamos cobrar assinatura mensal", "tem que emitir NFS-e". Quem escreveu o módulo pode ter indexado por `uazapi`, `stripe`, `focusnfe`. É a seção 2 do `MODULO.md` — sinônimos e termos de busca — que costura os dois lados, e é por isso que ela é obrigatória.

Buscar por `uazapi` tem que funcionar. Buscar por `whatsapp` e por `inbox de atendimento` tem que funcionar **melhor**.

## Ordem de busca

Começa barato e específico, termina amplo. Pare quando achar — mas registre todos os termos tentados.

| # | Onde | O que casa |
|---|------|-----------|
| 1 | `problema` de cada módulo no `modulos.json` | a frase do pedido, direta |
| 2 | `sinonimos` | pt e en, o nome do fornecedor, os termos da casa |
| 3 | `fatias[].problema` | a fatia resolve, mesmo que o módulo inteiro seja maior que o pedido |
| 4 | `LACUNAS.md` | já foi pedido antes e não existe — a resposta é "não, e já perguntaram" |
| 5 | índice do memox, por termo | módulo extraído mas ainda não indexado no catálogo; achado fraco, reporte como tal |

Termos a tentar sempre, em ordem: o substantivo do problema em português, o mesmo em inglês, o nome do fornecedor se o usuário citou um, e o nome do artefato de interface que o usuário mencionou (`inbox`, `checkout`, `webhook`).

## Os quatro desfechos

### EXISTE

Há módulo cujo problema (ou o de uma fatia) cobre o pedido, e a verificação está em dia.

Responda com: id, problema, fatias com o que cada uma cobre, faixa de esforço por fatia, stack essencial, e a data da última verificação.

**Não recomende.** Regra 1: o modulex informa; quem escolhe é o sprintx.

### EXISTE, VERIFICAÇÃO VENCIDA

Achou, mas `verificado_em` está fora do prazo de `references/03-verificacao.md`.

O aviso **não é rodapé**. Vai na primeira linha da resposta, antes do conteúdo do módulo:

```
ATENCAO: verificado pela ultima vez em <data> (<n> meses). Modulo desatualizado
produz plano confiante e errado. Rode /modulex-verificar antes de injetar.
```

Regra 8. Um módulo vencido que responde sem alarde é pior que um catálogo vazio.

### EXISTE PARCIAL

Há módulo cujo problema **encosta** no pedido sem cobri-lo: cobre uma parte, ou resolve o mesmo problema para outro canal/fornecedor.

Diga exatamente o que cobre e o que não cobre — a seção 4 do `MODULO.md` existe para isto. O sprintx decide se o pedaço que existe compensa.

### NAO EXISTE

Procurou nos cinco lugares, com sinônimos em pt e en, e não achou.

Duas obrigações:

1. **Registre a busca em `LACUNAS.md`**, com o termo procurado e a data. É assim que o catálogo aprende o que falta — e a lacuna mais pedida é a próxima extração a fazer.
2. **Não invente módulo.** Não sugira "o fornecedor X provavelmente serve". Isso é escolha de fornecedor, e é do sprintx (regra 1).

## Formato da resposta

Seco. A consulta é chamada dentro de outra etapa (P3, F1) e não pode dominar a saída dela.

```
Modulo — "<o que foi procurado>"
Termos: <termos e sinonimos tentados>
Desfecho: <EXISTE | EXISTE (VENCIDO) | EXISTE PARCIAL | NAO EXISTE>

<id do modulo> — <problema, uma linha>
Fatias:
  <fatia>  <nucleo|opcional>  <esforco>  <o que cobre, meia linha>
Stack essencial: <o que viaja>
Stack herdada: <o que o stackx do destino precisa traduzir>
Verificado em: <data> <(VENCIDO)>
Repo: <url>

Nao cobre: <as bordas relevantes ao que foi pedido>
```

Sem módulo:

```
Modulo — "<o que foi procurado>"
Termos: <termos tentados>
Desfecho: NAO EXISTE
Registrado em LACUNAS.md. Nenhum modulo pronto para este problema.
```

## Quem chamou muda o que se diz

A consulta é a mesma; o enquadramento não.

| Chamador | O que enfatizar | O que **não** dizer |
|----------|-----------------|---------------------|
| **P3 do prodx** | que o custo é menor do que parece | que o sistema já faz — não faz; ninguém instalou. Ver `references/integracao/prodx.md` |
| **P4 do prodx** | as fatias, para dimensionar o escopo mínimo | o nome do fornecedor como decisão |
| **F1 do sprintx** | tudo; é insumo de primeira classe | — |
| **E1 do runx** | a cadeia de falha e o catálogo de erros | qualquer coisa que soe como causa comprovada |

## Critério de saída

- Os termos tentados estão registrados na resposta, inclusive os que não acharam nada.
- O desfecho está declarado.
- Se vencido: o aviso está na primeira linha.
- Se `NAO EXISTE`: a busca entrou no `LACUNAS.md`.
- Nenhum repositório de módulo foi clonado e nenhum artefato foi aberto.

## Quando falha

| Falha | O que fazer |
|-------|-------------|
| Catálogo não existe | Responda "catálogo vazio, nenhum módulo registrado" e ofereça a M2. Nunca bloqueie o trabalho que chamou |
| `modulos.json` existe mas está desatualizado em relação aos `MODULO.md` | Reporte a divergência e responda pelo `modulos.json` mesmo assim. Reindexar é M2/M3, não é trabalho da consulta |
| Dois módulos resolvem o mesmo problema | Liste os dois com fatias e esforço lado a lado. **Não escolha** (regra 1). A escolha é da F2/F3 do sprintx, com o stackx |
| O módulo achado tem stack incompatível com o projeto | Reporte a incompatibilidade como informação, não como veto. Muita coisa porta; o que não porta é a UI. Quem decide é o sprintx com o stackx |
| Pedido vago demais para buscar | Peça o problema em uma frase de quem pede. Não busque por adivinhação: termo errado devolve `NAO EXISTE` falso, e falso negativo aqui custa um plano inteiro |
