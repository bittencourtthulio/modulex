# Ligar a esteira — o hook de detecção

O detector roda ao fim do trabalho e enfileira candidato. Ele **não extrai
módulo** (D17) e **não publica nada**.

## Claude Code

No `settings.json` do projeto:

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

## OpenCode

Mesmo comando, no gancho de fim de sessão do harness.

## Sem hook

Rode à mão quando fechar a feature, ou deixe a mergex chamar no fechamento
do trabalho — `docs/integracao/patch-sprintx.md`:

```bash
python3 .expx/modulex/scripts/detectar_candidato.py
```

## As duas regras do hook

**`--quieto` é obrigatório no modo automático.** Um hook que fala quando não
tem nada a dizer é um hook que alguém desliga na segunda semana.

**O script sai com código 0 mesmo quando falha.** Hook que quebra atrapalha
quem está entregando, e a regra 11 vale aqui também: a ausência do modulex
nunca bloqueia ninguém.

## O que ele grava, e onde

`.expx/modulex/fila/<slug>.json` — **local, nunca publicado**.

Esse arquivo carrega nome de branch, caminho e host, que podem identificar o
cliente. Ele é evidência para quem vai extrair. O que sobe é o `MODULO.md`
que a M2 produz, e só ele passa pelo gate.

Acrescente ao `.gitignore` do projeto:

```
.expx/modulex/fila/
```

## Ajustar a sensibilidade

O limiar padrão é 4 pontos. Para ver o que ele acharia sem enfileirar:

```bash
python3 .expx/modulex/scripts/detectar_candidato.py --limiar 99
```

Falso positivo não é inofensivo: enche a fila de candidato que ninguém vai
promover, e fila cheia de lixo é fila que ninguém abre.
