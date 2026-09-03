# Integração — prodx

O prodx decide **se** há trabalho. O modulex informa **quanto custa** o trabalho que existe pronto. São perguntas diferentes, e confundi-las quebra o P3.

## Três pontos de contato, todos com autoridade de sinal

| Etapa | O que o modulex entrega | Autoridade |
|-------|------------------------|-----------|
| **P3** existência | um sexto lugar de busca: "existe módulo pronto para este problema?" | **sinal apenas** |
| **P4** avaliação | a faixa de esforço de referência e as **fatias**, para dimensionar o escopo mínimo | **sinal apenas** |
| **P5** briefing | uma linha `modulo_disponivel: <id>` na seção de contexto | **contexto, nunca instrução** |

## P3 — o sexto lugar, e a distinção que não pode ser perdida

O P3 procura em cinco lugares. O modulex é o sexto, e é o único que responde uma pergunta **diferente** dos outros cinco.

Os cinco lugares originais respondem: *o sistema já faz isso?*
O modulex responde: *alguém já resolveu isso antes, em outro projeto?*

**Módulo pronto NÃO é evidência de que o sistema já faz.** Ninguém instalou nada. Se o P3 tratasse o achado como existência, devolveria `ja_existe` para uma funcionalidade inexistente — e o cliente receberia um texto explicando onde encontrar uma tela que não está lá. Esse é o erro mais caro que o prodx pode cometer, porque ele volta pelo atendimento.

O que o módulo prova é outra coisa: **fazer é mais barato do que parece.** Isso não muda o desfecho do P3; muda o P4.

Portanto:

- o achado do modulex **nunca** produz desfecho `EXISTE` nem `EXISTE PARCIAL`;
- ele é registrado como **achado à parte**, na seção de evidências do `02-existencia.md`, com rótulo próprio;
- o desfecho do P3 continua saindo dos cinco lugares originais.

Formato do registro no `02-existencia.md`, como sexta linha da tabela "onde foi procurado":

```
| 6 | Catalogo do modulex | <id do modulo>, fatias <n>, esforco <faixa> — NAO e evidencia de existencia |
```

## P4 — fatias mudam o escopo mínimo, e este é o efeito principal

O P4 pergunta qual é a menor versão que resolve o problema. As fatias do módulo respondem isso com dado real, não com opinião.

**"WhatsApp completo" e "só receber e responder mensagem" são escopos mínimos de tamanhos muito diferentes**, e a distinção é decisão de produto, não técnica — que é exatamente o que o P4 decide.

No módulo zero: o núcleo (instâncias, webhook, mensageria, inbox) resolve "atender cliente por WhatsApp". As extensões (organização, IA, ligações, grupos, notificações) resolvem outras coisas, que o cliente pode nem ter pedido. Um P4 que enxerga as fatias corta o escopo mínimo no núcleo e deixa o resto como pedido futuro.

Duas travas:

1. **A faixa de esforço é referência histórica, não estimativa** (regra 6). Ela entra no P4 como ordem de grandeza — "isso é dia ou é mês" — e nunca como número no veredito. Estimar continua sendo a F3.5 do sprintx.
2. **O escopo mínimo não nomeia tecnologia.** O P4 escreve "receber e responder mensagem de WhatsApp num inbox compartilhado", nunca "instalar o módulo whatsapp-uazapi". A escolha do fornecedor é do sprintx, com o stackx (regra 1).

Quando a faixa for `NAO DETERMINADO` — como no módulo zero — o P4 usa as fatias e **ignora o esforço**. Fatia sem esforço ainda dimensiona escopo; esforço inventado envenena o veredito.

## P5 — uma linha no briefing, e só

O briefing ganha **uma linha**, na seção "Contexto para quem vai investigar":

```
modulo_disponivel: <id do modulo> — <fatias que cobrem o escopo minimo aprovado>
```

Isso é **contexto, nunca instrução** — a mesma regra que já governa aquela seção, e a regra 10 do prodx continua valendo integralmente.

| Pode | Não pode |
|------|----------|
| dizer que existe módulo e qual é | dizer para usar o módulo |
| citar as fatias que cobrem o escopo | citar tabela, biblioteca, endpoint ou arquitetura |
| citar o id, que é uma referência | citar o fornecedor como decisão tomada |

A fronteira é a de sempre: o briefing **descreve o que se sabe**, não **prescreve o que fazer**. Um briefing que diz "usar Uazapi" invadiu o sprintx e deve ser corrigido — exatamente como um briefing que trouxesse estimativa.

## O que não muda no prodx

- A triagem (P0) não consulta o modulex. Nenhum dos oito gatilhos depende do catálogo, e acrescentar consulta ao caminho curto encarece a etapa que existe para ser barata.
- Os quatro vereditos continuam os mesmos. Não existe veredito `usar_modulo`.
- A regra 5 (evidência) continua valendo: o achado do modulex é evidência de que **o módulo existe**, não de que a funcionalidade existe.
- A ausência do modulex não muda nada. Sem catálogo, o P3 procura nos cinco lugares e o P4 dimensiona como sempre dimensionou.

## O erro a evitar, em uma frase

Tratar "existe módulo" como "já existe no sistema". O primeiro é uma oportunidade de custo; o segundo é uma resposta ao cliente. Trocar um pelo outro devolve ao cliente uma tela que não existe.
