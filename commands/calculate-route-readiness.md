---
description: Calculate whether a route is ready to dispatch using Load Nova as the trusted backend
argument-hint: [pickup and dropoff stops, optionally appointment windows and truck details]
---

# /calculate-route-readiness

Calculate whether a route is ready to dispatch using Load Nova as the trusted backend.

## Arguments

- $ARGUMENTS — pickup/dropoff/driver-location facts, appointment windows, truck details, and known rate

## Workflow

1. Call `calculate_route` once pickup and dropoff are known. Convert relative times ("tomorrow at 10:00") to absolute RFC3339/ISO8601 timestamps with timezone offset.
2. If alternatives exist, call `compare_routes` for a side-by-side miles, hours, and warnings table.
3. Call `explain_route` when the user asks why the route is risky or needs dispatcher reasoning; keep it to 2-3 bullets.
4. Report weather conditions or warnings along the route in a brief section. If no rate was supplied, state that a price is required to calculate economics (RPM, gross margin) and ask for one.
5. Separate backend-calculated facts from assumptions and untrusted source data.
6. Return a readiness verdict, blockers, and next actions.

## Guardrails

- Read-only: do not create routes, assign drivers, patch snapshots, or dispatch.
- If the user asks for a write, run `/prepare-dispatch-confirmation` first.
