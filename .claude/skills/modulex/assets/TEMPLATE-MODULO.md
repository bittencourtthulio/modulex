---
kind: modulo
schema: expx-schema-v1
id: <slug-do-modulo>
problema: <uma linha, na linguagem de quem pede>
fornecedores: [<nome>]
fatias: [nucleo, <slug-da-fatia>]
repo: <url do repositorio do modulo>
stack_essencial: [<item>]
stack_herdada: [<item>]
esforco: <faixa | NAO DETERMINADO>
verificado_em: <AAAA-MM-DD>
verificado_contra: <versao da API | "documentacao publica em AAAA-MM-DD">
extraido_de: <sistema de origem>
status: <ativo | obsoleto>
---

# <nome do modulo>

<Uma frase dizendo o que este modulo entrega. Na linguagem de quem pede.>

## 1. Problema que resolve

<Uma frase, na linguagem de quem pede — nao na do fornecedor.
Certo: "atender cliente por WhatsApp, com caixa de entrada compartilhada".
Errado: "integracao com a API da Uazapi".
E o campo que o P3 do prodx e a F1 do sprintx procuram.
Obrigatorio. Nao aceita NAO DETERMINADO.>

## 2. Sinonimos e termos de busca

<Portugues e ingles, o nome do fornecedor, e os termos que a casa usa.
Seja generoso: o custo de um sinonimo a mais e uma linha; o de um a menos
e um NAO EXISTE falso e um plano construido do zero por cima de um modulo
que estava la.>

**Portugues:** <termo, termo>
**Ingles:** <termo, termo>
**Fornecedores:** <nome, nome>
**Termos da casa:** <termo, termo>

## 3. Fatias

<Nucleo obrigatorio e extensoes opcionais. Teste de toda fatia opcional:
ela pode NAO ser instalada sem quebrar o nucleo? Se nao pode, e nucleo.
Modulo pequeno declara fatia unica chamada nucleo — nao e defeito.>

| Fatia | Tipo | Resolve |
|-------|------|---------|
| `nucleo` | obrigatoria | <o problema que a fatia sozinha resolve> |
| `<slug>` | opcional | <idem> |

## 4. O que cobre e o que NAO cobre

<Por fatia. A segunda metade e a mais valiosa e a que todo mundo esquece.
O "nao cobre" NUNCA fica vazio (regra 7). Nomeie a origem de cada borda:
ficou fora do escopo da origem · o fornecedor nao faz · depende do destino.>

### `nucleo`

**Cobre:**
- <item>

**NAO cobre:**
- <item> — <origem da borda>

### `<slug da fatia opcional>`

**Cobre:**
- <item>

**NAO cobre:**
- <item> — <origem da borda>

## 5. Decisoes de escopo ja fechadas

<As escolhas que o modulo tomou e que o projeto de destino pode querer rever.
PAUTA DA F2 do sprintx, jamais fato consumado (regra 4).
O modulo decidiu para a casa dele.>

| Decisao | Valor na origem | O que muda no destino se for diferente |
|---------|-----------------|---------------------------------------|
| <decisao> | <valor> | <consequencia> |

## 6. Dependencia de stack

<Teste, item a item: se eu trocar isto, o terceiro para de funcionar?
Sim = essencial, viaja. Nao = herdada, o stackx do destino traduz.
Coluna herdada vazia e suspeita.
Cuidado com o que parece stack mas e DECISAO: se o destino pode
legitimamente escolher outro caminho, o item pertence a secao 5.>

### Essencial ao problema — viaja

| Item | Por que o terceiro exige |
|------|--------------------------|
| <item> | <motivo> |

### Herdada do sistema de origem — traduzir pelo stackx do destino

| Item | O que e na origem | Equivalente no destino |
|------|-------------------|------------------------|
| <item> | <valor na origem> | <a preencher pelo projeto> |

## 7. Pre-requisitos

<Conta no fornecedor, token, versao de runtime, DNS, dominio publico.
Bloqueante = sem isso o gate da fase nao passa; o plano para ali.>

