---
title: Troubleshooting
status: stub
created: 2026-05-01
related:
  - home-assistant
  - roastronaut
---

# Troubleshooting

A live document, not a planning artifact. This file grows entry-by-entry as we hit real problems during the build. Each entry is short: symptom, what was actually wrong, fix.

The intent is that future you (and anyone else who builds this) can search by symptom and find a fix faster than re-debugging from scratch. Sections below are placeholders organized by symptom area; one example entry per section shows the intended format. Replace each example once you have a real entry to put there.

## Sensor Readings

### Example: BT spikes 200°F instantaneously, then falls back

**Symptom:** the BT trace in HA shows a one-sample spike to 600+°F, then returns to a normal value within 1-2 seconds.

**Cause:** in this example, the K-type connector at the panel-mount jack wasn't fully seated. The brief disconnection let cold-junction compensation report nonsense for a single read.

**Fix:** reseated the panel-mount jack; added a small zip-tie strain relief on the cable behind the panel.

## Motor Behavior

### Example: paddle stalls intermittently mid-roast

**Symptom:** motor speed drops to zero for 1-2 seconds, then resumes.

**Cause:** in this example, beans wedged between paddle tip and screen rim because clearance was set to 1/16" (Cotton's spec) instead of the project's recommended 1/8".

**Fix:** raised the paddle clearance to 1/8" per the Phase 1 upgrade note.

## Heat Control

### Example: ET tracks dimmer commands but BT lags 90+ seconds

**Symptom:** dimmer is commanded to 80%, ET responds within 20 seconds, BT crawls.

**Cause:** in this example, the BT probe had slipped and was no longer in the bean mass; it was reading air temperature inside the chamber.

**Fix:** reseated the probe through the side hole; verified position with a manual stir during charge.

## HA and ESPHome Integration

### Example: ESPHome sensor disappears from HA after a few hours

**Symptom:** `sensor.roastronaut_bean_temp` shows "unavailable" in HA while the ESP32 is reachable on the network.

**Cause:** in this example, the ESPHome API was timing out because a long-running automation in HA was blocking the recorder thread.

**Fix:** restructured the automation to be non-blocking; raised the API connect timeout in the ESPHome config.

## LLM Touchpoints

### Example: post-mortem returns the same generic critique regardless of input

**Symptom:** every roast critique reads "your curve looks reasonable, consider lowering heat earlier."

**Cause:** in this example, the prompt template wasn't substituting `{bt_curve}` correctly. The LLM was being asked to critique a literal placeholder string.

**Fix:** verified template variables in HA; added a sanity log of the rendered prompt body before each call.
