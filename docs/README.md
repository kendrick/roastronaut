---
title: Roastronaut Docs
status: reference
created: 2026-05-01
related:
  - roastronaut
---

# Roastronaut Docs

Roastronaut docs are split into two layers: cross-cutting reference and operational docs at this top level, and one doc per implementation phase under [phases/](phases/).

If you're landing fresh, start with [phases/README.md](phases/README.md) for the four-phase plan, or jump straight to a cross-cutting doc below.

## Cross-Cutting Docs

| Doc                                              | Status    | What it's for                                                                                                                           |
| ------------------------------------------------ | --------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| [safety.md](safety.md)                           | active    | The layered safety story plus the per-roast checklist that supersedes the partial checklists in the phase docs.                         |
| [glossary.md](glossary.md)                       | reference | Coffee-roasting and project-specific terms (BT, ET, RoR, first crack, the boom, etc.). Phase docs link first occurrences here.          |
| [roast-log-schema.md](roast-log-schema.md)       | active    | The canonical, additive schema for one logged roast across all four phases. Storage backend (InfluxDB vs HA recorder) is the open call. |
| [tooling-claude-code.md](tooling-claude-code.md) | active    | Where Claude Code earns its keep on this project, plus the four LLM prompts (version-controlled here, not in HA configs).               |
| [bom.md](bom.md)                                 | reference | Per-phase bill of materials with running totals. `[link]` placeholders to fill in once vendors are picked.                              |
| [troubleshooting.md](troubleshooting.md)         | stub      | Lives empty for now. Grows entry-by-entry as we hit real problems during the build.                                                     |

## Phase Docs

Four sequential phases (smoke test, then crawl / walk / run). See [phases/README.md](phases/README.md). Phase 0 is currently active; Phases 1-3 are planned.
