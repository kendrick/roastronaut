---
title: Roastronaut Phase 3 (Run) - AI-Driven Roasting
status: planned
phase: run
created: 2026-05-01
related:
  - homeops
  - home-assistant
prerequisite: Phase 2 (Walk) complete and stable; at least 5 logged roasts in storage
budget: ~$30 in new parts (assumes homelab can host inference; otherwise add API costs)
---

# Roastronaut Phase 3 (Run): AI-Driven Roasting

## Goals

By the end of this phase, the roaster:

- **Sees the beans** via an ESP32-CAM mounted to peek through the sifter screen, streaming frames to HA throughout the roast.
- **Grades the roast in real time** using a multimodal model (local via Ollama, or hosted via API), outputs an estimated [SCAA roast level](../glossary.md#scaa-roast-levels) and a "drop now / wait" recommendation overlaid on the dashboard.
- **Critiques the roast post-mortem**. At the end of each roast, an HA script bundles [BT](../glossary.md#bt-bean-mass-temperature)/[RoR](../glossary.md#ror-rate-of-rise)/audio/vision data and asks an LLM to evaluate the roast and suggest profile adjustments.
- **Suggests starting profiles** for new beans based on origin info plus your own roaster's history with similar beans.

## Non-goals

- No fully autonomous "load beans, walk away" operation. Heat gun trigger is still pulled by you. Beans are still loaded by hand. We're augmenting decisions, not removing humans.
- No replacement of Phase 2's [first-crack](../glossary.md#first-crack) detector. Vision is additive; audio is faster and cheaper.

## What Changes Vs. Phase 2

Phase 2 added control. Phase 3 adds judgment. The hardware delta is small. Almost everything new lives in software:

1. **ESP32-CAM** mounted with a view of the bean chamber.
2. Everything else is software, running on whatever you've already stood up in the homelab, or on hosted APIs if you'd rather not run inference yourself.

## Shopping List (~$25)

| Item                                       | Notes                                                                      | ~Price |
| ------------------------------------------ | -------------------------------------------------------------------------- | ------ |
| ESP32-CAM (AI Thinker) + FTDI programmer   | The classic cheap board. Has its own MCU separate from the Phase 1/2 ESP32 | $12    |
| Aluminum tube, ½" OD, ~16" length          | Boom arm to suspend camera above the sifter, in room air                   | $6     |
| Aluminum L-bracket + camera mount hardware | Foot of boom to base; tip of boom to camera                                | $5     |
| Small 5V LED ring (optional)               | Only if your roasting space's ambient light is poor                        | $4     |

**Phase 3 hardware total: ~$23 base, ~$27 with optional LED.**

> **Why the boom and not a side viewport.** The OV2640 sensor in the ESP32-CAM is rated to ~70°C; the sifter body sits at 150–200°C during roast and a camera mounted close to it would cook in 15 minutes. Top-down on a boom puts the camera in room air, with the hot exhaust column dispersing well before it reaches lens height. You trade chaff drift and some heat shimmer (both manageable) for not destroying the camera every few weeks.

### Recurring Cost Considerations

If you run inference locally (Ollama on the homelab with a vision-capable model like Llama 3.2 Vision or Qwen2-VL): **$0/roast**, plus electricity. This is the right answer for your stack.

If you use a hosted API instead:

- **Roast grading** (vision call every 10s during roast, ~80 calls per roast at typical roast length): roughly $0.10–$0.30 per roast on Claude Haiku 4.5 or GPT-4.1-mini at low resolution.
- **Post-mortem critique** (one text call per roast with full telemetry): roughly $0.02–$0.05 per roast.
- Total: well under $0.50/roast even on hosted, which is fine if you roast a few times a week.

Local-first is more in line with your homeops principles. Use hosted as a fallback while you're standing up local inference, or for the post-mortem call where latency matters less than reasoning quality.

## Build Sequence

### Step 1: Build the Camera Boom

The camera lives in room air, 8–12" above the sifter, looking down through the open top of the chamber between the motor mount and pivot bracket.

1. Cut a length of ½" aluminum tube to roughly 14–16" total. Bend it into an inverted "L" (about 8" vertical, 6–8" horizontal) or fabricate the same shape from two pieces and a 90° corner bracket.
2. Mount the foot of the boom to the back of the base, behind the rear pivot support, well clear of the rising heat plume. The base of the boom should be solid enough not to wobble during paddle motion. A doubled L-bracket bolted through the plywood works fine.
3. The horizontal portion extends forward over the sifter, positioning the boom tip roughly centered over the open top of the chamber.

### Step 2: Mount and Wire the Camera

1. Attach the ESP32-CAM at the boom tip with a small aluminum bracket and two M2 screws, lens pointing straight down into the chamber.
2. Run a thin USB cable along the boom (zip ties or fabric tape), down to a 5V supply at the base. The ESP32-CAM is its own self-contained device: separate Wi-Fi connection, separate firmware, doesn't talk to the Phase 1/2 ESP32.
3. Boot it up, get it on your Wi-Fi, and verify the live feed in HA before going further.

A few things to expect with the top-down view:

- **Chaff drift.** Light bean chaff blows up and out during the roast. Wipe the lens before each session with a microfiber.
- **Heat shimmer.** The rising hot air column distorts the image, especially in the high-heat phase. The vision model handles this fine in practice; it grades color and texture, not geometry, but you'll see it.
- **Framing benefit.** Top-down shows you the whole bean mass at once, which is actually better for color grading than a side window would be.

### Step 3: Lighting (optional)

With the camera looking down from above, ambient room or garage light plus a regular desk lamp aimed at the sifter top is usually enough. Take a few test frames during a dry run (no beans, gun off) and see if the image is usable.

If it isn't:

1. Mount a small 5V LED ring on the boom, alongside the camera, angled into the chamber.
2. Power it from the same 5V supply as the camera.

Skip this step if your roasting space already has decent overhead light.

### Step 4: Configure the Camera in HA

ESP32-CAM exposes itself as either an MJPEG stream or a snapshot endpoint. HA's `generic_camera` integration picks it up as a camera entity. Test that you can view the live feed in Lovelace before continuing.

### Step 5: Set up Local Vision Inference (recommended path)

Standing up Ollama on the homelab is its own task, but the rough shape:

- Pull a vision-capable model. Llama 3.2 Vision (11B) is the practical floor; if your homelab has a GPU, Qwen2-VL or LLaVA-NeXT do better at fine-grained color discrimination.
- Expose an OpenAI-compatible endpoint via Ollama's built-in API.
- HA calls the endpoint via the `rest_command` or `openai_conversation` integrations.

If you're not ready to host inference, point the same automation at a hosted endpoint (Claude API, OpenAI, etc.). The wire format is similar enough that switching later is a one-line change.

### Step 6: Roast-grading Automation

The flow:

1. Every 10 seconds during an active roast, an HA automation grabs the current camera frame.
2. Sends it to the vision model with a prompt like: "These are coffee beans being roasted. Estimate the current SCAA roast level on a 1–8 scale (1=green, 8=Italian/Vienna). Look at color uniformity, surface oil, and char. Respond as JSON: `{level: int, confidence: float, notes: str}`."
3. The model's response is parsed and exposed as `sensor.roastronaut_visual_roast_level`.
4. Lovelace displays the current level alongside BT/RoR. When the level matches the profile's target, a banner reads "**Drop now**" and the dump button highlights.
5. You decide whether to trust it. The decision stays with you.

### Step 7: Post-mortem Critique Automation

At end of each roast (when the dump command fires), an HA script:

1. Bundles the full telemetry: BT curve, [ET](../glossary.md#et-environmental-temperature) curve, RoR over time, mic-detected first-crack timestamp, dimmer duty cycle history, motor speed, camera-detected level history, profile that was selected.
2. Sends it as JSON to an LLM with a prompt asking for a roast review: was development time appropriate, did the roast hit profile, what would you adjust next time.
3. Stores the response as a markdown note attached to the roast log entry.

This is probably the most novel piece of the build. No commercial home roaster I know of does it.

> **Prompt tip:** Give the model your last 5 roasts as context. It can compare and notice trends: "your last three roasts have all crashed RoR right after first crack; consider lowering heat earlier."

### Step 8: Recipe Intelligence

When you load new beans, paste origin info into a HA input field:

> "Ethiopia Yirgacheffe natural, washed by [farm], 1900m elevation, screen 15+, harvested Nov 2025"

A HA script sends this to the LLM along with your roaster's roast history filtered for similar profiles (same origin, same processing method, same roast level target). The LLM proposes a starting profile.

You can either accept the proposed profile as-is, edit it before saving, or ignore it. Like the grading recommendation, the call stays with you.

The bigger your log gets, the less generic the suggestions become.

> **Claude Code:** All three Phase 3 prompts (vision grading, post-mortem, recipe intelligence) are version-controlled in [tooling-claude-code.md](../tooling-claude-code.md#llm-prompt-patterns). Edit there, then sync to HA.

## Privacy and Self-Hosting Notes

If you're staying local-first (which you should be):

- ESP32-CAM stream stays on the LAN. Don't expose it externally.
- All inference runs on your homelab. No frames leave your network.
- Roast logs stay in your InfluxDB / HA recorder. Nothing sent to vendors.

If you're using hosted APIs as a transition:

- The vision API will see frames of beans roasting in your chamber. There's nothing privacy-sensitive in the frames themselves, but be aware they leave your network.
- The post-mortem API call sends your roast telemetry. Same: nothing personal, but it does leave your network.
- Use Anthropic or OpenAI's data-retention-off settings if you care.

Plan to migrate to fully local once your homelab inference setup is stable.

## Phase Exit Criteria: When You Can Call This "Done"

There isn't really a "done" for Phase 3. Once the AI layer is in, you'll keep refining prompts, models, profiles, and dashboard ergonomics for as long as you keep roasting. Reasonable signals you've reached a stable plateau:

- **Vision grading agrees with your manual assessment** more often than not on a roast-by-roast basis. Aim for ~80% agreement on roast level within ±1 SCAA point.
- **Post-mortem critiques produce at least one actionable suggestion** in maybe half of them. The other half will be "looks fine," which is also useful.
- **Recipe-suggested profiles produce drinkable coffee** on the first try when you accept them as-is.
- **Latency is acceptable**. Vision call returns in under 5 seconds, post-mortem in under 30. Local inference should hit these comfortably; hosted is faster.

## Decision Log

| Decision           | Choice                                               | Rationale                                                                                                                                                  |
| ------------------ | ---------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Camera hardware    | ESP32-CAM                                            | Cheapest path to a Wi-Fi camera; AI Thinker variant is well-supported in ESPHome                                                                           |
| Camera placement   | Top-down on a boom, in room air                      | OV2640 is rated to ~70°C and the sifter wall sits at 150–200°C during roast. Side viewport would cook the sensor. Boom puts it in ambient air with margin. |
| Inference location | Local (Ollama) preferred, hosted as fallback         | Aligns with privacy-first principle; cost is zero ongoing                                                                                                  |
| Vision model       | Llama 3.2 Vision baseline, Qwen2-VL if GPU available | Both handle the task; Qwen2-VL is sharper on color                                                                                                         |
| Grading interval   | Every 10 seconds                                     | Roasts move fast at the end; 10s gives 6 evaluations per minute, enough to catch the drop point                                                            |
| Decision authority | [Human keeps drop and dump](../safety.md#human-keeps-drop-and-dump-authority) | This is the line. The AI advises, you decide. Family safety and "the AI made a mistake" recovery both matter. |

## Future Ideas (Run+1, When bored)

These belong in a separate doc if/when you actually pursue them:

- **Bean weight loss tracking**. Load cell under the sifter measures real-time mass loss as moisture and chaff escape. Combined with BT, gives a much better development-end-point predictor than visual alone.
- **Per-roast CSV export** to an inbox for archival or sharing with the home-roasting community.
- **Automated profile generation**. Train a small model on your roast log to propose new profiles for unseen origins.
- **Voice control**. "Hey, roast a Brazilian medium" via existing HA voice setup.
- **Aroma sensor** (TGS-series MQ gas sensor) to add a smell dimension to the dataset. Probably overkill but tempting.

## Credits & References

- Phase 1 mechanical design adapted from Larry Cotton's Make: article (see [phase-1-crawl-instrumented-manual-roaster.md](phase-1-crawl-instrumented-manual-roaster.md)).
- Ollama: https://ollama.com
- ESPHome ESP32-CAM component: https://esphome.io/components/esp32_camera.html
- SCAA roast level reference: https://sca.coffee/research/coffee-standards
