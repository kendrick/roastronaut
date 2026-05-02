---
title: Tooling - Claude Code
status: active
created: 2026-05-01
related:
  - home-assistant
  - claude-code
  - roastronaut
---

# Tooling: Claude Code

This is a coffee-roaster project, not a dev-tools project. But six pieces of the stack are dramatically faster to build with an AI coding agent than without one, and the project is set up so you can use one. Below is what those six pieces are and where to stop.

There is also an explicit trap to avoid. See the bottom of this doc.

## Where Claude Code Earns Its Keep

### ESPHome YAML

ESPHome configs are dense, syntactically picky, and tightly tied to component documentation that is itself in YAML. Claude Code is fluent in ESPHome configs because the format is public and well-documented. Use it to draft the initial YAML for [Phase 0](phases/phase-0-smoke-test-hand-cranked-validation.md) (one MAX31856, one BT sensor) and for [Phase 1](phases/phase-1-crawl-instrumented-manual-roaster.md) (two amps, motor PWM via DRV8871, microswitch input).

The round trip looks like: paste your wiring spec, get a candidate YAML, flash, watch logs, paste error back, iterate. Most of the value is in the second iteration. The first draft is rarely correct because pin assignments depend on your specific ESP32 dev board.

### HA automations and scripts

Same dynamic, different surface. The Phase 0 LLM-critique automation and the [Phase 2](phases/phase-2-walk-closed-loop-control.md) PID-target publishing script are both well-shaped for an AI to draft. The input and output formats are narrow, and the YAML is verbose enough to be tedious to hand-write.

Worth keeping the loop tight: define one automation, get it drafted, paste-test in HA, fix, move on. Don't ask for "all the automations for Phase 2" in one shot.

### Lovelace dashboards

The Lovelace dashboard for [Phase 1](phases/phase-1-crawl-instrumented-manual-roaster.md) (BT/ET line, RoR gauge, motor slider, kill button) is mostly card config. Claude Code can produce a working ApexCharts time-series card from a one-paragraph description faster than you can find the right docs page. Use it.

Don't ask it to invent a custom card. That path leads to hand-holding a JS bundle, which is exactly the trap below.

### Prompt engineering

