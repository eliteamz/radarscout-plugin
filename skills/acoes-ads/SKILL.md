---
name: acoes-ads
description: "Executa mudanças em Amazon Ads pelo Radar — pausar, reativar ou arquivar campanha, ajustar orçamento diário, mudar lance de palavra-chave, de alvo ou o padrão do grupo, pausar palavra-chave ou alvo, negativar termo ou alvo, criar campanha inteira, grupo de anúncios ou portfólio, incluir palavras-chave, alvos ou produtos num grupo existente — e revisa a fila de propostas da automação (aprovar ou recusar). Use quando o usuário pedir para pausar/ligar/arquivar campanha, subir ou baixar lance ou orçamento, desligar uma palavra que gasta sem vender, negativar um termo, lançar uma campanha nova, organizar campanhas em portfólios, adicionar palavras ou produtos a um grupo, ou perguntar o que o Radar está sugerindo nos anúncios."
---

# Ações em Amazon Ads

Aqui a conversa **muda a conta de anúncios de verdade**. Para só entender o desempenho, use a skill `ads` — esta é o passo seguinte, quando o vendedor decidiu agir.

**Conta:** exige `seller_account_id` (via `whoami` do `radarscout`) e o app **Performance** com plano ativo (`entitlements.performance` no `whoami`). Em modo preview o vendedor lê os dados de Ads, mas nenhuma ação é aplicada — diga isso em vez de tentar.

## A regra que não se quebra

Toda ferramenta de ação roda **em simulação por padrão**: sem `execute:true` ela devolve exatamente o que seria enviado à Amazon e nada muda lá.

1. **Leia antes.** Pegue o estado atual: `list_ad_campaigns` (id, orçamento diário e portfólio da campanha), `list_ad_bids` (`campaign_id`/`ad_group_id`/`keyword_id`/`target_id` e o lance atual), `list_search_terms` (o termo antes de negativar), `list_seller_offers` (o SKU antes de anunciar um produto). **Nunca invente um id** nem chute o valor atual.
2. **Simule.** Chame a ferramenta sem `execute` e mostre ao vendedor o de-para: valor de hoje → valor proposto, e por quê.
3. **Peça confirmação explícita** desse valor, nessa campanha.
4. **Só então** repita a chamada com `execute:true`.

Um "pode subir o lance" solto no meio da conversa **não é confirmação** de um valor específico. Simule, mostre o número, confirme.

## As ações

### Ajustar o que já existe

| Quero… | Ferramenta | Precisa de |
| --- | --- | --- |
| Pausar campanha | `pause_campaign` | `campaignId` |
| Religar campanha | `resume_campaign` | `campaignId` |
| Mudar orçamento diário | `update_campaign_budget` | `campaignId`, `dailyBudget` |
| Mudar lance de palavra-chave | `update_keyword_bid` | `keywordId`, `bid` |
| Mudar lance de alvo | `update_target_bid` | `targetId`, `bid` |
| Mudar lance padrão do grupo | `update_ad_group_default_bid` | `adGroupId`, `defaultBid`; `campaignId` recomendado |
| Desligar/religar palavra-chave | `update_keyword_state` | `keywordId`, `state` (`PAUSED`/`ENABLED`); `campaignId` recomendado |
| Desligar/religar alvo | `update_target_state` | `targetId`, `state`; `campaignId` recomendado |
| Bloquear uma busca | `create_negative_keyword` | `campaignId` (sempre), `keywordText`, `matchType`; `adGroupId` para valer só no grupo |
| Bloquear um produto/categoria | `create_negative_target` | `campaignId` (sempre), `expression`; `adGroupId` para valer só no grupo |
| Encerrar campanha de vez | `archive_campaign` | `campaignId`, `confirm_archive:true` junto de `execute:true` |

Negativação **sem** `adGroupId` vale para a campanha inteira — diga isso ao vendedor antes de executar; é o erro mais caro de reverter.

**Pausar ≠ negativar.** Pausar a palavra-chave (ou alvo) só desliga aquele item — para de gastar, o histórico fica e dá para religar. Negativar bloqueia a **busca** no grupo ou na campanha inteira, inclusive o que outras palavras capturariam. Se o vendedor quer "parar de gastar com essa palavra", pausar costuma ser o que ele quer; pergunte se houver dúvida.

