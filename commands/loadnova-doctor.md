---
description: Check the Load Nova MCP connection and Education readiness before running operational workflows
---

# /loadnova-doctor

Check the installed Load Nova MCP connection before using operational workflows.

## Workflow

1. Run a `tools/list` sanity check against the `loadnova` MCP server (for example `claude mcp list` or the session's connected Load Nova tools) and count the returned tools.
2. Call `education_status` (no arguments) and read `runtimeMode`, `canStartPractice`, `practiceAvailable`, and `blockedReason`.
3. Report server (staging or prod), transport (streamable HTTP), connected/authenticated state, runtime mode, Education readiness, enabled tool count, and warnings.
4. Group the available tools by read, calculate, and confirmed-write risk.
5. Do not call write tools.

## Output

- Active server, transport, and protocol version (2025-11-25)
- Connected/authenticated state
- Runtime mode (work/education) and Education readiness
- Tool count grouped by risk
- Setup blockers, if any, with the browser reconnect path
- Safest next workflows
