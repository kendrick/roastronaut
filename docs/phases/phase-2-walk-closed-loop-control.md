---
title: Roastronaut Phase 2 (Walk) - Closed-Loop Control
status: planned
phase: walk
created: 2026-05-01
related:
  - homeops
  - home-assistant
prerequisite: Phase 1 (Crawl) complete and stable
budget: ~$50 in new parts
---

# Roastronaut Phase 2 (Walk): Closed-Loop Control

## Goals

By the end of this phase, the roaster:

- **Drives the heat gun continuously** via an AC dimmer module instead of just on/off. Anywhere from ~0% to 100% power, set by the ESP32.
- **Follows a stored roast curve** via PID control of bean-mass temperature. You pick a profile from a Home Assistant dropdown ("Ethiopia natural — light", "Brazil washed — medium", etc.) and the controller drives BT to match.
- **Detects first crack automatically** via a microphone and an FFT-based threshold detector running on the ESP32. Fires an HA event so you can see the marker on the live curve.
- **Dumps the beans automatically** at roast end via a servo that rotates the sifter to the dump position.
- **Modulates the cooling fan** for a controlled cooldown profile rather than full-blast forever.

## Non-goals

- No camera-based roast grading; that's Phase 3.
- No LLM-driven critique or post-mortem; Phase 3.
- No auto-loading of green beans; that's Cotton's "going further" rabbit hole and we're not going there.

## What Changes Vs Phase 1

Phase 1 was instrumentation; Phase 2 is actuation. The same hardware stack, with four new actuators and one new sensor:

1. **AC dimmer** in series with the heat gun's hot leg. Replaces the on/off smart plug for normal control. (Smart plug stays in the chain as a hard kill.)
2. **I2S microphone** mounted on the project enclosure, listening for the 200–400 Hz pop signature of first crack.
3. **Servo + linkage** that pushes the sifter from roast to dump position when commanded.
4. **MOSFET-controlled cooling fan** replaces the box fan with a PWM-controlled DC fan, or adds a relay+PWM driver if you want to keep the box fan.

## Shopping List (~$50)

| Item                                                              | Notes                                                                             | ~Price   |
| ----------------------------------------------------------------- | --------------------------------------------------------------------------------- | -------- |
| RobotDyn AC light dimmer module (MOC3021 / BTA16 based)           | Takes 0–5V PWM from ESP32, drives 220V/110V AC up to ~5A. Verify the 110V version | $8       |
| Zero-cross detection input on the dimmer                          | Most modules include this on the same board                                       | included |
| INMP441 I2S MEMS microphone breakout                              | Digital, no analog noise. Plenty of bandwidth for crack detection                 | $5       |
| MG996R metal-gear servo + horn + linkage hardware                 | Strong enough to tip a loaded sifter via lever arm                                | $8       |
| 5V 3A buck converter (separate from ESP32 supply)                 | Powers the servo cleanly without browning out the MCU                             | $5       |
| Logic-level N-channel MOSFET (IRLZ44N or similar) + flyback diode | Cooling fan PWM driver                                                            | $3       |
| 12V DC blower fan, ~80mm, ~50 CFM                                 | Replaces or supplements box fan in cooling station                                | $12      |
| Project hardware (servo bracket, microphone standoff, wiring)     | Misc                                                                              | $10      |

**Phase 2 total: ~$50.**

If you'd rather keep the 20" box fan for cooling and skip the DC fan entirely, omit the last two rows and use a smart plug for fan on/off. You lose continuous cooldown control but save $15.

## Build Sequence

### Step 1: Install the AC Dimmer

The dimmer goes between your GFCI extension cord and the heat gun. The smart plug from Phase 1 stays in front of the dimmer as a hard kill. Both must be on for the gun to receive any power.

1. Cut the heat gun's power cord (or use an inline outlet box). Wire the hot leg through the dimmer's load terminals; neutral and ground pass straight through.
2. Mount the dimmer module **outside** the project enclosure or in its own small box. It dissipates real heat at full load and you don't want it warming your thermocouple amps.
3. Run three wires back to the ESP32: PWM input, zero-cross output, and shared ground.
4. The ESP32 fires the triac on a delay after each zero-cross. That delay is the inverse of duty cycle. ESPHome has a built-in `ac_dimmer` component that does the timing for you.

> **Safety note:** This is line voltage. If you're not comfortable with mains wiring, use a pre-built unit with built-in AC outlets (e.g., a Krida dimmer) instead of a bare module. Cost is similar; safety is dramatically better. The smart plug + GFCI cord stay regardless.

### Step 2: Mount the I2S Microphone

1. Pick a spot on the project enclosure facing the sifter, ideally with a direct line of sight (or near-line) to the bean chamber. Drill a small hole; mount the INMP441 breakout behind it with a foam gasket so the MEMS port is exposed but the board is protected.
2. Run four wires to the ESP32: 3.3V, GND, I2S clock, I2S data. (L/R select pin tied to GND.)
3. Cable shouldn't be more than ~12"; I2S doesn't love long runs.

The ESP32 reads the audio stream, runs a windowed FFT, and watches the 200–400 Hz band. First crack shows up as a sharp transient peak well above the background fan/motor noise floor. You'll calibrate the threshold during your first few Phase 2 roasts.

### Step 3: Add the Dump Servo

This is the biggest mechanical addition since Phase 1. We're motorizing the same pivot motion you currently do by hand.