| Pre-requisito | Bloqueante | Observacao |
|---------------|-----------|------------|
| <item> | <sim \| nao> | <como se obtem> |

## 8. Faixa de esforco observada

> **Isto nao e estimativa.** E referencia historica: quanto custou, uma vez,
> num projeto, com uma equipe. A estimativa deste projeto e a F3.5 do sprintx,
> e depende do plano real.

| Fatia | Faixa | Data da observacao | Origem |
|-------|-------|--------------------|--------|
| `nucleo` | <faixa \| NAO DETERMINADO> | <AAAA-MM-DD> | <execucao cronometrada \| relatorio do mergex \| -> |

<Sem observacao real: NAO DETERMINADO, e a lacuna vai para a secao 13
(regra 6). Fonte invalida: sensacao, analogia com outro modulo.>

## 9. Plano de fases com gates

<O caminho de implementacao e o criterio de aceite de cada fase.
RASCUNHO da F3 do sprintx e criterio da F5.
Um gate e resultado observavel, nao intencao.>

| Fase | Entrega | Gate |
|------|---------|------|
| <n> | <o que constroi> | <resultado observavel> |

**Ordem e dependencias:** <o que nao pode ser invertido, e por que.>

## 10. Inventario de artefatos

<A marca decide a regra 2: o que rodou em producao se copia — carrega
correcoes invisiveis. Exemplo se le e se reescreve sob TDD.
Nunca marque como producao o que voce nao sabe que rodou.>

| Artefato | Quantos | Onde | Marca |
|----------|---------|------|-------|
| <tipo> | <n> | <caminho relativo no repo do modulo> | <rodou em producao \| exemplo> |

## 11. Cadeia de falha e armadilhas conhecidas

<Os elos onde quebra e o sintoma observavel de cada um.
HIPOTESE da E1 do runx, nunca causa comprovada (regra 10).
O sintoma e a chave de busca: quem abre ocorrencia nao diz "o elo 6 falhou".
O campo prova e o que separa hipotese de diagnostico.>

| Elo | Sintoma observavel | Como isolar | O que o resultado prova |
|-----|--------------------|-------------|-------------------------|
| <n> | <o que o usuario ve> | <a verificacao> | <e o que NAO prova> |

**Armadilhas:**

- <armadilha> — <sintoma> — <por que acontece>

## 12. Catalogo de erros

<Codigos, causa, e se tem retry. Codigo de erro do fornecedor NAO e causa.
Preferencialmente tambem em formato maquina: erros.json no repo do modulo.>

| Codigo | Significa | Causas possiveis | Retry |
|--------|-----------|------------------|-------|
| <codigo> | <o que o fornecedor diz> | <as causas reais, mais de uma> | <sim \| nao \| com backoff> |

## 13. Lacunas

<O que a documentacao oficial nao responde e foi descoberto na marra.
E O ATIVO MAIS CARO DO MODULO e o que justifica ele existir.
Entra aqui tambem todo NAO DETERMINADO das outras secoes, com o que
seria preciso para preenche-lo.
Secao vazia = o modulo e um resumo de documentacao.>

| Lacuna | O que se sabe | O que falta para fechar |
|--------|---------------|-------------------------|
| <o que a doc nao responde> | <o que se descobriu na pratica> | <o que resolveria> |

## 14. Procedencia

<De onde veio o conhecimento, de qual sistema, quando foi verificado, e
contra qual versao da API. Append-only: cada verificacao acrescenta linha.
Obrigatorio, nao aceita NAO DETERMINADO (regra 5) — conhecimento sem
origem declarada e indistinguivel de invencao.>

**Extraido de:** <sistema de origem, e em que estado estava — producao?>
**Extraido em:** <AAAA-MM-DD>
**Extraido por:** <quem>

| Data | O que foi verificado | Contra o que | Resultado |
|------|----------------------|--------------|-----------|
| <AAAA-MM-DD> | <campos> | <versao da API \| doc publica> | <sem divergencia \| divergencias corrigidas> |
