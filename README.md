# Load Nova for Claude Code

Load Nova is a freight dispatch copilot by Frontier X Labs Technologies Inc. This plugin connects Claude Code to the Load Nova remote MCP server (streamable HTTP, MCP protocol 2025-11-25) and adds dispatch operating rules, six slash commands, and a skill. 19 tools are exposed: 14 Work tools (sessions, drivers, routing, broker audits, confirmed writes) and 5 Education tools (onboarding, practice drills, practice board lifecycle).

## Install

From the public marketplace repo:

```
/plugin marketplace add github:Frontier-X-Labs/loadnova-claude-plugin
/plugin install loadnova@loadnova
```

## First use: OAuth browser flow

The plugin ships `.mcp.json` pointing at the staging server. Leave OAuth client credentials blank — they are not needed:

1. On the first tool call, Claude Code reads protected-resource metadata from `/.well-known/oauth-protected-resource/mcp` on the MCP host and self-registers a public client via Dispatch Core Dynamic Client Registration (`POST https://api-stage.loadnova.app/api/v1/oauth/register`, PKCE S256).
2. Claude Code opens a browser window to Dispatch Core. Sign in with the Load Nova account (staging portal for staging, production portal for prod) and approve the requested `agent:*` scopes.
3. Return to Claude Code; the connection is ready. Run `/loadnova:loadnova-doctor` to verify.

Note: `https://staging.loadnova.app/mcp` is not the Load Nova MCP server. Always use the `mcp.*` hostnames in `.mcp.json`.

## Bearer-token fallback

If browser OAuth is unavailable, use a one-time agent token from the Load Nova web portal (Account → Agents). The MCP server forwards it to Dispatch Core. Add it as a header on the server entry in the installed plugin's `.mcp.json`:

```json
"loadnova": {
  "type": "http",
  "url": "https://mcp.staging.loadnova.app/mcp",
  "timeout": 120000,
  "headers": {
    "Authorization": "Bearer <agent-token>"
  }
}
```

Never commit a real token to the repository, paste it into chat, or store it in screenshots.

## Switching staging to production

`.mcp.json` ships a single `loadnova` server on staging
(`https://mcp.staging.loadnova.app/mcp`). To switch to production, edit the
installed plugin's `.mcp.json` and replace the `url` with
`https://mcp.loadnova.app/mcp`. Reconnect via `/mcp` and re-run the OAuth flow
against the production portal.

## Commands

| Command | What it does |
|---|---|
| `/loadnova:loadnova-doctor` | Verifies the MCP connection with a tools/list sanity check and `education_status` |
| `/loadnova:morning-dispatch-briefing` | Morning action plan from `start_dispatcher_session` and `list_drivers` |
| `/loadnova:parse-load-text` | Extracts load facts and audits them with `audit_broker_domain` and `audit_rate_confirmation` |
| `/loadnova:draft-broker-reply` | Drafts a broker reply from `calculate_route` economics plus a negotiation checklist |
| `/loadnova:prepare-dispatch-confirmation` | Builds the human-review checklist before a confirmed `dispatch_load` |
| `/loadnova:calculate-route-readiness` | Route math with `calculate_route`, `compare_routes`, and `explain_route` |

## Skill

The `loadnova-dispatch` skill loads automatically when a task matches dispatch, routing, driver, broker-audit, or Education-practice workflows. It carries the operating rules: Work vs Education modes, untrusted-source handling, confirmed-write contract (`create_driver`, `update_driver`, `delete_driver`, `delete_drivers`, `dispatch_load` require explicit confirmation; destructive tools need `confirmed: true`), read-first patterns, and the full tool map.

## Support

Email support@loadnova.app or visit https://loadnova.app.
