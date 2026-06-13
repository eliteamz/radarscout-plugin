---
name: repasses
description: "Resume os repasses (settlements) da Amazon de uma conta de seller — próximo repasse projetado (quando e quanto), último repasse concluído e deduções do ciclo — e concilia com as vendas do período. Use quando o usuário perguntar quanto/quando vai receber, falar em repasse/settlement/depósito, ou estranhar a diferença entre o que vendeu e o que a Amazon deposita."
---

# Conferência de repasses

Responde **"quando e quanto vou receber?"** e explica por que o **valor depositado ≠ valor vendido**.

## Conta e período

- **Conta:** exige `seller_account_id` (via `whoami` do `radarscout`).
- **Período:** o resumo de repasses **não usa período** (é por ciclo). Para a conciliação com vendas, sim — ISO `YYYY-MM-DD`, America/Sao_Paulo, fim exclusivo; sem período use os últimos 30 dias.

## Passo a passo

1. Resolva a conta.
2. Chame a tool de resumo de repasses (`get_settlement_summary`) do `radarscout` (só `seller_account_id`). Ela traz:
   - **Próximo repasse projetado:** data estimada de depósito + valor estimado.
   - **Último repasse concluído:** período, **repasse líquido (net payout)** e **data de depósito no banco** (distinta do fim do período).
   - **Deduções do ciclo atual:** tarifas Amazon + reserva retida.
   - Um aviso quando a **ingestão de dados ainda está incompleta** (valores podem ser parciais).
3. Para conciliar com vendas, chame a tool de panorama de vendas (`get_sales_overview`) na janela equivalente e contraste GMV/Receita com o repasse líquido — a diferença são tarifas, reservas, devoluções e impostos.
4. Termos (settlement, reserva, net payout): use `explain_concept` ou a skill `glossario`.

## O que entregar

- Resposta direta: "**Próximo depósito: ~R$ X em DD/MM**" + último concluído.
- A ponte vendas → repasse: por que recebe menos que vendeu (deduções nomeadas).
- **Aviso de dados parciais** quando a tool sinalizar ingestão incompleta — não afirme valores como definitivos nesse caso.

## Exemplo

> **Usuário:** "Quanto a Amazon vai me repassar?"
> **Skill:** chama o resumo de repasses.
> **Resposta:** "**Próximo repasse projetado: ~R$ 18.400, depósito em 19/06.** Último concluído: R$ 16.900 líquidos, depositado em 05/06. No ciclo atual já há R$ 4.100 em tarifas + R$ 2.000 de reserva retida. ⚠️ Ingestão ainda em andamento — pode subir."

## Cuidados

- **Data de depósito ≠ fim do período** do settlement — não troque uma pela outra.
- Repasse líquido já é pós-deduções; ao conciliar, parta do GMV e subtraia, não some.
- Se houver aviso de ingestão incompleta, deixe claro que é estimativa parcial.
- **Sem repasse registrado** (próximo, último e ciclo todos nulos): diga que ainda não há repasse projetado nem concluído para essa conta — não despeje campos vazios.
