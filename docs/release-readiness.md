# Prontidão de distribuição — Radar Scout MCP

Verificação automatizada em **2026-06-12** contra os endpoints públicos
(`https://mcp.radarscout.com.br` + `https://clerk.radarscout.com.br`).

## Verificado ✔

- **Desafio 401:** `POST /mcp` sem token → `HTTP 401` com
  `WWW-Authenticate: Bearer ... resource_metadata=".../.well-known/oauth-protected-resource"`.
- **Protected Resource Metadata (RFC 9728):** OK → `authorization_servers = ["https://clerk.radarscout.com.br"]`.
- **Authorization Server Metadata (RFC 8414):** OK em `clerk.radarscout.com.br/.well-known/oauth-authorization-server`.
- **DCR habilitado:** `registration_endpoint` presente (`/oauth/register`) → connect **zero-config** via Dynamic Client Registration (RFC 7591).
- **CIMD não anunciado:** `client_id_metadata_document_supported` ausente → o Claude cai no **DCR** (comportamento esperado; `token_endpoint_auth_methods_supported` inclui `"none"`, mas sem o flag CIMD não basta).
- **PKCE:** `code_challenge_methods_supported = ["S256"]`.
- **Refresh tokens:** `grant_types_supported` inclui `refresh_token`; scope `offline_access` disponível.
- **Alcance público:** endpoints respondem por HTTPS (sem bloqueio observado).

## A conferir com o time ⚠️

- **Scope:** a PRM anuncia `profile/openid` e o auth server lista `openid, profile, email, offline_access, …` — **não** o `radar:read` citado na pesquisa (§7). Confirmar se o controle de acesso é por **audience-binding no resource** (RFC 8707) em vez de scope.

## Gates manuais (ação humana antes de publicar)

- [ ] Tornar o repositório **público**.
- [ ] Testar o **connect OAuth ao vivo** no Claude Desktop **e** no Claude Code atuais (consent + repasse de token).
- [ ] Revisar o texto do **consent screen** (nome do client + hostname do redirect) apresentado ao vendedor.
- [ ] Submeter ao **Anthropic Connector Directory** e publicar no **MCP Registry**.
- [ ] **Bump de `version`** a cada release (terceiros não auto-atualizam).
