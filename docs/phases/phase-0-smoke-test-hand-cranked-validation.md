---
title: Roastronaut Phase 0 (Smoke Test) - Hand-Cranked Validation
status: planned
phase: zero
created: 2026-05-01
related:
  - homeops
  - home-assistant
prerequisite: None — this is the entry point
budget: ~$55–$65 in new parts (all carries forward to Phase 1)
---

# Roastronaut Phase 0 (Smoke Test): Hand-Cranked Validation

## Why This Exists

Before sinking a weekend into Cotton's mechanical build, prove the _software stack_ works end-to-end on the simplest possible mechanical setup. Phase 0 is a smoke test, not a real roaster. You hand-crank a stock flour sifter over a heat gun for ~10 minutes, while the eventual sensor → ESPHome → HA → LLM pipeline runs on a breadboard nearby.

If anything in that chain is broken or annoying, you find out on a $60 setup, not after fabricating an aluminum pivot bracket.

It also answers a question that no amount of planning can answer: **do you actually enjoy roasting coffee enough to motorize it?**

## Goals

By the end of Phase 0:

- You have roasted at least one batch of coffee to drinkable result, hand-cranking the sifter over a heat gun.
- One K-type thermocouple is reading [bean-mass temperature](../glossary.md#bt-bean-mass-temperature), exposed to Home Assistant via ESPHome.
- A Lovelace card shows live BT during the roast.
- A [smart plug](../safety.md#smart-plug-hard-kill) between the wall and the heat gun is HA-controlled, with an automation that kills it if BT exceeds 480°F.
- **An LLM critiques each roast post-mortem.** At end of roast, an HA automation grabs the BT curve, sends it to Claude (or whichever provider) via API, and displays a written critique back in HA.

That last bullet is the centerpiece. The whole point of Phase 0 is to flush integration risk on the AI touchpoint before it's buried under fabrication work.

## Non-goals

- No motor; you crank by hand.
- No second thermocouple, no audio, no vision, no PID, no profiles.
- No mechanical fab. No drilling holes in the sifter. No plywood base. No enclosure.
- No closed-loop heat control. The heat gun's own switch is your only control over heat.

## Shopping List (~$60)

You already have: flour sifter, heat gun, microswitch (not used in Phase 0).

| Item                                                                  | Notes                                      | ~Price         |
| --------------------------------------------------------------------- | ------------------------------------------ | -------------- |
| ESP32 dev board (NodeMCU-32S or DevKitC)                              | Same one you'll keep using through Phase 2 | $8             |
| Adafruit MAX31856 breakout (or clone)                                 | Thermocouple amp; carries forward          | $12            |
| K-type thermocouple, beaded tip, ungrounded, glass-braid sleeve, ~36" | Carries forward as your eventual BT probe  | $8             |
| HA-compatible smart plug (Kasa KP125 or Athom flashable)              | Carries forward as the Phase 1 hard-kill   | $15            |
| GFCI extension cord, 14 AWG, 15A                                      | Mandatory regardless of phase              | $25            |
| Solderless breadboard + Dupont jumper kit                             | Phase 0 only; saved for next bench project | $8             |
| Stainless wire cooling rack + 4 bricks (or 4 stacks of books)         | Sifter jig                                 | $10 / scrounge |

If you have a GFCI cord and a wire rack on hand, you can do this for closer to $50.

**Phase 0 hardware total: ~$60.** Every dollar except the breadboard kit ($8) carries forward to Phase 1.

## Setup

### Step 1: Sifter Jig

Goal: the sifter sits about 4–6" above the heat gun nozzle, hands-free, so you can crank with one hand and watch your phone with the other.

1. Set the wire cooling rack across two stacks of bricks (or two stacks of hardcover books, whatever's stable). Aim for a gap below the rack tall enough for the heat gun to stand on its end with nozzle pointing up, plus 4–6" clearance to the rack.
2. Stand the heat gun under the rack, nozzle up, gun resting on its base. Most heat guns have a flat base for exactly this. The Harbor Freight gun Cotton specifies stands fine.
3. Place the sifter on the rack, centered over the nozzle. The sifter's flat bottom (just outside the screen ring) should rest on the rack with the screen exposed to airflow from below.
4. Verify by sighting from the side that hot air will flow up through the screen, not around the sifter.

This is the sketchy part of the build. **Do this outside or in a garage with the door fully open.** Concrete or stone surface, nothing flammable within 3 feet.

### Step 2: Thermocouple Placement

The K-type probe goes through the sifter's lid opening (where the agitator's crank rod comes through) so the bead sits in the bean mass during the roast.

1. Run the probe down through the lid opening alongside the existing crank shaft. The bead should reach roughly 1" above the screen (i.e., it'll be buried in the bean mass once you load beans).
2. Tape or zip-tie the probe sheath to the sifter handle so it stays put when you tilt and crank. Glass-braid leads tolerate the heat fine.
3. The other end of the probe lead exits the sifter and runs to the breadboard on a nearby table.

> **This doubles as a Phase 1 dry run.** Notice how the probe wants to sit, what gets in the way, where the lead naturally routes. When you do the permanent install in Phase 1, you'll already know your preferred entry geometry.

### Step 3: Breadboard the Electronics

No soldering. Everything Dupont-jumpered on a breadboard.

- ESP32 dev board on the breadboard, USB cable to your laptop or a 5V power brick.
- MAX31856 breakout adjacent to the ESP32. Wire SPI (MOSI, MISO, CLK, CS) plus 3.3V and GND between them per the breakout's pinout.
- K-type probe leads connect to the MAX31856's TC+ and TC- screw terminals.
- Smart plug stays in its packaging until Step 5.

Set the breadboard on a heat-safe surface a couple feet from the sifter, well clear of any heat plume.

### Step 4: ESPHome Firmware

Crossing this bridge later per your previous direction, but the shape:

- One ESPHome YAML config defining the ESP32 + MAX31856.
- Exposes one sensor: `sensor.bean_temp`.
- Auto-discovered by HA the moment the ESP32 boots on your Wi-Fi.

That's the entire Phase 0 firmware. Maybe 30 lines of YAML.

> **Claude Code:** ESPHome YAML is the canonical Claude Code use case for this project. See [tooling-claude-code.md](../tooling-claude-code.md#esphome-yaml).

### Step 5: Smart Plug + Safety Automation

1. Plug the [GFCI cord](../safety.md#gfci-extension-cord) into the wall.
2. Plug the smart plug into the GFCI cord.
3. Plug the heat gun into the smart plug.
4. Pair the smart plug with HA via whichever integration matches the model you bought (Kasa, Tasmota, ESPHome, etc.).
5. Build one HA automation, in plain English: **"If `sensor.bean_temp` rises above 480°F, turn off the smart plug."** That's the entire safety story.

Test it before any beans go in: manually heat the probe with a lighter from a few feet away, watch HA cut the plug. Don't proceed until that test passes.

### Step 6: Lovelace Card

One ApexCharts card (or built-in History Graph card if you'd rather not install HACS for this). Plot `sensor.bean_temp` over the last 15 minutes. That's the dashboard.

Add a single button card labeled **"Roast Complete"** that calls a script you'll define in Step 7.

### Step 7: The AI Touchpoint (the Actual Point of Phase 0)

This is the integration piece you haven't built before. Worth doing carefully.

Crossing the wiring bridge later, but the shape:

1. **Get an API key.** Anthropic API recommended given the rest of your stack alignment. Console at https://console.anthropic.com. A few dollars of credit covers months of Phase 0 use.
2. **Define a HA `rest_command`** that POSTs to `https://api.anthropic.com/v1/messages` with your API key in the header and a JSON body containing the model name, max tokens, and a single user message.
3. **Define a HA script** triggered by the "Roast Complete" button. Steps:
   - Query the recorder for the last 15 minutes of `sensor.bean_temp` history.
   - Format that history as a compact text representation: e.g., a list of (minute:second, temp) pairs or a 60-second-interval summary.
   - Build a prompt: "Below is the bean-mass temperature curve from a coffee roast I just finished. Did this look like a reasonable curve? Identify first crack timing if you can spot it. What would you change next time? Be specific and brief."
   - Call the `rest_command` with that prompt.
   - Take the response and write it to a `persistent_notification` or to a markdown file on the HA host.

The first time this works end-to-end, take a screenshot. That moment is what Phase 0 exists for.

> **Claude Code:** The post-mortem critique prompt is canonical and version-controlled in [tooling-claude-code.md](../tooling-claude-code.md#phase-0-post-mortem-critique). Edit there, then sync to HA.

## First-roast Checklist

- [ ] Outside or garage door fully open.
- [ ] Concrete/stone surface under the jig. Nothing flammable in a 3-foot radius.
- [ ] GFCI cord between wall and smart plug.
- [ ] Sifter jig stable. Push-test it before turning anything on.
- [ ] Probe reading something sensible (~70–90°F room temp) in HA before you start.
- [ ] Smart plug kill verified earlier in the day.
- [ ] Phone or laptop with HA dashboard within sight.
- [ ] Oven mitt or silicone glove for the cranking hand. **The sifter handle gets hot!** It sits in the rising exhaust column for 10+ minutes.
- [ ] Wooden spoon and a metal bowl on the side as a backup if you need to dump beans somewhere fast.
- [ ] Coffee already brewed… you won't be drinking the new roast for at least 12 hours.

## The Roast Itself

1. Load 1 cup (~150g) of green beans into the sifter through the lid opening.
2. Turn the heat gun on. Low setting if it's above 60°F outside, high if cooler.
3. Crank the sifter handle at roughly **one revolution every 2–3 seconds**. Steady rhythm, both directions are fine, just keep the beans moving.
4. Watch BT climb on your phone. You should see a steady rise from ambient toward 200°F over the first 4–5 minutes (the drying phase), then continued climb.
5. At ~395–410°F BT you should hear the [first crack](../glossary.md#first-crack): a popping sound like distant popcorn. Note the time mentally.
6. Keep cranking. Beans go from tan to brown. Decide your stop point: light roast just past first crack, medium at ~430°F, dark approaching second crack at ~440–450°F.
7. When you're done, **turn the heat gun off first**, then dump the beans into the metal bowl and stir vigorously to cool them. Or pour onto the cooling rack and shake.
8. Tap the **"Roast Complete"** button on your HA dashboard.
9. Wait 10–30 seconds. Read the LLM critique.

Your arm will be tired. That's intended.

## Phase Exit Criteria: When Phase 0 Is Done

All of the following:

- [ ] At least **one drinkable batch** roasted. ("Drinkable" is a low bar… not burnt, not raw. It does not need to be good.)
- [ ] BT curve renders smoothly in HA Lovelace during a roast, with at least 1Hz update rate.
- [ ] Smart-plug kill automation tested at least once with a simulated overtemp.
- [ ] **LLM post-mortem critique works end-to-end.** You hit the button, you get back a coherent paragraph about your roast curve.
- [ ] You're not tired of roasting coffee.

If the last bullet fails—if you've done two batches and you're already over it—that's a perfectly valid Phase 0 outcome. Better to learn that for $60 than after Phase 1.

## What You've Validated for Phase 1

By the time Phase 0 is done, you've confirmed:

- Your specific ESP32 board flashes ESPHome correctly.
- MAX31856 + your specific K-type probe wiring works.
- HA discovers the sensor with no manual config.
- Lovelace renders a sub-minute-resolution time series adequately.
- Your specific smart plug integrates with HA.
- The HA → Anthropic API rest_command path works.
- Your API key works and your account has billing set up.
- Your preferred thermocouple entry point through the sifter is workable.
- You actually want to motorize this thing.

That's a meaningful chunk of integration risk flushed before fabrication starts.

## Decision Log

| Decision           | Choice                               | Rationale                                                                                                                   |
| ------------------ | ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| Mechanical chamber | Stock sifter, hand-cranked           | Better heat distribution than a bowl; same chamber geometry as Phase 1 so curves carry over; less arm fatigue than stirring |
| Sifter support     | Wire rack + bricks                   | Hands-free, scrounge-able, no fab                                                                                           |
| Probe entry        | Through lid opening, taped to handle | Doubles as a dry run for Phase 1's permanent install                                                                        |
| AI touchpoint      | Required for phase completion        | Whole point of Phase 0 is flushing integration risk; the LLM call is the riskiest unfamiliar piece                          |
| LLM provider       | Anthropic API                        | Aligns with rest of homeops stack; <$1 in credit covers Phase 0                                                             |
| Soldering          | None                                 | Breadboard everything; saves a tooling decision for later                                                                   |
| Cooling            | Metal bowl + stir                    | Phase 1's cooling station is overkill for Phase 0 batch sizes                                                               |

## Open Questions to Revisit During Phase 0

- Does the BT signal at 1Hz update rate look smooth enough, or do you need to bump to 4Hz / add filtering?
- Is the LLM critique actually useful, or generic? If generic, the prompt needs work.
- Does the unmotorized agitator distribute beans well enough for your taste, or is heat gun direct-impingement (no agitator at all, just shake the sifter) actually better at this scale?
- Does the sifter handle get hot enough to need more than an oven mitt? (Anecdotally yes after ~12 minutes.)
- How loud is first crack relative to the heat gun fan? (Phase 2 audio detection viability.)
