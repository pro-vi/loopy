# consult-capability (axis primitive)

## Purpose

The frontier-model consult channel available in the host, so the composed
prompt schedules consults only when they exist and degrades gracefully
otherwise (the bodytxt learning). **Environment-detected, not
archetype-varying** — it is a composition *overlay* that changes which sections
Phase 3 emits, and does **not** contribute to the classifier's weighted-Hamming
distance.

## Values

- `tier-0` — no consult channel. Drop scheduled-consult sections; substitute a
  periodic human-look gate; surface the limitation in provenance.
- `tier-1` — human-bridge: the loop emits a consult request as an async handoff
  for the human to relay.
- `tier-2` — single programmatic consult (one MCP tool, one sub-agent, or
  `/second-opinion`).
- `tier-3` — rich consult fabric (Agentify Desktop MCP, PAL, multiple `mcp__*`
  tools, `/agentify`): blind adversarial consults + multi-modal research.

## Detection heuristics

At frontload time, probe the host:

| Found | Tier |
|---|---|
| `mcp__agentify-desktop__*` tools, PAL, or multiple `mcp__*` + `/agentify` | `tier-3` |
| one `mcp__*` tool or sub-agent channel, or `/second-opinion` | `tier-2` |
| only a way to ask the human to relay | `tier-1` |
| nothing | `tier-0` |

## Archetype defaults

| Archetype | Default |
|---|---|
| frontier | detect; `tier-0` if undetected |
| goal | detect; `tier-0` if undetected |
| story | detect; `tier-0` if undetected |
| greenfield | detect; `tier-0` if undetected |

All archetypes default `tier-0` and degrade gracefully — never silently assume
a consult channel exists.

## Composition rules

- `tier-0` **forces** removal of consult-dependent sections (blind comprehension
  reads, scheduled creative consults, adversarial refute panels) and inserts a
  periodic human-look gate; record the degradation in provenance.
- `tier-1` replaces programmatic consults with an async human-bridge handoff.
- `tier ≥ 2` enables the greenfield invariant-8 blind adversarial consult and
  the story Surface-Taste blind read; consult cadence aligns with
  `cadence-shape` boundaries.
