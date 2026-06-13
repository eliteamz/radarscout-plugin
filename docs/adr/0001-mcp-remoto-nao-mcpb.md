# Distribuir o MCP remoto via config `http`, não como bundle `.mcpb`

O Radar Scout expõe um MCP **remoto** (Streamable HTTP + OAuth 2.1) em `https://mcp.radarscout.com.br/mcp`. Decidimos distribuí-lo declarando-o como servidor `{"type":"http","url":...}` no `.mcp.json` do plugin (auto-descoberto pelo Claude Code) e, no release, via Anthropic Connector Directory + MCP Registry — **não** empacotando como bundle `.mcpb`/`.dxt`. O formato `.mcpb` empacota um servidor **local** (stdio) + `manifest.json`; ele não serve para um servidor remoto OAuth como o nosso.

## Considered Options

- **`.mcp.json` http + Directory/Registry** (escolhido) — caminho oficial para servidor remoto; connect via OAuth (401 → `/mcp`); zero binário a distribuir.
- **Bundle `.mcpb` com proxy stdio local** — exigiria empacotar e versionar um proxy que reencaminha para o endpoint remoto. Mais superfície de manutenção, instalação por device, e nenhum ganho sobre o connect remoto direto.

## Consequences

- A descoberta/instalação depende de o endpoint ser **público** e alcançável pela nuvem da Anthropic (sem bloqueio de IP) e de o auth server expor metadata correta (RFC 9728 + CIMD/DCR). Ver checklist de release no README e §7 da pesquisa.
- Um futuro contribuidor que vir só o `.mcp.json` pode propor "adicionar um `.mcpb`": isto registra que a ausência é deliberada.
