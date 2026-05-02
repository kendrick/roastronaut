---
title: Safety
status: active
created: 2026-05-01
related:
  - homeops
  - home-assistant
  - roastronaut
---

# Safety

This is the consolidated safety story for Roastronaut. Every phase doc has its own safety bits scattered through the build sequence; this doc is the single place those defenses are explained, justified, and turned into a per-roast checklist that supersedes the partial checklists in [Phase 0](phases/phase-0-smoke-test-hand-cranked-validation.md) and [Phase 1](phases/phase-1-crawl-instrumented-manual-roaster.md).

The roaster runs at 1500W into a chamber holding 12 ounces of dry organic material at 400°F. Treat it accordingly.

## Layered Defenses

The roaster has five independent safety layers. Each one assumes the others will eventually fail.

### GFCI extension cord

A 14 AWG, 15A, ground-fault-protected cord between wall and everything else. Introduced in [Phase 0](phases/phase-0-smoke-test-hand-cranked-validation.md) and required for every phase. Trips on a ~5mA leakage to ground in milliseconds.

Protects against: a moisture path (sweat, spilled water, condensation on the heat shield) shorting line voltage to anything you are touching. The roaster lives outside or in an open garage; it will get rained on eventually.

### Smart-plug hard kill

Software-controlled AC plug between the GFCI cord and the heat gun (or, from [Phase 2](phases/phase-2-walk-closed-loop-control.md) onward, between the GFCI cord and the AC dimmer). HA cuts power within ~1 second of any of these:

- BT exceeds 480°F (overtemp; scorching beans, fire risk)
- ET exceeds 600°F (overtemp; the chamber itself is overheated; ET threshold added in [Phase 1](phases/phase-1-crawl-instrumented-manual-roaster.md) once the ET probe exists)
- Hinge-lever microswitch trips during heating (Phase 1+; you have physically lifted the sifter while the gun is on)

The plug stays in the chain forever. From Phase 2 onward the AC dimmer handles normal heat control, but the plug remains as the hard kill.

Protects against: any failure mode the firmware notices but cannot directly resolve. The thermocouple amp says "the beans are 480°F"; the dimmer can be told to drop to 0%, but if anything in that signal path is wrong, the plug yanks mains.

### Microswitch interlock

Hinge-lever microswitch on top of the sifter handle stop, wired to an ESP32 GPIO with pull-up. Introduced in [Phase 1](phases/phase-1-crawl-instrumented-manual-roaster.md). When the handle is lifted (sifter in or near the dump position), the switch opens. Two consequences:

1. ESPHome cuts the motor PWM signal in software (so the paddles stop with the chamber tipped).
2. HA fires a hard kill on the smart plug if the gun is currently powered.

Protects against: the situation where you lift the sifter to look at the beans, or to dump early, while the gun is still blasting heat upward into nothing (or into your hand).

### Dimmer-and-plug chain (Phase 2 onward)

Both the [smart plug](#smart-plug-hard-kill) and the AC dimmer have to allow current for the heat gun to draw any power. The dimmer is the soft, continuous control; the plug is the hard, binary kill. They are wired in series so neither can be bypassed by a software bug in the other.

Protects against: a stuck-on dimmer triac, or a runaway PID loop pinning duty cycle at 100%. The plug doesn't care what the dimmer is doing; it can break the circuit regardless.

### Human keeps drop and dump authority

Per the [Phase 3 decision log](phases/phase-3-run-ai-driven-roasting.md): the AI advises, you decide. The vision grader can highlight a "drop now" banner on the dashboard, but pulling the dump trigger (manual tip in Phase 1, button in Phase 2 and beyond) is always a human action.

Protects against: a model hallucinating that the beans are done at 350°F, or failing to notice a fire in progress, or any other failure mode where confident-sounding software output would be worse than your own eyes and ears.

## Per-Roast Safety Checklist

This supersedes the per-phase checklists in [Phase 0](phases/phase-0-smoke-test-hand-cranked-validation.md) and [Phase 1](phases/phase-1-crawl-instrumented-manual-roaster.md). Run it before every roast regardless of phase.

### Environmental

- [ ] Outside, or in a garage with the door fully open. Smoke is real; the room will smell of roasted coffee for a day.
- [ ] Concrete or stone surface under the roaster. Nothing flammable within 3 feet (no curtains, no cardboard, no wood shavings).
- [ ] Cooling station downwind of the roaster, set up before you load beans.

### Power chain

- [ ] GFCI cord in the chain between wall and everything else.
- [ ] Smart plug present and reporting to HA. Toggle it from HA once; confirm the heat gun's indicator light responds.
- [ ] Phase 2 onward only: AC dimmer wired in series with the plug, not in parallel.

### Sensors and interlocks

- [ ] Both thermocouples reading sensible room temp (~70-90°F) in HA. Phase 0 has only BT; Phase 1+ has BT and ET.
- [ ] Phase 1 onward only: lift the sifter handle; confirm the motor stops and HA shows the interlock as tripped. Set it back down; confirm motor resumes.
- [ ] Smart-plug overtemp automation tested at least once today (a finger on the thermocouple bead with a lighter from a few feet away will do it).

### Kit on the table

- [ ] Phone or tablet with HA dashboard within sight.
- [ ] Oven mitt or silicone glove. The sifter handle gets hot in 12 minutes of cranking, even with the motor in Phase 1 and beyond.
- [ ] Wooden spoon and a metal bowl as a backup dump path if anything goes wrong with the cooling station or servo.
- [ ] Coffee already brewed. The new roast won't be drinkable for at least 12 hours.

### Mental check

- [ ] You have slept and eaten. Tired roasting is bad roasting.
- [ ] You have at least 30 uninterrupted minutes ahead of you. A roast is not pause-able.
- [ ] You actually want to be doing this right now.

## Decision Log

| Decision | Choice | Rationale |
| --- | --- | --- |
| Hard kill mechanism | Smart plug, not bare relay | The plug is HA-native, has its own tested radio stack, and lives outside the project enclosure. A bare relay adds soldering and a single point of firmware failure. |
| GFCI placement | Cord, not panel | The roaster moves between garage and patio. A GFCI cord travels with it and works on any outlet; rewiring panels is out of scope. |
| Overtemp thresholds | 480°F BT, 600°F ET | BT 480 is well above any drinkable drop temp (~445 max) and below the 500°F+ where chaff can autoignite. ET 600 catches a stuck dimmer at full bore before BT can react. |
| Drop / dump authority | Human only | Per the Phase 3 decision log; an LLM grader is not on the hook if the roast catches fire and you didn't notice. You are. |
| Dimmer and plug in series | Required | A stuck triac at full power must be breakable from HA without picking up a soldering iron. |
| Microswitch on motor only (Phase 1) | Yes for Phase 1 | In Phase 1 you operate the gun by hand; the switch only needs to stop the paddles. Phase 2 escalates it to a hard-kill trigger because the gun is now under software control. |
