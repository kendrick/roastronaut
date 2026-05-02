---
title: Bill of Materials
status: reference
created: 2026-05-01
related:
  - roastronaut
---

# Bill of Materials

Every part referenced in any phase doc, grouped by the phase that introduces it. Prices are copied from the phase docs (best-effort approximations as of May 2026). All links are placeholders for you to fill in once you have picked specific vendors.

The "Buy" column reads `now` for items you should order when starting that phase, `later` for items that are optional within the phase or depend on a decision you do not have to make yet, and `(have)` for items already on hand from earlier hobby kit or carried forward from an earlier phase. Items marked `(have)` are not in the totals.

## Phase 0: Smoke Test

See [phase-0-smoke-test-hand-cranked-validation.md](phases/phase-0-smoke-test-hand-cranked-validation.md).

| Item | Qty | ~Price | Buy | Notes / link |
| --- | --- | --- | --- | --- |
| Flour sifter (stock) | 1 |  | (have) | Existing kit. [link] |
| 1500W heat gun | 1 |  | (have) | Existing kit. [link] |
| Hinge-lever microswitch | 1 |  | (have) | Existing kit; not used in Phase 0; carried for Phase 1. [link] |
| ESP32 dev board (NodeMCU-32S or DevKitC) | 1 | $8 | now | Same board through Phase 2. [link] |
| Adafruit MAX31856 breakout | 1 | $12 | now | K-type thermocouple amp. [link] |
| K-type thermocouple, beaded tip, ungrounded, glass-braid sleeve, ~36" | 1 | $8 | now | Becomes the BT probe in Phase 1. [link] |
| HA-compatible smart plug (Kasa KP125 or Athom flashable) | 1 | $15 | now | Hard kill, Phase 0 onward. [link] |
| GFCI extension cord, 14 AWG, 15A | 1 | $25 | now | Required every phase. [link] |
| Solderless breadboard plus Dupont jumper kit | 1 | $8 | now | Phase 0 only; saved for next bench project. [link] |
| Stainless wire cooling rack | 1 | $10 | now | Sifter jig support. [link] |
| Bricks (or stacks of hardback books) | 4 | scrounge | now | Sifter jig stand. [link] |

**Phase 0 subtotal (new parts): ~$86 listed, ~$60 with substitutions.** Every item except the breadboard kit ($8) carries forward.

## Phase 1: Crawl

See [phase-1-crawl-instrumented-manual-roaster.md](phases/phase-1-crawl-instrumented-manual-roaster.md).

### Carried forward or already on hand

| Item | Qty | Source |
| --- | --- | --- |
| Flour sifter, heat gun, hinge-lever microswitch | 1 each | (have) |
| 6mm flange couplings | 2 | (have) |
| Greartisan 12V 60RPM gearmotor | 1 | (have) |
| ESP32, MAX31856, K-type probe, smart plug, GFCI cord | 1 each | (carried from Phase 0) |

### Mechanical and structural

