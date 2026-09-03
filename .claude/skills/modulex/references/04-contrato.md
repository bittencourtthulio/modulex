# O contrato — o que é um módulo

Um módulo é um repositório que atende a este contrato. O contrato é um arquivo **`MODULO.md` na raiz do repositório do módulo**.

Repositório sem `MODULO.md` não é módulo: é repositório com código dentro. A diferença é o contrato, não o conteúdo.

## Frontmatter

Padrão **expx-schema v1**: chaves em `snake_case` sem acento, enums minúsculos sem acento, datas em ISO, **chave nunca omitida**.

```yaml
kind: modulo
schema: expx-schema-v1
id: <slug-do-modulo>
problema: <uma linha, na linguagem de quem pede>
fornecedores: [<nome>, ...]
fatias: [<slug-da-fatia>, ...]
repo: <url>
stack_essencial: [<item>, ...]
stack_herdada: [<item>, ...]
esforco: <faixa | NAO DETERMINADO>
verificado_em: <AAAA-MM-DD>
verificado_contra: <versao ou "documentacao publica em <data>">
extraido_de: <sistema de origem>
status: <ativo | obsoleto>
```

`status: obsoleto` mantém o módulo no catálogo. Módulo obsoleto continua sendo achado pela M0, que responde "existiu, está obsoleto, motivo" — apagar faz a próxima pessoa refazer a descoberta.

## As catorze seções

### 1. Problema que resolve

Uma frase, **na linguagem de quem pede**, não na do fornecedor.

- Certo: "atender cliente por WhatsApp, com caixa de entrada compartilhada".
- Errado: "integração com a API da Uazapi".

É o campo que o P3 do prodx e a F1 do sprintx procuram. Quem pede não sabe o nome do fornecedor; se soubesse, não estaria perguntando.

**Obrigatório. Não aceita `NAO DETERMINADO`** — módulo sem problema declarado não tem chave de busca, e um módulo que ninguém acha é um módulo que não existe.

### 2. Sinônimos e termos de busca

Português e inglês, o nome do fornecedor, e os termos que a casa usa.

Sem isso a busca falha exatamente como falha a busca de existência do prodx por nomenclatura: o código diz `payment_intent`, a tela diz "cobrança", o cliente diz "boleto".

Liste generosamente. O custo de um sinônimo a mais é uma linha; o de um sinônimo a menos é um `NAO EXISTE` falso, e um plano inteiro construído do zero por cima de um módulo que estava lá.

**Obrigatório.**

### 3. Fatias

Núcleo obrigatório e extensões opcionais, cada uma com seu próprio recorte.

**Teste de toda fatia opcional:** ela pode não ser instalada sem quebrar o núcleo? Se não pode, ela é núcleo — e declará-la opcional é mentira que a F3 descobre no meio da execução.

Módulo pequeno declara fatia única, chamada `nucleo`. Não é defeito.

**Obrigatório.**

### 4. O que cobre e o que NÃO cobre

Por fatia. **A segunda metade é a mais valiosa e a que todo mundo esquece.**

Módulo que não declara suas bordas produz plano que descobre o buraco na metade da execução — quando o custo de descobrir já é o dobro.

O "não cobre" tem três origens, e vale nomear qual:

| Origem | Exemplo |
|--------|---------|
| ficou fora do escopo da origem | "não faz disparo em massa" |
| o fornecedor não faz | "a Uazapi não é a API oficial da Meta" |
| depende do projeto de destino | "não traz o cadastro de cliente ao qual a conversa se liga" |

**Obrigatório em todas as fatias. Nunca fica vazio** (regra 7). Se você não sabe o que não cobre, você não conhece o módulo o suficiente para extraí-lo.

### 5. Decisões de escopo já fechadas

As escolhas que o módulo tomou e que o projeto de destino pode querer rever.

Formato de tabela: decisão, valor escolhido na origem, e o que mudaria no destino se fosse diferente.

Marcadas como **pauta da F2**, jamais como fato consumado (regra 4). O módulo decidiu para a casa dele: multi-tenant com contas distintas por cliente exige credencial no banco; tenant único não exige.

**Obrigatório.** Módulo sem decisões declaradas é módulo cujas decisões vão entrar por omissão — que é o modo silencioso de a casa de origem se instalar na casa de destino.

### 6. Dependência de stack, em duas colunas

**Essencial ao problema × herdada do sistema de origem.**

O teste, item a item: *se eu trocar isto, o terceiro para de funcionar?* Sim → essencial, viaja. Não → herdada, o stackx do destino traduz.

Cuidado com a terceira categoria: o que parece stack mas é **decisão**. Se o projeto de destino pode legitimamente escolher outro caminho, o item pertence à seção 5, não a esta. Ver `references/02-extracao.md`.

**Obrigatório, nas duas colunas.** Coluna herdada vazia é suspeita: quase todo módulo extraído carrega convenção da casa de origem, e não enxergá-la é o caminho mais curto para injetá-la.

### 7. Pré-requisitos

Conta no fornecedor, token, versão de runtime, DNS, domínio público, certificado — o que for.

