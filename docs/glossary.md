---
title: Glossary
status: reference
created: 2026-05-01
related:
  - homeops
  - roastronaut
---

# Glossary

Coffee-roasting and project-specific terms used across the phase docs. Every term has an anchor that the phase docs link to on first occurrence.

### BT (bean-mass temperature)

The temperature of the bean mass during a roast, measured by a probe physically embedded in the beans. The single most important sensor reading for following a roast curve.

### ET (environmental temperature)

The temperature of the air inside the roasting chamber, measured by a probe in the airstream above the heat source and below the bean mass. Used alongside BT to spot heat-application problems before they show up in the beans.

### RoR (rate of rise)

The first derivative of bean temperature, expressed in °F per minute in this project. Roasters live and die by RoR; a flat or crashing RoR after first crack usually means a stalled or baked roast.

### SCAA roast levels

The Specialty Coffee Association's eight-point scale running from 1 (green) through 8 (Italian / Vienna). This is the target output of the Phase 3 vision grader; common waypoints are light at 2-3, medium at 4-5, dark at 6-7.

### First crack

The audible popping that happens around 395-410°F BT as bean cell walls rupture and release moisture. Marks the start of the development phase; every drinkable roast happens at or after first crack.

### Second crack

A quieter, sharper crackling around 440-450°F BT as bean structure breaks down further and oils migrate to the surface. Past second crack you are into dark / Italian / Vienna territory; past second crack by much, you have charcoal.

### Development time ratio

The fraction of total roast time spent between first crack and drop. A common target is 20-25%; lower tends to read sour, higher tends to read flat or baked.

### Charge temperature

The roaster's ET at the moment beans are loaded. Higher charge means a faster early ramp; in this project's small batch sizes the difference matters less than for commercial drums but still shows up.

### Turning point

The lowest BT after charging, when the cold beans stop pulling heat out of the chamber faster than the gun puts it in. Comes maybe 90 seconds in; the climb from turning point onward is what you are actually controlling.

### Drop temperature

The BT at the moment the beans leave the chamber. The single number that most reliably correlates to perceived roast level.

### Smoke test

The Phase 0 setup: a hand-cranked sifter over a heat gun, used to validate the software stack end-to-end before fabricating anything. Borrowed from electronics, where you power on a new circuit and wait to see if it smokes.

### Crawl, walk, run

The three implementation phases after Phase 0. Phase 1 instruments a manual roaster, Phase 2 closes the control loop, Phase 3 adds vision and LLM judgment. Each phase has to be stable before the next one is worth starting.

### BT probe

The K-type thermocouple physically embedded in the bean mass. In Phase 0 it tapes to the lid and dangles in; from Phase 1 onward it goes through a 1/8" hole in the sifter body and is anchored with a P-clamp.

### ET probe

The K-type thermocouple in the airstream between the heat gun nozzle and the sifter screen. Added in Phase 1; Phase 0 has only a BT probe.

### The boom

The aluminum tube introduced in Phase 3 that holds the ESP32-CAM 8-12" above the sifter. Puts the camera in room air so the OV2640 sensor doesn't cook in the rising exhaust column.

### Hard kill

The smart plug in series with the heat gun. HA can cut it instantly on overtemp or interlock trip; it stays in the chain even after Phase 2 introduces the AC dimmer for normal heat control. See safety.md for the full layered story.

### Drop

The act of dumping beans out of the roasting chamber at end of roast, into the cooling station. Phase 1 you tip the sifter by hand; Phase 2 a servo does it on command; Phase 3 still leaves the trigger to you.

### Dump

Synonymous with drop in this project. The HA button label and the Phase 2 servo command both use "dump".

### Cooling station

The half-sheet pan over a wire rack over a box fan, downwind of the roaster, where beans cool from ~400°F to handleable in 60-90 seconds. Phase 2 optionally adds a PWM-controlled DC blower in place of the box fan to modulate cooldown rate.
