---
name: lucro
description: "Detalha a composição do lucro de uma conta de seller via cascata financeira (GMV → Receita Líquida → MC1 → MC2 → MC3), mostrando devoluções, impostos, CMV, tarifas Amazon e anúncios, e sinaliza a cobertura de CMV. Use quando o usuário perguntar por que o lucro caiu, quiser entender margem ou custos, pedir o detalhamento de MC1–MC3, ou questionar de onde vem (ou some) o lucro."
---

# Análise de lucro (cascata financeira)

Explica **onde o lucro é formado e consumido**, etapa a etapa, em vez de só mostrar um número final. Usa a cascata de margem de contribuição do Radar.

## Conta e período

- **Conta:** exige `seller_account_id` (via `whoami` do `radarscout`; ver skill `inicio` se houver várias contas).
- **Período:** ISO `YYYY-MM-DD`, America/Sao_Paulo, **fim exclusivo**. Sem período informado, use os **últimos 30 dias** e diga a janela.

## Passo a passo

1. Resolva conta + janela.
2. Chame a tool de cascata financeira (`get_profit_waterfall`) do `radarscout`. Ela retorna a sequência:
   - **GMV** − devoluções − impostos = **Receita Líquida**
   - Receita Líquida − **CMV** = **MC1**
   - MC1 − **tarifas Amazon** = **MC2**
   - MC2 − **anúncios** = **MC3** (lucro líquido)
   - mais a **cobertura de CMV** (completa / parcial / ausente) e a lista de SKUs **sem custo cadastrado**.
3. Se a cobertura de CMV for parcial/ausente, **avise**: o MC1–MC3 está subestimado em lucro (custo faltando) e liste os SKUs a cadastrar.
4. Para um produto específico, complemente com a tool de detalhe de catálogo (`get_product_details`, por ASIN) quando o usuário quiser estimativas/tarifas daquele item.
5. Se o usuário não entender um termo (MC1, CMV, Receita Líquida…), use a tool de conceitos (`explain_concept`) ou a skill `glossario`.

## O que entregar

- A cascata em reais, do GMV ao MC3, mostrando quanto cada etapa **consome**.
- A maior alavanca: qual dedução mais comeu o lucro (CMV? tarifas? anúncios? devoluções?).
- **Ressalva de cobertura de CMV** sempre que não for completa.
- Resposta direta ao "por que caiu": compare com período anterior se fizer sentido.

## Exemplo

> **Usuário:** "Por que meu lucro caiu mês passado?"
> **Skill:** chama a cascata do mês e do anterior.
> **Resposta:** "Seu GMV subiu 8%, mas o **MC3 caiu 14%**. O vilão foram os **anúncios** (de R$ 3.1k → R$ 6.4k). CMV e tarifas ficaram estáveis. ⚠️ Cobertura de CMV **parcial**: 7 SKUs sem custo — o lucro real pode estar pior."

## Cuidados

- Não confunda **Receita Líquida** (pós-devoluções/impostos) com GMV.
- MC3 é o lucro final; não some anúncios/tarifas por fora.
- Sem CMV cadastrado, o MC1+ fica otimista — sempre sinalize.