**Lance padrão do grupo move vários de uma vez.** `update_ad_group_default_bid` muda todo item que aparece com `bid_source` "padrão do grupo" em `list_ad_bids`. Mostre quantos itens herdam antes de simular.

**Arquivar não tem volta.** A campanha sai da operação para sempre na Amazon. Só chame com `confirm_archive:true` depois de o vendedor dizer, com essas palavras, que entende que não dá para desfazer. Para tirar do ar de forma reversível, é `pause_campaign`.

### Montar estrutura nova

| Quero… | Ferramenta | Precisa de |
| --- | --- | --- |
| Lançar uma campanha inteira | `create_campaign_structure` | `campaign` (nome, `targetingType` AUTO/MANUAL, `dailyBudget`, `state`, datas e `portfolioId` opcionais) + `adGroups[]` (nome, `defaultBid`, `state`, `productAds` por SKU e, opcionalmente, `keywords`, `targets`, `negativeKeywords`) |
| Criar portfólio | `create_portfolio` | `name` (único na conta) |
| Criar grupo numa campanha existente | `create_ad_group` | `campaignId`, `name`, `defaultBid`, `state` |
| Incluir palavras-chave num grupo | `add_keywords` | `campaignId`, `adGroupId`, `keywords[]` (texto, `matchType`, `bid` opcional) — até 100 |
| Incluir alvos de produto/categoria | `add_targets` | `campaignId`, `adGroupId`, alvos (tipo como `ASIN_SAME_AS`/`ASIN_CATEGORY_SAME_AS`, valor, `bid` opcional) — até 100 |
| Anunciar um produto num grupo | `add_product_ads` | `campaignId`, `adGroupId`, SKUs (de `list_seller_offers`; conta de seller não aceita ASIN) — até 100 |

Como conduzir:

- **Nada de estratégia padrão.** Nome, orçamento, lances, tipo de correspondência, estado inicial e a divisão em grupos são escolhas do vendedor (ou da agência). Se faltar algum, **pergunte** — não preencha com um "padrão de mercado".
- **Estado inicial é declarado.** A campanha nasce `ENABLED` ou `PAUSED` conforme o vendedor disser. Nascer pausada e religar em seguida gasta a espera de 7 dias do estado da campanha — se ele quer ligada, crie ligada.
- **Simule a árvore inteira.** O `dry_run` de `create_campaign_structure` traz o `plan` — as chamadas que seriam feitas, em ordem. Apresente como árvore legível (campanha → grupos → produtos/palavras) antes de confirmar.
- **Grupo nasce vazio.** Depois de `create_ad_group`, vêm `add_product_ads` e `add_keywords`/`add_targets`; sem produto o grupo não veicula.
- **Repetidos são recusados, não ignorados.** Palavra, alvo ou produto que já está no grupo recusa a chamada inteira com a lista `duplicates` — tire esses e simule de novo.
- **Aceite parcial é real.** Se a Amazon aceitar só parte, a resposta vem `partial: true` com `created` e `failed` (com motivo por item). O que foi criado existe na conta — reporte os dois lados.
- **Falha no meio da criação de campanha:** status `partial`; o que chegou a existir é **pausado** por segurança (`pausedForSafety`). Diga o que existe, o passo que falhou (`failedStep`) e o motivo. Se `compensationFailed` vier `true`, a pausa de segurança também falhou — peça ao vendedor para conferir a conta.

## As proteções (e como explicá-las)

O Radar segura mudanças bruscas. Vale explicar em português, não citar o nome técnico:

- **Mudança gradual:** orçamento até **30%** por vez, lance até **50%** por vez.
- **Faixas de segurança:** lance de **R$ 0,02 a R$ 500**; orçamento de **R$ 1 a R$ 100.000** — barram erro de digitação.
- **Tempo de observação:** depois de uma mudança, aquela campanha, palavra-chave, alvo ou lance padrão de grupo espera **7 dias** antes de aceitar outra (**14 dias** para negativações) — é o tempo de as vendas por atribuição amadurecerem e dar para ver o efeito. Pausar e religar (campanha, palavra ou alvo) contam como a mesma mudança. **Incluir** palavras, alvos, produtos ou grupos não tem espera — não substitui nada.
- **Teto diário da conta:** pelo menos 100 alterações/dia no total e até 50 por campanha. Uma inclusão em lote conta como uma alteração.
- **Criação de campanhas:** até **10 por dia** por conta; nome de campanha já existente é recusado (sem diferenciar maiúsculas).

