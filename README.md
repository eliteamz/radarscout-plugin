# Radar Scout — Plugin (MCP + Skills)

Converse com seus dados do Radar Scout — **vendas, lucro (MC1–MC3), repasses e repricer** da sua operação Amazon — direto no seu agente de código.

> **Status:** encanamento (v0.1.0). As skills do vendedor chegam numa próxima leva.

## O que vem aqui

- **MCP `radarscout`** — servidor remoto read-only (HTTP + OAuth) com 12 tools: `whoami`, busca/detalhe de produtos, panorama de vendas, profit waterfall, repasses, repricer e glossário.
- **Skills** — fluxos do dia a dia do vendedor (em breve, via `skill-creator`).

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

## Checklist de release

Antes de publicar e submeter ao Anthropic Connector Directory:

- [ ] Tornar o repositório **público**.
- [ ] **Bump de `version`** no `plugin.json` (marketplaces de terceiros não auto-atualizam — usuários rodam `/plugin marketplace update`).
- [ ] Testar o **connect OAuth ao vivo** no Claude Desktop **e** no Claude Code atuais.
- [ ] Validar **CIMD vs DCR** no auth server (Clerk) e o **alcance público** do endpoint pela nuvem da Anthropic (sem bloqueio de IP).
- [ ] Revisar o texto do **consent screen** (nome do client + hostname) apresentado ao vendedor.
- [ ] Confirmar a versão mínima de Claude Code (`displayName` exige v2.1.143+).

## Licença

MIT — ver [LICENSE](./LICENSE).
