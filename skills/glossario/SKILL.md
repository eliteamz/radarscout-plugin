---
name: glossario
description: "Explica conceitos e métricas do Radar e da operação Amazon — PMI, MC1–MC3, repasse/settlement, preço mínimo, floor price, Buy Box %, safe mode, GMV, Receita Líquida, CMV — com definição em português. Use quando o usuário perguntar o que significa um termo, sigla ou métrica, ou pedir para explicar um conceito. Não acessa dados do seller."
---

# Glossário do Radar

Traduz o jargão do Radar e da Amazon em linguagem de vendedor. **Não** consulta dados da conta — é puramente conceitual, então **não** precisa de `seller_account_id`.

## Quando usar

- Usuário pergunta "o que é PMI?", "o que significa MC3?", "o que é safe mode?", "Buy Box % é o quê?".
- Outra skill usou um termo e o usuário não entendeu — explique aqui e volte ao fluxo.

## Passo a passo

1. Identifique o(s) termo(s) na pergunta.
2. Chame a tool de conceitos (`explain_concept`) do servidor `radarscout` com `term` (ex.: `PMI`, `MC3`, `repasse`, `floor price`, `Buy Box %`, `safe mode`, `GMV`, `Receita Líquida`, `CMV`, `settlement`). Ela devolve uma definição em português e **não** acessa dados de seller.
3. Se houver vários termos, chame uma vez por termo e agrupe as respostas.
4. Aterrisse a definição no contexto do usuário quando der ("…por isso seu preço travou no mínimo").

## O que entregar

- Definição curta e clara, em pt-BR, sem repetir só o nome.
- Quando útil, uma frase de "por que isso importa para você".
- Se o termo não for do domínio Radar/Amazon, diga e ofereça o conceito mais próximo.

## Exemplo

> **Usuário:** "O que é PMI?"
> **Skill:** chama `explain_concept` (term="PMI").
> **Resposta:** "**PMI** é o Preço Mínimo Inteligente — o menor preço que o repricer pode aplicar sem comer sua margem, calculado a partir do seu custo. É ele que trava a queda de preço quando o concorrente baixa demais."

## Cuidados

- Não invente definições — use sempre a tool de conceitos como fonte.
- Para perguntas sobre **os dados** do usuário (e não sobre o conceito), encaminhe à skill apropriada (`lucro`, `repricer`, etc.).
