---
description: Draft a broker reply grounded in calculated Load Nova route economics
argument-hint: [intent — ask missing info, quote rate, confirm pickup, decline, or custom]
---

# /draft-broker-reply

Draft a broker reply from reviewed Load Nova route facts.

## Arguments

- $ARGUMENTS — intent (ask for missing info, quote a rate, confirm pickup, decline, or custom) plus any load context

## Workflow

1. Gather facts: pickup/dropoff stops and appointment windows from the conversation or $ARGUMENTS; driver context from `list_drivers` when relevant.
2. Call `calculate_route` with the known stops for trusted miles, hours, and weather.
3. Work the economics: if no rate is supplied, state that a rate is required for RPM/margin math and ask for one; otherwise evaluate rate-per-mile and gross margin against calculated miles.
4. Draft a concise reply matching the intent, with assumptions and a short review checklist for the human.

## Negotiation checklist

- Rate per mile vs calculated deadhead and loaded miles
- Appointment windows and detention terms
- Equipment and weight confirmation
- Broker MC number and `audit_broker_domain` result when known

## Guardrails

- A draft is never a send. Present it for human editing.
- Do not call write tools; sending email is not available over the Load Nova MCP server.
