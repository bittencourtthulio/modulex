---
kind: modulo_indice
schema: expx-schema-v1
criado_em: 2026-09-02
atualizado_em: 2026-09-19
total_modulos: 1
ativos: 1
obsoletos: 0
vencidos: 0
---

# Índice de módulos

Append-only. Uma linha por módulo. É a versão legível do `modulos.json` — a
consulta do M0 lê o JSON; humano lê este arquivo.

Módulo obsoleto **nunca é apagado**: a M0 continua achando e responde
"existiu, está obsoleto, motivo". Apagar faz a próxima pessoa refazer a
descoberta.

| id | problema | fatias | fornecedor | esforco | verificado_em | status |
|----|----------|--------|------------|---------|---------------|--------|
| `whatsapp-uazapi` | atender cliente por WhatsApp, com caixa de entrada compartilhada pela equipe | 6 (nucleo + 5 opcionais) | uazapi | NAO DETERMINADO | 2026-08-24 | ativo |

## Indicadores

- **Módulos no catálogo:** 1
- **Ativos:** 1 · **Obsoletos:** 0
- **Com verificação vencida:** 0 — o contrato do `whatsapp-uazapi` vence em 2027-02-24
- **Buscas sem resultado registradas:** 2 — `emitir nota fiscal` e `nfs-e`, ambas em 2026-09-19
- **Sem faixa de esforço:** 1 — o P4 do prodx dimensiona só pelas fatias nesse caso

## O indicador do modulex

Dois números, e cada um sozinho mente. Precisam do memox para serem exatos;
sem ele, o catálogo reporta o que sabe e declara a leitura parcial.

- **Features com integração de terceiro que consultaram um módulo antes de planejar:** 0 de 0 — nenhuma feature ainda. As buscas de 2026-09-19 foram verificação da instalação, não features, e deliberadamente **não** entram aqui
- **Dessas, quantas viraram módulo novo depois:** 0

| Leitura | Escrita | Diagnóstico |
|---------|---------|-------------|
| alta | alta | ciclo fechado — estado saudável |
| alta | zero | o catálogo apodrece: é lido, envelhece, ninguém devolve |
| zero | alta | a consulta está cara demais |
| zero | zero | a skill não pegou |
