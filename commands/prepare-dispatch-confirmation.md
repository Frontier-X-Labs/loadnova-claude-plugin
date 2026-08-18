---
description: Build the human-review checklist required before dispatch_load writes a real assignment
argument-hint: [driver name or ID, plus load details — stops, rate, equipment, broker]
---

# /prepare-dispatch-confirmation

Prepare the human-review checklist required before any dispatch assignment.

## Arguments

- $ARGUMENTS — the driver (name or reference) and the load details: stops, appointment windows, rate, equipment, broker

## Workflow

1. Resolve the driver with `list_drivers`, then confirm capacity with `get_driver_details` or `check_driver_fit` using the known stops.
2. Verify the route with `calculate_route` (full stops, absolute RFC3339/ISO8601 appointment windows with timezone offsets); use `explain_route` when risks need plain-language justification.
3. Check completeness: `dispatch_load` recalculates and refuses to write below 80% route completeness. List every known field — stops with appointment windows, load ID/reference, rate, equipment, commodity/weight, broker context, notes — and flag what is missing.
4. Present the exact payload: driver ID, stops, rate, commercial metadata, expected side effects, and where the record will appear (from `surfaceImpact`: Chrome Extension and web portal active-route views).
5. Wait for explicit approval. Only then call `dispatch_load` with `confirmed: true` and every known field carried forward; include an `idempotency_key` when retrying after a failure.

## Guardrails

- Never claim a dispatch succeeded unless the tool returns success.
- On a completeness refusal, ask only for the returned missing fields; do not retry silently.
- Never invent missing operational facts — state what is missing and the fastest next check.
