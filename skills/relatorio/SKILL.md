---
name: relatorio
description: "Gera um briefing diário ou semanal do negócio em formato narrativo — vendas, lucro, produtos campeões, saúde do repricer e próximo repasse — numa visão única. Use quando o usuário pedir um relatório semanal ou diário, um resumo geral, um 'como está meu negócio', ou um panorama consolidado da operação."
---

# Relatório (briefing do negócio)

Consolida as várias frentes do Radar num **texto corrido**, não em planilhas soltas. É a visão "abre o dia/semana e me diz o que importa".

## Conta e período

- **Conta:** exige `seller_account_id` (via `whoami` do `radarscout`).
- **Período:** "diário" = ontem→hoje (exclusivo); "semanal" = últimos 7 dias. ISO `YYYY-MM-DD`, America/Sao_Paulo, fim exclusivo. Sempre diga a janela usada.

## Passo a passo

Reúna as peças (reaproveitando o mesmo `seller_account_id` e janela), na ordem:

1. **Vendas:** tool de panorama (`get_sales_overview`) — GMV, pedidos, ticket, MC3.
2. **Campeões:** tool de ranking (`list_top_products`, `sort=profit`) — top 3–5 + alerta de margem negativa.
3. **Lucro:** tool de cascata (`get_profit_waterfall`) — MC3 e a maior deducão; ressalva de cobertura de CMV se parcial/ausente.
4. **Repricer:** tool de resumo (`get_repricer_summary`) — Buy Box win rate + aumentos/reduções; sinalize erros ou tudo em safe mode.
5. **Repasse:** tool de resumo de repasses (`get_settlement_summary`) — próximo depósito (quando/quanto).

Se alguma tool falhar ou vier vazia, siga com as demais e diga o que faltou — não trave o relatório inteiro.

## O que entregar

Um briefing narrativo curto, em pt-BR, com esta espinha:
- **Manchete** do período (vendeu/lucrou mais ou menos, em uma frase).
- **Vendas & lucro** com os números-chave.
- **Destaques** (campeões) e **alertas** (margem negativa, CMV faltando, repricer com erro/safe mode).
- **Dinheiro a caminho** (próximo repasse).
- Fecho com 1–2 ações sugeridas, quando houver sinal claro.

## Exemplo

> **Usuário:** "Me dá o relatório da semana."
> **Resposta (resumo):** "**Semana boa: GMV R$ 51k (+9%), MC3 R$ 7.8k (+4%).** Campeão: Fone X (R$ 1.9k de lucro). ⚠️ Cabo Y vendendo no prejuízo. Repricer saudável (Buy Box 72%, mais reduções que aumentos). 💰 Próximo repasse ~R$ 12k em 19/06. Sugestão: revisar o preço mínimo do Cabo Y."

## Cuidados

- É um resumo: não cole tabelões. Sintetize.
- Respeite as ressalvas das tools (CMV parcial, ingestão de repasse incompleta, safe mode).
- Pode ser disparado manualmente como `/relatorio` ou automaticamente quando o usuário pede um resumo geral.
