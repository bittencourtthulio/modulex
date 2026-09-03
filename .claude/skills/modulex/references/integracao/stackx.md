# Integração — stackx

**O ponto mais delicado do modulex inteiro.** É aqui que se decide se o módulo traz conhecimento ou se traz a casa de origem inteira junto.

## O risco, dito sem rodeio

Um módulo é extraído de um sistema real. Ele carrega, grudadas no conhecimento útil, as convenções da casa onde nasceu: como as tabelas são nomeadas, como o erro é sinalizado, em que idioma a interface fala, como o tenant é identificado.

Injetar isso inteiro num projeto que **já tem as suas** convenções é colocar a casa de origem dentro da casa de destino. O código sai funcionando e sai errado: errado no dialeto, que é exatamente o que o stackx existe para proteger.

E o modo como isso acontece quase nunca é uma decisão. É **omissão**: ninguém escolheu chamar a coluna de `company_id`, ela simplesmente veio junto.

## A separação obrigatória

Todo `MODULO.md` declara a dependência de stack em **duas colunas** (seção 6):

| Coluna | Definição | Viaja? |
|--------|-----------|--------|
| **essencial ao problema** | quebra se for mudado, porque o terceiro exige | **sim** — vale em qualquer projeto |
| **herdada do sistema de origem** | escolha da casa onde o módulo nasceu | **não** — o stackx do destino traduz |

O teste, item a item: **se eu trocar isto, o terceiro para de funcionar?**

No módulo zero:

| Item | Coluna | Por quê |
|------|--------|---------|
| webhook público, sem sessão de usuário | essencial | o fornecedor chama de fora; com JWT recebe 401 |
| webhook responde 2xx em todo caminho | essencial | não respondendo, o fornecedor reenfileira para sempre |
| JID de grupo preservado, sem tirar os não-dígitos | essencial | é regra do WhatsApp, não da casa |
| dois headers de autenticação (criar × operar) | essencial | é o contrato do fornecedor |
| coluna de tenancy chamada `company_id` | **herdada** | qualquer nome serve |
| resposta `{success, data}` | **herdada** | convenção da casa |
| interface em pt-BR | **herdada** | escolha de produto da origem |
| RLS via função `SECURITY DEFINER` | **herdada** | é a solução da origem para um problema real do Postgres; outro projeto pode isolar tenant de outro jeito |

Um módulo cuja coluna herdada está vazia é suspeito. Praticamente todo módulo extraído carrega convenção da casa de origem — não enxergá-la é o caminho mais curto para injetá-la.

## O stackx local vence. Sempre

**Regra 3, sem exceção:** convenção herdada do sistema de origem nunca sobrepõe o `docs/stack/CONVENCOES.md` do projeto de destino.

O motivo é o princípio do próprio stackx: *convenção não se propõe, se descobre*. O que o repositório de destino já faz **é** a convenção dele. O que outro repositório faz é informação sobre outro repositório — não tem autoridade nenhuma aqui.

Na prática, quando a M1 entrega a seção 6:

```
Stack essencial (viaja):
  <item> — <por que o terceiro exige>

Stack herdada (traduzir pelo stackx do destino):
  <item do modulo> → <equivalente neste projeto, ou PENDENTE>
```

- Se o projeto tem `CONVENCOES.md`: a coluna da direita é preenchida com o que o stackx detectou, e o que conflita é marcado.
- Se não tem: fica `PENDENTE`, e a M1 diz que o stackx não rodou. **Nunca preencha por analogia** — inventar o equivalente é reintroduzir a convenção alheia com outro nome.

## Conflito é reportado, não resolvido

Quando a convenção **essencial** do módulo conflita com a convenção detectada do projeto, o modulex **reporta e para**.

Exemplo real: o módulo zero exige um endpoint público sem autenticação de sessão. Um projeto cujo `CONVENCOES.md` diz "toda rota exige token autenticado" tem um conflito verdadeiro — e ele não é do modulex resolver.

```
CONFLITO — modulo <id>
  Modulo (essencial): <convencao>
  Projeto (CONVENCOES.md:<linha>): <convencao>
  Nao ha traducao. Isto e decisao humana, na F2 do sprintx.
```

O modulex não escolhe entre a regra de segurança do projeto e a exigência do fornecedor. Ele mostra as duas e cala.

Conflito na coluna **herdada** não é conflito: é tradução. `company_id` → `org_id` resolve-se sozinho, e é a M1 que faz.

## Precedência, com legadox na mesa

O stackx já tem uma regra de precedência com o legadox. O modulex entra abaixo das duas:

| Camada | Governa |
|--------|---------|
| **legadox** | o que existe hoje, na área tocada de projeto legado |
| **stackx** | o que deve ser seguido daqui pra frente, neste projeto |
| **modulex** | o que outro projeto fez, como informação |

Em projeto legado, o padrão local da área tocada manda; o stackx governa código novo em arquivo novo; e o módulo é insumo dos dois — nunca autoridade sobre nenhum.

## O que não muda no stackx

- O stackx não passa a consultar o modulex para detectar convenção. A detecção continua sendo sobre o repositório real, com evidência de arquivo e linha.
- Convenção de módulo **nunca** entra no `CONVENCOES.md` do projeto de destino. Se uma convenção essencial do módulo precisar valer aqui, ela entra como **PROPOSTA**, com confirmação humana, exatamente como qualquer outra proposta.
- Sem `CONVENCOES.md`, a injeção não bloqueia: marca `PENDENTE` e sinaliza que rodar o stackx antes da F3 evita retrabalho.
