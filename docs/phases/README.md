---
title: Roastronaut Phases
status: reference
created: 2026-05-01
related:
  - homeops
  - roastronaut
---

# Roastronaut Phases

The build is staged in four phases, each with a stable exit criterion before the next one is worth starting. The first three follow a crawl-walk-run progression; Phase 0 is a smoke test that runs ahead of all of them to flush software-stack risk before any mechanical fabrication happens.

| Phase | Status | One-line goal |
| --- | --- | --- |
| [Phase 0: Smoke Test](phase-0-smoke-test-hand-cranked-validation.md) | active | Hand-crank a flour sifter over a heat gun while the full sensor / ESPHome / HA / LLM pipeline runs on a breadboard. |
| [Phase 1: Crawl](phase-1-crawl-instrumented-manual-roaster.md) | planned | Build Larry Cotton's motorized sifter roaster with full BT, ET, and motor telemetry to Home Assistant. |
| [Phase 2: Walk](phase-2-walk-closed-loop-control.md) | planned | Add an AC dimmer, audio first-crack detection, and a dump servo. PID-control the heat to a stored profile. |
| [Phase 3: Run](phase-3-run-ai-driven-roasting.md) | planned | Add an ESP32-CAM, vision-based real-time roast grading, and an LLM post-mortem on every roast. |

Each phase doc has its own goals, non-goals, shopping list, build sequence, exit criteria, and decision log. Open questions specific to a phase live in that phase doc; cross-cutting questions live in the cross-cutting docs.

For cross-cutting reference (safety, glossary, BOM, tooling, schema), see [../README.md](../README.md).

The [attachments/](attachments/) directory holds the figures referenced from Phase 1's mechanical build sequence.