Cada um marcado como **bloqueante** ou **não bloqueante**:

- **bloqueante**: sem isso o gate da fase não passa. O plano para ali, não contorna.
- **não bloqueante**: dá para construir e resolver depois.

A distinção importa para a F3: pré-requisito bloqueante é o que define até onde dá para ir sem o cliente providenciar alguma coisa.

**Obrigatório.**

### 8. Faixa de esforço observada

Por fatia, com **data e origem da observação**.

Escreva no próprio arquivo que **não é estimativa** — porque quem lê tende a usá-la como se fosse. É referência histórica: quanto custou, uma vez, num projeto, com uma equipe.

`NAO DETERMINADO` quando não houver observação real (regra 6). Módulo com faixa inventada é pior que módulo inexistente, porque o P4 do prodx encolhe escopo com base em ficção.

Fontes válidas: execução cronometrada, relatório de entrega do mergex, datas de abertura e fechamento no índice do projeto. Fonte inválida: sensação, analogia com outro módulo, "parece uma semana".

**Obrigatório como campo; aceita `NAO DETERMINADO` como valor** — e aí entra na seção 13.

### 9. Plano de fases com gates

O caminho de implementação e o **critério de aceite** de cada fase.

É o que a F3 do sprintx usa como rascunho e o que a F5 audita. Um gate é um resultado observável, não uma intenção: "a mensagem enviada chega num telefone real" é gate; "envio implementado" não é.

**Obrigatório; aceita `NAO DETERMINADO`** — mas um módulo sem plano perde a maior parte do seu valor, porque é justamente o plano que o sprintx não teria de outro jeito.

### 10. Inventário de artefatos

O que existe de código pronto, quantos e onde, com a marca de **"rodou em produção"** ou **"exemplo"**.

A marca é o que decide a regra 2. Artefato que rodou em produção se copia — carrega correções invisíveis. Exemplo se lê e se reescreve; não tem cicatriz nenhuma.

Nunca marque como produção o que você não sabe que rodou.

**Obrigatório. Módulo sem artefato declara inventário vazio** — e vale como conhecimento, não como código.

### 11. Cadeia de falha e armadilhas conhecidas

Os elos onde quebra e o **sintoma observável** de cada um.

É o que a E1 do runx consulta como hipótese (regra 10). Formato: elo, sintoma que o usuário vê, verificação que isola o elo, e o que o resultado prova.

O sintoma é a chave de busca. Quem abre uma ocorrência não diz "o elo 6 falhou": diz "a caixa de entrada não atualiza sozinha".

**Obrigatório.**

### 12. Catálogo de erros

Códigos, causa, e se tem retry. Preferencialmente também em **formato máquina** (`erros.json` no repositório do módulo), para que a E1 possa cruzar com o log sem ler prosa.

**Obrigatório; aceita `NAO DETERMINADO`** quando o fornecedor não publica códigos e nenhum foi observado.

### 13. Lacunas

O que a documentação oficial não responde e foi **descoberto na marra**.

**É o ativo mais caro do módulo e o que justifica ele existir.** Documentação de API qualquer um lê. Que o webhook precisa responder 2xx mesmo para payload ignorado, sob pena de o provedor reenfileirar para sempre, ninguém lê — descobre-se depois de uma tarde perdida.

Entra aqui também todo `NAO DETERMINADO` das outras seções, com o que seria preciso para preenchê-lo.

**Obrigatório.** Seção 13 vazia é sinal de que o módulo é um resumo de documentação — e resumo de documentação não vale o custo de manter.

### 14. Procedência

De onde veio o conhecimento, de qual sistema foi extraído, quando foi verificado pela última vez, e **contra qual versão da API**.

Cada verificação posterior acrescenta uma linha; a seção é append-only.

**Obrigatório. Não aceita `NAO DETERMINADO`** (regra 5): sem procedência não há módulo. Conhecimento sem origem declarada é indistinguível de invenção, e a diferença entre os dois é a única coisa que faz o catálogo confiável.

## A regra dura

Campo não verificável vira **`NAO DETERMINADO`** e entra nas lacunas — mesma regra 11 do prodx.

Não se chuta, não se estima por analogia, não se infere do que "costuma ser". As três seções que **não** aceitam `NAO DETERMINADO` são a 1 (problema), a 4 (o que não cobre) e a 14 (procedência): sem elas o módulo não é buscável, não é confiável e não é rastreável.

## Onde vive o contrato

| Arquivo | Onde | Quem escreve |
|---------|------|--------------|
| `MODULO.md` | raiz do repositório do módulo | a M2, revisada por humano |
| `erros.json` | repositório do módulo, opcional | a M2 |
| linha no `INDICE.md` | repositório do catálogo | a M2 |
| entrada no `modulos.json` | repositório do catálogo | a M2 e a M3 |

O `MODULO.md` mora **com o módulo**, não com o catálogo. O catálogo indexa; o módulo se descreve. Assim um módulo pode ser publicado, clonado ou compartilhado sem o catálogo junto — e continua se explicando.
