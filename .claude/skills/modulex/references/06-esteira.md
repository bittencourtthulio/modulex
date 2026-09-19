# A esteira — como o catálogo cresce até a metade sozinho

A M2 é o estágio que fecha o ciclo, e é o que ninguém roda. Não por
preguiça: por custo. No fim de uma feature entregue, ninguém tem apetite
para preencher catorze seções à mão.

A esteira resolve a metade barata desse custo. Ela **não extrai módulo** —
ela enfileira candidato.

## Por que o hook não extrai

Um script rodando em segundo plano, sem humano, preenche os catorze campos
por inferência. É exatamente o que a regra dura proíbe: campo não
verificável vira `NAO DETERMINADO`, nunca chute.

E o dano é assimétrico. Um módulo com faixa de esforço inventada não é um
módulo meio certo: é um plano confiante e errado, porque o P4 do prodx
encolhe escopo com base naquele número. Catálogo cheio de módulo plausível
é pior que catálogo vazio — o vazio pelo menos faz o sprintx ler a
documentação atual e acertar.

A divisão de trabalho é limpa porque o script é bom exatamente onde o
humano é ruim, e vice-versa:

| O script observa sozinho | Só sai com julgamento |
|---|---|
| **8** faixa de esforço — datas reais de commit | **13** lacunas: o que se descobriu na marra |
| **10** inventário — arquivos, contagem, extensão | **4** o que **não** cobre |
| **7** pré-requisitos — variáveis de credencial | **6** essencial × herdado |
| **12** catálogo de erros — códigos tratados no código | **5** decisões de escopo, com alternativa descartada |
| **14** procedência — branch, commit, data | **1** o problema na linguagem de quem pede |
| | **2** sinônimos · **3** fatias · **9** plano · **11** cadeia de falha |

Repare no primeiro item da coluna esquerda: **a esteira fecha a D6.** O
módulo zero nasceu sem faixa de esforço porque ninguém cronometrou. Com a
esteira, esse passa a ser o campo mais confiável do arquivo — e é o campo
que mais governa o P4.

## O que a janela do git prova, e o que não prova

O detector lê o primeiro e o último commit do intervalo e devolve a
**janela de calendário**. Isso é observação real, e é tudo que é.

Não é horas trabalhadas. Não é estimativa. Uma feature com 7 dias de
calendário pode ter tido 4 horas de trabalho espalhadas, ou duas pessoas
em tempo integral. O candidato grava a janela e grava o aviso junto; a
faixa de esforço da seção 8 só nasce quando **um humano confirma** que a
janela corresponde ao trabalho.

Sem essa confirmação, continua `NAO DETERMINADO` — regra 6, sem exceção
por ser um número que veio de máquina. Número de máquina não é mais
verdadeiro que número de humano: é só mais fácil de acreditar.

## Os sinais de detecção

O detector pontua e nunca decide sozinho. O limiar padrão é 4.

| Sinal | Pontos | Por que conta |
|---|---|---|
| host externo no código | 3 | é o sinal mais forte: alguém está falando com fora |
| SDK de terceiro **no manifesto de dependências** | 2 | `package.json`, `requirements.txt`, `go.mod` |
| variável de ambiente de credencial | 2 | `*_API_KEY`, `*_TOKEN`, `*_WEBHOOK_SECRET` |
| rota de webhook **em arquivo de código** | 2 | terceiro que chama de volta |
| código de erro HTTP tratado explicitamente | 1 | 429, 402, 401 tratados = contrato de terceiro conhecido |

Duas restrições que parecem detalhe e não são:

- **SDK só conta no manifesto.** Um `.json` de dados que cita `uazapi` não
  integra nada. A primeira versão deste detector acusou o próprio
  repositório da skill — que fala de WhatsApp o tempo todo e não integra
  coisa nenhuma.
- **Host e webhook só contam em arquivo de código.** Documentação cita host
  em toda página.

Falso positivo aqui não é inofensivo: enche a fila de candidato que ninguém
vai promover, e fila cheia de lixo é fila que ninguém abre.

## A fila é local e nunca é publicada

`.expx/modulex/fila/<slug>.json`

Este arquivo carrega nome de branch, caminho de arquivo e host — coisas que
podem identificar o cliente. Ele fica no projeto, como evidência para quem
vai extrair, e **não sobe para lugar nenhum**.

O que sobe é o `MODULO.md` que a M2 produz, e só ele passa pelo gate. A
distinção entre "evidência local" e "conhecimento publicável" é a mesma que
separa anonimizar de extrair (D19).

## O fluxo inteiro

```
feature entregue
      │
      ▼  hook, em segundo plano
detectar_candidato.py ──► .expx/modulex/fila/<slug>.json     local, mecânico
      │
      ▼  /modulex-extrair <slug>
M2: humano responde o que o script não sabe                   julgamento
      │
      ├─ julgamento completo ────► status: ativo
      └─ julgamento em aberto ───► status: candidato
      │
      ▼  gate local, antes de qualquer push
publicar.py ──► PR no catálogo ──► CI ──► merge ──► reindexação
```

## Candidato não é módulo

`status: candidato` é um módulo com as seções mecânicas preenchidas e pelo
menos uma seção de julgamento ainda em `NAO DETERMINADO`.

| | candidato | ativo |
|---|---|---|
| aparece na M0 | sim, **marcado como candidato** | sim |
| entra na F1 do sprintx | sim, como contexto | sim |
| vira rascunho de sprint na F3 | **não** | sim |
| tem artefato copiado na F6 | **não** | sim |
| conta no indicador de escrita | sim | sim |

Por que aparece na busca se não pode ser usado: porque a informação mais
valiosa de um candidato é que **ele existe**. "Alguém aqui já integrou
isso, e o registro está pela metade" muda o plano — manda conversar com
quem fez, em vez de começar do zero. O que ele não pode é virar plano.

O validador cobra isso: `status: ativo` com seção de julgamento em
`NAO DETERMINADO` reprova. As seções 7, 8, 9, 10 e 12 aceitam
`NAO DETERMINADO` e continuam ativas — são as que o contrato já declarava
como opcionais em valor.

## Ligar o hook

O detector roda ao fim do trabalho. Em Claude Code, no `settings.json` do
projeto:

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "python3 .expx/modulex/scripts/detectar_candidato.py --quieto"
          }
        ]
      }
    ]
  }
}
```

`--quieto` é obrigatório: um hook que fala quando não tem nada a dizer é um
hook que alguém desliga na segunda semana. O script também sai com código 0
mesmo quando falha — hook que quebra atrapalha quem está entregando, e a
regra 11 vale aqui também.

Quem prefere sem hook roda à mão, ou deixa para a mergex chamar no
fechamento do trabalho: `docs/integracao/patch-sprintx.md`.

## Critério de saída

- O candidato tem as seções mecânicas preenchidas com o que foi **observado**.
- Toda seção de julgamento está explicitamente em `NAO DETERMINADO`.
- A janela do git está gravada com o aviso de que é calendário, não esforço.
- A fila está no projeto e **não** foi publicada.
- O próximo passo está escrito no próprio arquivo: `/modulex-extrair <slug>`.
