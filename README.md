```
██████╗  ██████╗  █████╗ ███████╗████████╗██████╗  ██████╗ ███╗   ██╗ █████╗ ██╗   ██╗████████╗
██╔══██╗██╔═══██╗██╔══██╗██╔════╝╚══██╔══╝██╔══██╗██╔═══██╗████╗  ██║██╔══██╗██║   ██║╚══██╔══╝
██████╔╝██║   ██║███████║███████╗   ██║   ██████╔╝██║   ██║██╔██╗ ██║███████║██║   ██║   ██║
██╔══██╗██║   ██║██╔══██║╚════██║   ██║   ██╔══██╗██║   ██║██║╚██╗██║██╔══██║██║   ██║   ██║
██║  ██║╚██████╔╝██║  ██║███████║   ██║   ██║  ██║╚██████╔╝██║ ╚████║██║  ██║╚██████╔╝   ██║
╚═╝  ╚═╝ ╚═════╝ ╚═╝  ╚═╝╚══════╝   ╚═╝   ╚═╝  ╚═╝ ╚═════╝ ╚═╝  ╚═══╝╚═╝  ╚═╝ ╚═════╝    ╚═╝
```

# Roastronaut

A small-scale home coffee roaster, built incrementally from a hand-cranked sifter to a vision-graded, AI-augmented machine.

## What This Is

Roastronaut is a DIY coffee roaster project staged in four phases (a smoke test up front, then a crawl-walk-run progression). The inspiration and base is Larry Cotton's flour-sifter design from a [2019 Make: Magazine article](https://makezine.com/projects/simple-sifter-coffee-roaster/). From there I'll layer on sensor telemetry into Home Assistant, closed-loop heat control with audio first-crack detection, and eventually a vision + LLM layer for real-time roast grading and post-roast critique.

The docs are the artifact; anyone with a Saturday and a heat gun can recreate Phase 0 from scratch.

## Why It Exists

A few overlapping reasons:

- Home roasters in the $200-$500 strike me as mostly mediocre. The really good ones cost $2k+. A DIY build aiming for batch-to-batch consistency could live in a useful gap.
- It's a good project (for me, at least). Mechanical, electrical, firmware, integration, software, some AI bits: one project that touches all of those, contained enough that I can actually finish it.
- The AI-in-the-loop pieces (vision-based grading, LLM post-mortems, recipe intelligence) _aren't really in any commercial home roaster_ as far as I know. Worth seeing how far an off-the-shelf vision model and a logged roast history actually can get us.

I'm explicitly publishing this to be shared. Pull requests, forks, build-log gists, and side-channel "I tried this and it broke" messages are all welcome. That'll get us better roasts, more shared knowledge, and fewer one-off DIY rigs that end up in the bin.

## Current Status

I'm in Phase 0. The cross-cutting docs (safety, schema, tooling, BOM, glossary, troubleshooting) are first-pass complete and the smoke-test build is underway.

| Phase                                                                            | Status  | One-line goal                                                                                              |
| -------------------------------------------------------------------------------- | ------- | ---------------------------------------------------------------------------------------------------------- |
| [Phase 0: Smoke Test](docs/phases/phase-0-smoke-test-hand-cranked-validation.md) | active  | Hand-crank a flour sifter over a heat gun; full sensor / ESPHome / HA / LLM pipeline runs on a breadboard. |
| [Phase 1: Crawl](docs/phases/phase-1-crawl-instrumented-manual-roaster.md)       | planned | Build the motorized sifter roaster with full telemetry to Home Assistant.                                  |
| [Phase 2: Walk](docs/phases/phase-2-walk-closed-loop-control.md)                 | planned | Add an AC dimmer, audio first-crack detection, and a dump servo. PID control to a stored profile.          |
| [Phase 3: Run](docs/phases/phase-3-run-ai-driven-roasting.md)                    | planned | Add an ESP32-CAM, vision-based real-time roast grading, and an LLM post-mortem on every roast.             |

## Repo Layout

```
.
├── README.md            ← you are here
├── LICENSE
└── docs/
    ├── README.md                ← entry point for the docs tree
    ├── safety.md                ← layered defenses and per-roast checklist
    ├── glossary.md              ← BT, ET, RoR, SCAA, the boom, etc.
    ├── roast-log-schema.md      ← canonical schema for one logged roast
    ├── tooling-claude-code.md   ← where the AI coding agent earns its keep
    ├── bom.md                   ← consolidated bill of materials
    ├── troubleshooting.md       ← grows as we hit real problems
    └── phases/
        ├── README.md            ← per-phase orientation
        ├── phase-0-smoke-test-hand-cranked-validation.md
        ├── phase-1-crawl-instrumented-manual-roaster.md
        ├── phase-2-walk-closed-loop-control.md
        ├── phase-3-run-ai-driven-roasting.md
        └── attachments/         ← figures referenced from Phase 1
```

I'll add code (ESPHome configs, HA scripts, Lovelace dashboards, custom external components) in their own directories as each phase reaches that work.

## Credits

The mechanical base of Phase 1 is adapted from Larry Cotton's "Simple Sifter Coffee Roaster" in [Make: Magazine, November 2019](https://makezine.com/projects/simple-sifter-coffee-roaster/). All the figures embedded in Phase 1 are his.

## License

[CC BY-SA 4.0](LICENSE). Build it, modify it, share what you make. If you publish a derivative, you must keep it under the same terms.

Creative Commons officially recommends against CC licenses for software. When meaningful code lands in this repo, I'll add a small license addendum covering that part. For now everything in the repo is documentation, and CC BY-SA 4.0 fits.