Quando uma chamada volta recusada, o motivo vem junto:

| Resposta | O que dizer ao vendedor |
| --- | --- |
| `dry_run` | Simulação: "é isso que eu mandaria — confirma?" |
| `ok` | Aplicado na Amazon. |
| `rejected_cooldown` | Essa campanha/lance mudou há pouco; faltam `daysRemaining` dias de observação. |
| `rejected_hard_limit` | O valor está fora da faixa permitida, o salto é grande demais (mostre a `allowedRange`), ou o nome/item já existe (mostre os `duplicates`). |
| `rejected_rate_limit` | Bateu o teto de alterações do dia (ou de 10 campanhas criadas no dia). |
| `partial` | Parte foi criada, parte não — reporte o que existe e o que faltou. |
| `api_error` | A Amazon recusou — repasse o motivo (tipo do erro, campo, faixa), não repita a chamada às cegas. |
| `not_connected` | A conta de anúncios não está conectada ao Radar. |
| `not_enabled` | A execução de mudanças não está liberada para essa conta. |

**Forçar (`override_cooldown`, `override_rate_limit`) só depois** de a chamada ter sido recusada exatamente por esse motivo **e** com o vendedor confirmando que quer passar por cima. Nunca mande "forçar" preventivamente, nunca por conta própria.

## Fila de propostas da automação

O motor de Ads do Radar propõe mudanças; quem decide é o vendedor.

1. `list_ads_proposals` — traz as **pendentes** por padrão (`status:"all"` para todas). Cada uma vem com `reasoning`, `params` e `ageInHours`.
2. Apresente a proposta em linguagem do vendedor: o que muda, de quanto para quanto, e o motivo.
3. `approve_ads_proposal` (executa) ou `reject_ads_proposal` com `reason` — o motivo fica registrado.

Duas ressalvas:

- **Proposta com mais de 24 horas pode estar velha** — lances, orçamentos e status mudam. Confira o estado atual pelas ferramentas de leitura antes de aprovar.
- **A execução é assíncrona.** Aprovar não confirma o resultado: consulte `list_ads_proposals` de novo. `executed` = aplicada; `failed` = falhou; `unknown` = **o sistema não conseguiu confirmar** — investigue pelo `auditId` antes de tratar como feito.

## O que entregar

- Antes: o de-para (hoje → proposto), o motivo e o que a mudança afeta.
- Depois: o que efetivamente aconteceu — aplicado, recusado (com o motivo em português) ou aguardando confirmação.
- Quando o vendedor pedir uma decisão que a leitura não sustenta (poucos cliques, janela imatura), **diga isso antes de executar**. A skill `ads` tem as premissas de leitura.

## Exemplo

> **Usuário:** "Baixa o lance daquela keyword que tá queimando dinheiro."
> **Skill:** `list_ad_bids` (lance atual R$ 2,40) → simula `update_keyword_bid` com R$ 1,80.
> **Resposta:** "Hoje o lance de *capa de celular* é R$ 2,40. Simulei a redução para **R$ 1,80** (−25%, dentro do permitido) — nada foi enviado à Amazon ainda. Confirma que aplico? Depois de aplicar, essa palavra-chave fica 7 dias sem aceitar nova mudança, para dar tempo de ver o efeito."

## Cuidados

- **Nunca execute sem confirmação explícita do valor.** Simulação primeiro, sempre.
- **Nunca invente ids.** `campaignId`, `adGroupId`, `keywordId`, `targetId`, `portfolioId` e SKU vêm das ferramentas de leitura.
- **Nunca arquive** sem o vendedor confirmar que entende que não tem volta; na dúvida, pause.
- **Nunca force** uma proteção sem recusa prévia por aquele motivo e sem o vendedor pedir.
- **Não negative um termo** em cima de poucos cliques ou de janela recente — veja `keyword_type` e volume na skill `ads`.
- **Não trate proposta aprovada como concluída** sem reler a fila; `unknown` pede investigação.
- Não decida a estratégia pelo vendedor: quanto cortar, quando pausar e o ACoS-alvo são dele. Sem estratégia declarada, exponha o trade-off e pergunte.