1. Mount the MG996R servo to the base, behind the rear pivot support, oriented so its horn arm sweeps in the dump direction.
2. Fabricate a short linkage (1/8" steel rod with two clevis ends) between the servo horn and a mounting tab on the back of the sifter body. Drill a small hole through the sifter body for the tab; rivet or screw the tab on.
3. Connect the servo to its own 5V/3A supply (don't share with ESP32; the inrush current when the servo starts moving will brown out the MCU).
4. Signal pin to ESP32 PWM-capable GPIO. Shared ground between servo supply and ESP32 ground.

In software, the servo has just two positions: `roast` (sifter vertical, handle in stop) and `dump` (sifter rotated past 90° to the dump angle). HA exposes this as a single button: **Dump Beans**.

> **Safety upgrade:** Wire the existing hinge-lever microswitch so that lifting the handle _also_ triggers the dump-position via servo. That way if you ever physically lift the sifter mid-roast, the sifter doesn't fight you and the motor still cuts.

### Step 4: Cooling Fan PWM

If you went with the DC blower fan option:

1. Wire the blower's positive lead to 12V supply via the MOSFET's drain.
2. MOSFET source to ground. Gate to ESP32 PWM pin via a small (220Ω) resistor. Pull-down resistor (10kΩ) gate-to-source.
3. Flyback diode across the fan terminals, cathode to +12V.
4. Mount the fan under the cooling rack on a small stand.

If you're keeping the box fan: replace the AC dimmer step's "smart plug" wiring approach for the fan. A second smart plug works fine, you just lose granular speed control.

### Step 5: Calibrate

This is the part most DIY roaster builds skip. **Do not skip this.**

**Dimmer calibration:**

1. With the bean chamber empty and sifter mounted normally, run the heat gun at fixed dimmer levels: 30%, 40%, 50%, 60%, 70%, 80%, 100%.
2. Hold each level for 90 seconds. Record stabilized ET temperature at each point.
3. You should see a roughly linear-ish ET vs duty-cycle curve. Use this as the feed-forward baseline for your PID.

**Microphone calibration:**

1. Run a complete roast with default mic threshold settings. Note the times when _you_ hear first crack and second crack.
2. Compare to the FFT 200–400 Hz band magnitude logged in HA. Adjust threshold so first crack triggers reliably without false positives from motor whine or ambient noise.
3. Repeat for two more roasts of different bean origins to make sure your threshold isn't single-bean overfit.

**PID tuning:**

Start conservative: proportional only, modest gain. Add integral term once you've seen the loop overshoot less than 10°F at first transition. Derivative is rarely necessary for a system this slow; skip unless oscillation persists. Aim for roast-curve tracking within ±5°F of profile.

## Software Stack Additions

You'll do this when you cross the bridge. The high-level shape:

- **ESPHome additions:** `ac_dimmer` component for the heat gun, `servo` component for the dump arm, custom audio component for the I2S mic + FFT (this is the only piece that requires either a custom external component or a small lambda; possibly worth a brief detour into Arduino-side code).
- **HA profiles:** stored as YAML scripts. Each profile is a list of (time, target_BT, fan_speed, motor_speed) tuples. A node-red flow or Python script in HA interpolates between tuples and publishes target_BT to an MQTT topic the ESPHome PID listens on.
- **HA dashboard:** profile dropdown, "Start Roast" button, target curve overlaid on actual BT, RoR gauge, first-crack marker (auto-placed when the FFT detector fires), dump button, cooldown progress.
- **Profile library:** start with three: light, medium, dark. Refine over time as you log more roasts.

## Phase Exit Criteria: When You're Ready for Run

- **5 successful PID-controlled roasts** following stored profiles, with BT staying within ±5°F of target curve through development phase.
- **First-crack detector working** with no more than one false positive per 5 roasts.
- **Dump servo reliable** across at least 10 commanded dumps with no jamming.
- **Cooldown curve controllable**. You can specify a target cooldown rate and the fan modulates to hit it.
- **Roast log retention**. At least 5 full roasts retained in InfluxDB or HA long-term statistics for use as Phase 3 training data.

## Decision Log

| Decision              | Choice                      | Rationale                                                                                                                   |
| --------------------- | --------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Heat control          | AC dimmer                   | Continuous power control is the table-stakes upgrade; on/off PID is laggy and inelegant                                     |
| Mic                   | I2S digital (INMP441)       | Analog mics + ADC pickup motor noise; I2S is essentially noise-free                                                         |
| First-crack detection | FFT threshold on 200–400 Hz | Simple, works, doesn't need ML at this stage. Phase 3 can replace with a trained model if needed                            |
| Dump                  | Servo + linkage             | A solenoid would be faster but requires more force; a servo gives positional feedback for free                              |
| PID location          | On the ESP32 (in ESPHome)   | Latency matters; running the PID loop in HA over network would be sloppy. ESP32 publishes state to HA, HA only sets target. |

## Open Questions to Revisit During Build

- Does the AC dimmer's harmonics interfere with thermocouple readings? If so, add an LC filter or move the dimmer further from the MAX31856 boards.
- Does the I2S mic pick up enough first-crack signal through enclosure walls? Might need an external pickup near the sifter.
- Is MG996R torque enough to dump 12oz of hot beans + sifter weight at the linkage geometry? If marginal, upgrade to a DS3218 (20kg-cm, ~$15 more).
