# M3 — Verificação

**O módulo envelhece.** API muda, limite muda, erro some, endpoint é descontinuado, plano de preço vira outro.

## Por que módulo desatualizado é pior que módulo nenhum

Sem módulo, o sprintx lê a documentação atual e acerta. Com módulo vencido, ele produz um **plano confiante e errado** — e o erro só aparece na execução, depois de o P4 já ter encolhido o escopo com base numa faixa de esforço que não vale mais, e de o cliente já ter recebido um prazo.

O custo do módulo vencido não é o retrabalho da integração. É o retrabalho mais a confiança gasta: o plano parecia sólido porque veio de algo que já funcionou.

Por isso a regra 8: **módulo com verificação vencida é sinalizado na consulta e não silencia.**

## O que torna um módulo obsoleto

Três coisas, em ordem de gravidade:

| # | Gatilho | Efeito |
|---|---------|--------|
| O1 | **Contrato mudou** — endpoint, autenticação, formato de payload, código de erro | o módulo produz plano errado. Obsoleto de fato, mesmo dentro do prazo |
| O2 | **Prazo vencido** — passou o intervalo de revalidação sem ninguém conferir | o módulo é *suspeito*, não necessariamente errado. Sinalizado na consulta |
| O3 | **Contradição de campo** — uma execução real encontrou algo diferente do que o módulo diz | obsoleto naquele campo. Corrige-se o campo, não o módulo inteiro |

O3 é o mais valioso e o mais perdido: quem está executando descobre a divergência e não a devolve. A F6 do sprintx e a E1 do runx devem disparar M3 quando isso acontecer — ver os patches.

## Prazos por tipo de campo

Nem tudo envelhece na mesma velocidade. Um único prazo para o módulo inteiro erra nos dois sentidos: revalida cedo demais o que não muda, e tarde demais o que muda toda semana.

| Campo | Prazo | Por quê |
|-------|-------|---------|
| Contrato da API (endpoints, auth, payload) | **6 meses** | é o que quebra o plano inteiro quando muda |
| Catálogo de erros | **6 meses** | acompanha o contrato |
| Pré-requisitos (conta, token, runtime, DNS) | **6 meses** | muda com política do fornecedor |
| Cadeia de falha e armadilhas | **12 meses** | envelhece devagar; a maioria é consequência do desenho, não da versão |
| Plano de fases e gates | **12 meses** | a ordem de construção muda pouco |
| Dependência de stack | **12 meses** | idem |
| Faixa de esforço | **12 meses** | é referência histórica; envelhece como referência, não como fato |
| Problema, sinônimos, fatias, o que cobre | **24 meses** | o problema do usuário quase não muda |

O `verificado_em` do frontmatter é a **data da verificação mais antiga entre os campos**. É o número que a consulta usa, e é conservador de propósito: um módulo cujo contrato tem sete meses está vencido, ainda que o resto esteja novo.

## O que revalidar, e como

Verificação **não** é reler o `MODULO.md`. É confrontá-lo com a realidade.

### 1. Contrato da API (seção 2 do contrato, e o que dela decorre)

Contra a documentação atual do fornecedor. Para cada endpoint listado no módulo:

- ainda existe, com o mesmo método e caminho?
- a autenticação mudou de forma ou de nome de header?
- o payload ganhou campo obrigatório novo?
- o fornecedor anunciou depreciação com data?

Confronto contra **documentação**, não contra memória. Se não houver acesso à documentação, a verificação **falha** e é registrada como tal — não se declara em dia o que não foi conferido.

### 2. Pré-requisitos

Conta ainda é criada do mesmo jeito? O plano gratuito ainda cobre o que o módulo assume? A versão de runtime exigida ainda é suportada?

### 3. Catálogo de erros

Códigos ainda existem? Apareceu código novo nos trabalhos que usaram o módulo desde a última verificação?

### 4. Faixa de esforço

Houve execução real nova? Se houve, a faixa se atualiza com a observação nova — **e não com a média**. Registre as duas observações com data; faixa é intervalo do que se viu, não estatística.

Se a faixa era `NAO DETERMINADO` e agora há uma execução cronometrada, este é o momento de preenchê-la. É assim que a lacuna do módulo zero fecha.

### 5. Lacunas

O que era lacuna virou documentado pelo fornecedor? Então sai da seção 13 e vira contrato. O que se descobriu na marra desde então entra.

## Roteiro

1. Leia o `MODULO.md` inteiro e a procedência.
2. Confronte na ordem acima, campo por campo, contra a fonte externa.
3. Registre **cada divergência** encontrada, com o que dizia e o que é hoje.
4. Corrija o `MODULO.md`. Correção de campo não reescreve o módulo.
5. Atualize `verificado_em` — de cada bloco e o do frontmatter.
6. Acrescente à seção 14 (procedência) uma linha do que foi verificado, contra o quê e quando.
7. Atualize `verificado_em` no `modulos.json`.
8. **Avise quem consumiu o módulo desde a última verificação**, se houve divergência de contrato. Ver abaixo.

## O aviso retroativo

Se a verificação encontrar divergência de contrato (O1), os trabalhos que injetaram este módulo desde a última verificação **planejaram com informação errada**.

Liste-os pelo `INDICE.md` do catálogo e pelo memox, e reporte:

```
Divergencia de contrato no modulo <id>.
<o que mudou>
Trabalhos que injetaram este modulo desde <data da ultima verificacao>:
  <trabalho> — <data> — <fase em que esta>
Quem estiver antes da F6 deve reinjetar; quem passou da F6 deve conferir <o campo>.
```

Sem isso, a verificação conserta o catálogo e deixa os planos errados de pé.

## Critério de saída

- Cada campo foi confrontado com a fonte externa, ou está registrado por que não pôde ser.
- Divergências estão registradas com o antes e o depois.
- `verificado_em` foi atualizado no `MODULO.md`, por bloco, e no `modulos.json`.
- A procedência (seção 14) ganhou a linha desta verificação.
- Se houve divergência de contrato: os consumidores desde a última verificação foram listados e avisados.

## Quando falha

| Falha | O que fazer |
|-------|-------------|
| Sem acesso à documentação do fornecedor | A verificação **falha**. Registre "não verificado por falta de acesso" e mantenha a data antiga. Nunca carimbe em dia o que não foi conferido |
| O fornecedor sumiu, foi comprado ou descontinuou o produto | Marque o módulo como `obsoleto` no `modulos.json`. Ele **não sai do catálogo**: a M0 continua achando e responde "existiu, está obsoleto, motivo". Apagar faz a próxima pessoa refazer a descoberta |
| A API mudou tanto que o plano não vale mais | Isto não é verificação, é extração nova. Marque o módulo como obsoleto, aponte para o substituto, e rode M2 sobre um trabalho recente |
| O módulo nunca teve `verificado_em` | Trate como vencido em todos os campos e verifique tudo. Sem procedência não há módulo (regra 5) |
| A divergência foi encontrada por quem estava executando (O3) | Corrija só aquele campo, com data nova só nele. Não custe uma verificação completa a quem só quis devolver um achado — atrito aqui mata a única fonte de O3 que existe |