| Item | Qty | ~Price | Buy | Notes / link |
| --- | --- | --- | --- | --- |
| ½" project plywood, ~2'×2' | 1 sheet | $15 | now | Base, supports, stops. [link] |
| 2×4 scrap, 6" length | 1 | free or $3 | now | Motor mount block. [link] |
| Aluminum bar, 1/16" × 1½" × 24" | 1 | $12 | now | Brackets, paddles, crank rotator. [link] |
| Aluminum sheet, .020-.025" thick, 12" × 18" | 1 | $10 | now | Heat shield, nozzle bracket, wind-break funnel. [link] |
| ¼" wood dowel, 12" | 1 | $2 | now | Heat gun handle stop. [link] |
| 10D common nail (3") | 1 | $1 | now | Sifter pivot pin. [link] |
| Assorted #6 sheet metal and wood screws, 6-32 machine screws plus nuts | 1 kit | $12 | now | Whole build. [link] |
| Spray paint or clear Deft | 1 | $8 | later | Optional finish; skip if you don't care. [link] |
| Half-sheet perforated baking pan (13" × 18") | 1 | $15 | now | Cooling tray. [link] |
| Box fan, 20" | 1 | own or $20 | now | Cooling station. [link] |
| Wire cooling rack to fit pan | 1 | $8 | now | Air gap under cooling pan. [link] |

### Electronics

| Item | Qty | ~Price | Buy | Notes / link |
| --- | --- | --- | --- | --- |
| K-type thermocouple, beaded tip, ungrounded, glass-braid sleeve | 1 (one new; one carried from Phase 0) | $4 | now | Second probe is for ET. Ungrounded matters; grounded probes confuse cold-junction comp. [link] |
| Adafruit MAX31856 breakout | 1 (one new; one carried) | $12 | now | Don't substitute MAX6675; too noisy at the precision we want for RoR. [link] |
| DRV8871 motor driver breakout | 1 | $7 | now | PWM speed control for the Greartisan. [link] |
| 12V → 5V buck converter, 1A | 1 | $5 | now | Powers ESP32 from same 12V supply as motor. [link] |
| 12VDC 2A power supply | 1 | $10 | now | Powers motor and ESP32 (via buck). [link] |
| ABS project enclosure, ~4" × 3" × 2" | 1 | $8 | now | Mounts to side of base. [link] |
| Panel-mount K-type thermocouple jacks plus plugs | 2 sets | $12 | now | Through-panel disconnect for sensors. [link] |
| Hookup wire, JST-XH connectors, screw terminals, Dupont jumpers | 1 kit | $10 | now | Wiring kit. [link] |

**Phase 1 subtotal (new parts only): ~$140.** Project total to end of Phase 1: ~$200 buying everything new, less with reuse and scrounging.

## Phase 2: Walk

See [phase-2-walk-closed-loop-control.md](phases/phase-2-walk-closed-loop-control.md).

| Item | Qty | ~Price | Buy | Notes / link |
| --- | --- | --- | --- | --- |
| RobotDyn AC light dimmer module (MOC3021 / BTA16) | 1 | $8 | now | Verify the 110V version; takes 0-5V PWM in. [link] |
| INMP441 I2S MEMS microphone breakout | 1 | $5 | now | Digital, no analog noise. [link] |
| MG996R metal-gear servo plus horn and linkage hardware | 1 | $8 | now | Dump servo. [link] |
| 5V 3A buck converter (separate from ESP32 supply) | 1 | $5 | now | Powers servo without browning out the MCU. [link] |
| Logic-level N-channel MOSFET (IRLZ44N or similar) plus flyback diode | 1 | $3 | later | Cooling fan PWM driver; skip if you keep the box fan. [link] |
| 12V DC blower fan, ~80mm, ~50 CFM | 1 | $12 | later | Replaces or supplements box fan; skip if keeping box fan and a smart plug. [link] |
| Project hardware (servo bracket, mic standoff, wiring) | 1 | $10 | now | Misc. [link] |

**Phase 2 subtotal: ~$50 with the DC fan; ~$35 if you keep the box fan and use a second smart plug for cooling on/off.**

## Phase 3: Run

See [phase-3-run-ai-driven-roasting.md](phases/phase-3-run-ai-driven-roasting.md).

| Item | Qty | ~Price | Buy | Notes / link |
| --- | --- | --- | --- | --- |
| ESP32-CAM (AI Thinker) plus FTDI programmer | 1 | $12 | now | Self-contained; separate from the Phase 1/2 ESP32. [link] |
| Aluminum tube, ½" OD, ~16" length | 1 | $6 | now | Boom arm. [link] |
| Aluminum L-bracket plus camera mount hardware | 1 | $5 | now | Foot of boom and camera mount. [link] |
| Small 5V LED ring | 1 | $4 | later | Only if your roasting space's ambient light is poor. [link] |

**Phase 3 subtotal: ~$23 base, ~$27 with the optional LED.**

## Grand Total

| Phase | Subtotal (new parts) | With substitutions |
| --- | --- | --- |
| Phase 0 | ~$86 | ~$60 |
| Phase 1 | ~$140 | ~$130 |
| Phase 2 | ~$50 | ~$35 |
| Phase 3 | ~$23-$27 | ~$23 |
| **Project total** | **~$300-$305** | **~$250** |

Project cost from a standing start, assuming you have the heat gun, sifter, microswitch, gearmotor, and flange couplings noted above. All numbers as of May 2026; expect ±15% drift on commodity electronics.