The four LLM touchpoints in this project all need iteration. See [the prompts subsection below](#llm-prompt-patterns) for the canonical versions; treat those as starting points, not ending points. Claude Code is useful here in a meta way: paste an existing prompt and three example inputs and outputs, ask what is making it generic. The model tends to find its own failure modes faster than you do.

### Custom ESPHome external components

The [Phase 2](phases/phase-2-walk-closed-loop-control.md) audio FFT detector is the one piece of firmware in this project that has no stock ESPHome component. You will be writing a small C++ external component or a non-trivial lambda. Claude Code has been useful for this kind of "C++ glue between ESPHome and an Arduino-side library" work in adjacent projects.

This is the single most likely use case to drift into the trap. Keep it scoped: get the FFT working as a custom component, expose one binary sensor (`first_crack_detected`), stop.

### Roast-log analysis tooling

When you have 30 logged roasts and you start asking questions like "what dimmer duty cycle gives me the cleanest RoR through first crack for African naturals," you will want quick analysis scripts. A Python notebook reading from InfluxDB or the HA recorder, doing a few groupbys and plots, is exactly the shape of script Claude Code generates well in one pass.

This is the [Phase 3](phases/phase-3-run-ai-driven-roasting.md) era and it is optional even then; the LLM post-mortem may already be answering the questions you would write the script for.

## LLM Prompt Patterns

These are the four prompts the project uses for its LLM touchpoints. They live here, in version control, instead of buried in HA configs. Edit them here, then copy into HA. If you change a prompt and like the result, change it here first and treat HA as the runtime.

### Phase 0 post-mortem critique

Used by the HA "Roast Complete" button in [Phase 0](phases/phase-0-smoke-test-hand-cranked-validation.md).

```
Below is the bean-mass temperature curve from a coffee roast I just finished.
The setup is a hand-cranked flour sifter over a 1500W heat gun, batch size
~150g of green beans.

Curve (offset_seconds, °F):
{bt_curve}

Please tell me:
1. Did this look like a reasonable roast curve? (Steady climb? Reasonable RoR?)
2. Can you identify a likely first-crack timestamp from a flattening in the curve?
3. What single thing would you change for the next roast?

Be specific and brief. Three short paragraphs.
```

### Phase 3 vision grading

Called every 10 seconds during an active roast. See [Phase 3](phases/phase-3-run-ai-driven-roasting.md).

```
This image shows coffee beans during a roast, viewed from above through the
top of the chamber. The background may include some shimmer from the rising
heat column.

Estimate the current SCAA roast level on a 1-8 scale (1 = green, 4 = medium,
8 = Italian / Vienna). Look at color uniformity, surface oil, and char.

Respond as JSON only, with no other text:

{"level": int, "confidence": float between 0 and 1, "notes": "one sentence"}
```

### Phase 3 post-mortem critique

Replaces the Phase 0 critique once Phase 3 telemetry is available.

```
Below is the full telemetry from a coffee roast I just finished.

Profile selected: {profile_name}
Bean origin: {bean_origin_notes}
Telemetry (JSON):
{roast_record}

Recent context (my last 5 roasts on similar beans):
{recent_roasts_summary}

Please review the roast:
1. Did it hit the profile? Where did it deviate?
2. Was development time appropriate? (Target 20-25%.)
3. What single change would you make to the profile or operator behavior next time?

Be specific. If the curves look fine, say so plainly.
```

### Phase 3 recipe intelligence

Used when loading new beans and asking for a starting profile.

```
I'm about to roast a new green coffee. Here is what I know about it:

{bean_origin_notes}

My target roast level is {target_level} (SCAA scale).

Below are summaries of my last roasts on similar beans (same origin, similar
processing, similar target level):

{similar_roasts_summary}

Propose a starting profile as a list of (time_seconds, target_BT_F,
target_fan_speed_pct, target_motor_speed_rpm) tuples. Keep it to 8 tuples or
fewer covering charge through drop. Briefly explain the choice in 2-3
sentences after the tuples.
```

## The Trap to Avoid

It is much easier to build tools to build the roaster than it is to build the roaster.

If you spend a Saturday making Claude Code generate ten variations of a roast-log analysis tool before you have ten roasts to analyze, you have not made the project better. You have a nicer notebook for the day you start having data.

Heuristic: for any tooling task, estimate (a) the time to build the tool with Claude Code, and (b) the time the tool will save you across the next four weeks of actual roasting. If (a) > (b), don't build it yet.

This rules out:

- Custom Lovelace cards before [Phase 2](phases/phase-2-walk-closed-loop-control.md) is roasting reliably.
- Roast-log analysis scripts before you have 10+ logged roasts.
- A "smart" prompt-versioning system in HA. The prompts are in this doc; that is enough.
- Anything that involves writing a wrapper around ESPHome.

It permits:

- Anything you would otherwise be hand-writing right now.
- Refactors of YAML or scripts you have already touched twice.
- The audio FFT component, even if it takes a day, because there is no alternative.

## Decision Log

| Decision | Choice | Rationale |
| --- | --- | --- |
| LLM provider for build-time coding | Claude Code | The user already runs it; no reason to bring in a second coding agent for this project. |
| Where the prompts live | This file | Version-controlled, diffable, separable from HA config. HA imports them; this doc owns them. |
| Custom UI for the dashboard | No | Lovelace stock cards do enough. Building a custom React card is the trap. |
| Audio FFT as a custom external component | Yes | No stock component covers it; bounded scope; single output binary sensor. |
| Roast-log analysis tooling | Defer to Phase 3 | Tools without data are guesswork. Wait until 10+ roasts exist. |
| Prompt-tuning workflow | Iterate in this doc, then copy to HA | Version control gives you a real history of what changed. HA is just the runtime. |
