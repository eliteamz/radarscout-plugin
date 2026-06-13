# Radar Scout — Plugin (MCP + Skills)

Converse com seus dados do Radar Scout — **vendas, lucro (MC1–MC3), repasses e repricer** da sua operação Amazon — direto no seu agente de código.

> **Status:** v0.1.0 — MCP remoto + 8 skills do vendedor.

## O que vem aqui

- **MCP `radarscout`** — servidor remoto read-only (HTTP + OAuth) com 12 tools: `whoami`, busca/detalhe de produtos, panorama de vendas, profit waterfall, repasses, repricer e glossário.
- **Skills** — 8 fluxos do dia a dia do vendedor, que orquestram os tools e entregam output interpretado em pt-BR:

| Skill | Para quê |
| --- | --- |
| `radarscout:inicio` | Conecta e descobre a conta de seller |
| `radarscout:vendas` | Panorama de vendas (GMV, ticket, MC3, campeões) |
| `radarscout:lucro` | Cascata financeira (MC1–MC3, CMV, tarifas, anúncios) |
| `radarscout:repricer` | Saúde do repricer e "por que o preço mudou" |
| `radarscout:repasses` | Quando e quanto a Amazon vai repassar |
| `radarscout:produtos` | Busca de catálogo e watchlist de Buy Box |
| `radarscout:relatorio` | Briefing diário/semanal do negócio |
| `radarscout:glossario` | Explica termos (PMI, MC3, safe mode…) |

## Instalação

### Claude Code (plugin = MCP + skills de uma vez)

```bash
/plugin marketplace add eliteamz/radarscout-plugin
/plugin install radarscout@eliteamz
```

…ou só o MCP:

```bash
claude mcp add --transport http radarscout https://mcp.radarscout.com.br/mcp
```

### Claude Desktop / claude.ai

Settings → **Connectors** → "+" → nome `radarscout`, URL `https://mcp.radarscout.com.br/mcp`.
Connectors adicionados no claude.ai aparecem automaticamente no Claude Code.

### Cursor — `.cursor/mcp.json`

```jsonc
{ "mcpServers": { "radarscout": { "url": "https://mcp.radarscout.com.br/mcp" } } }
```

### Windsurf / Devin Desktop — `~/.codeium/windsurf/mcp_config.json`

```jsonc
{ "mcpServers": { "radarscout": { "serverUrl": "https://mcp.radarscout.com.br/mcp" } } }
```

### VS Code Copilot — `.vscode/mcp.json`

```jsonc
{ "servers": { "radarscout": { "type": "http", "url": "https://mcp.radarscout.com.br/mcp" } } }
```

O primeiro acesso dispara o login OAuth (no Claude Code: comando `/mcp`). O MCP é
read-only e seller-scoped: cada usuário enxerga apenas os próprios dados.

## Licença

MIT — ver [LICENSE](./LICENSE).
