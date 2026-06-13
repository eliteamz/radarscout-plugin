---
name: repricer
description: "Avalia a saúde do Repricer de uma conta de seller — taxa de vitória na Buy Box, aumentos vs reduções de preço, atividade recente e modo seguro — e explica por que o preço de um SKU mudou ou está travado no preço mínimo. Use quando o usuário perguntar se o repricer está funcionando, por que um preço caiu/subiu, por que um SKU não baixa do mínimo, ou quiser revisar a reprecificação."
---

# Diagnóstico do Repricer

Responde duas perguntas: **"o repricer está indo bem?"** (saúde agregada) e **"por que ESTE preço mudou?"** (auditoria por SKU).

## Conta e período

- **Conta:** exige `seller_account_id` (via `whoami` do `radarscout`).
- **Período:** ISO `YYYY-MM-DD`, America/Sao_Paulo, **fim exclusivo**. Sem período, use os **últimos 7 dias** (repricing é de movimento rápido) e diga a janela.

## Passo a passo

### Saúde geral
1. Chame a tool de resumo do repricer (`get_repricer_summary`) do `radarscout` com a janela. Traz a **taxa de vitória na Buy Box** das ofertas com repricing ativo e a contagem de **aumentos vs reduções** de preço no período.
2. Chame a tool de atividade (`list_repricer_activity`) para os eventos recentes (mais novo → mais antigo): alterações de preço, mudanças de config, syncs e erros. Eventos em **modo seguro (safe mode)** são **simulações** (não publicaram preço na Amazon) — marque-os como tal. Pagina por cursor; a primeira página basta para um diagnóstico.

### Por que este preço mudou
3. Quando o usuário aponta um SKU/ASIN, chame a tool de explicação de mudança de preço (`explain_price_change`) do `radarscout` com `sku` (ou `asin`). Para cada decisão recente ela mostra: gatilho, **preço anterior → novo**, ação aplicada (`match_price`, `price_below`, `use_min_price`…), a Automação/Estratégia e os **limites de mínimo/máximo** ativos. Ela explica **explicitamente** quando o anúncio está travado no **Preço Mínimo** e qual limite é o vínculo (mínimo manual ou **PMI** calculado por custo).

4. Termos (PMI, safe mode, Buy Box %): use `explain_concept` ou a skill `glossario`.

## O que entregar

- Veredito de saúde: Buy Box win rate + tendência de preços (mais subindo ou descendo).
- Se houver **erros** ou tudo em **safe mode**, destaque — pode ser por isso que "não está funcionando".
- Na auditoria por SKU: a causa em uma frase ("caiu porque a estratégia X igualou o concorrente; está travado no mínimo PMI de R$ 39").

## Exemplo

> **Usuário:** "Por que o preço do SKU ABC-123 não para de cair?"
> **Skill:** chama `explain_price_change` (sku=ABC-123).
> **Resposta:** "Nas últimas decisões, o repricer aplicou `price_below` para ganhar a Buy Box, indo de R$ 59 → R$ 41. Agora está **travado no Preço Mínimo (PMI R$ 41)** — não desce mais porque o PMI é calculado pelo seu custo. Para baixar, reduza o custo ou ajuste o PMI."

## Cuidados

- Distinga **safe mode** (simulação) de preço realmente publicado — não diga que "mudou o preço" se foi simulação.
- "Travado no mínimo" não é bug: é proteção de margem. Explique qual limite vincula.
- **Repricer sem ofertas/atividade** (`total_offers` 0 e 0 mudanças de preço no período): diga que não houve repricing — pode estar inativo ou sem ofertas elegíveis. A atividade ainda pode trazer eventos de **config** (ligar/desligar, safe mode); use-os para explicar o estado.
