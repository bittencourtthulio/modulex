# Integração — runx

Um ponto de contato só: a **E1**, investigação. E ele tem uma regra que governa tudo.

## A cadeia de falha é hipótese, nunca causa

O módulo entrega à E1 a cadeia de falha (seção 11) e o catálogo de erros (seção 12). São **hipóteses de causa a comprovar** — nunca causa declarada (regra 10).

A E1 existe para comprovar a causa raiz. Uma lista de suspeitos que chega com ar de diagnóstico faz a E1 pular a prova, que é a única coisa que ela faz. O resultado é o pior defeito possível numa ocorrência: a causa plausível que não era a causa, consertada, com o problema voltando duas semanas depois.

O que o módulo dá é valioso e é outra coisa: **a ordem em que vale olhar.** Sete elos, e o defeito está em exatamente um. Saber por qual começar economiza a tarde; declarar qual é sem prova custa a semana.

## Formato da injeção

A M1 entrega para a E1 apenas as seções 11, 12 e — quando o sintoma for de configuração — a 6. **O plano de fases não desce**: a E1 investiga o que está quebrado, não constrói.

```
Hipoteses do modulo <id> — NAO SAO CAUSA COMPROVADA.
A E1 exige prova. Isto e a ordem em que vale olhar.

Elo <n>: <sintoma observavel>
  Checar: <a verificacao que isola o elo>
  Prova: <o que o resultado prova, e o que ele nao prova>
```

O campo **prova** é o que separa hipótese de diagnóstico. "Consulta retorna vazio sem erro" prova que o dado não chega ao usuário; **não** prova que falta permissão — pode não haver dado nenhum. É a checagem seguinte que separa os dois casos, e o módulo deve dizer qual é.

## Isolar o elo antes de ler código

A regra que o módulo zero declara e que a E1 já pratica: **isole o elo primeiro, leia o código do elo depois.**

Mudar código antes de localizar a quebra é como as tardes desaparecem. A cadeia dá o mapa; a E1 dá a prova de qual ponto do mapa é este defeito.

No módulo zero são sete elos, e o sintoma já particiona a busca: recebimento quebra nos elos 3 a 7; envio quebra nos elos 1, 2 ou na normalização de telefone. Metade da cadeia sai da mesa antes da primeira consulta.

## O catálogo de erros e o log

Quando o módulo publica `erros.json`, a E1 pode cruzar o código de erro do log direto com a tabela, sem ler prosa.

Duas cautelas:

- **código de erro do fornecedor não é causa.** Um 401 diz que a autenticação falhou; não diz se o header está errado, se o token expirou ou se a instância foi recriada do outro lado. O catálogo lista as três, e a E1 prova qual é.
- **erro ausente do catálogo não é erro impossível.** É erro novo — e vira gatilho de M3 (verificação) no fim da ocorrência.

## O que não muda no runx

- A E1 continua exigindo prova para declarar causa raiz. O módulo não afrouxa isso em nada.
- A E2 (plano) e a E3 (fix) não recebem injeção do modulex. O fix é deste sistema, sob TDD estrito.
- Sem módulo no catálogo, a E1 investiga como sempre investigou.

## Devolução: a E1 corrige o catálogo

Quando a investigação encontra uma armadilha que o módulo não listava, ou um erro fora do catálogo, isso volta para o módulo como gatilho O3 de verificação — corrigindo **aquele campo**, com data nova só nele.

Uma ocorrência real é a melhor fonte de seção 11 que existe: ela não descreve o que se imagina que quebre, mas o que quebrou.

Não custe uma verificação completa a quem só quis devolver um achado. Atrito aqui mata a única fonte de correção que vem de campo.
