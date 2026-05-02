---
title: Roast Log Schema
status: active
created: 2026-05-01
related:
  - home-assistant
  - roastronaut
---

# Roast Log Schema

The canonical record of what one roast contains, across all four phases. The schema is additive: every field introduced in an earlier phase keeps its shape in later phases. New phases only add fields. A Phase 0 roast can be opened in a Phase 3 dashboard without anything breaking.

## Why a Schema at All

The roast log is the only thing the project produces that you cannot reconstruct from current state. Recipes, post-mortems, vision grading, and any future model fine-tuning all read from it. If the shape changes between phases, every consumer breaks every time you upgrade the build. So we pin it now, while there is nothing to migrate.

The phase docs assume this schema exists; this doc defines it.

## Fields by Phase

### Phase 0 fields

Established in [Phase 0](phases/phase-0-smoke-test-hand-cranked-validation.md). These are the ones that must keep their shape forever.

| Field | Type | Source | Notes |
| --- | --- | --- | --- |
| `roast_id` | string (ULID) | HA script | Generated at roast start. |
| `started_at` | ISO 8601 timestamp | HA | Wall-clock at start. |
| `ended_at` | ISO 8601 timestamp | HA | Wall-clock at drop. |
| `bean_mass_temp` | time series of (offset_seconds, °F) | ESPHome | [BT](glossary.md#bt-bean-mass-temperature) sampled at 1Hz minimum. Offsets are seconds since `started_at`. |
| `ambient_temp_at_start` | float (°F) | manual or weather integration | Garage or patio temperature; influences early ramp. |
| `profile_name` | string or null | HA dropdown | In Phase 0 this is null (no profiles yet); preserved here for Phase 2+. |
| `llm_critique` | string (markdown) | HA rest_command | Free-form critique returned by the post-mortem call. |
| `subjective_notes` | string (markdown) | manual entry | Your notes during or after roast. Smell, sound, anything not telemetry. |
| `cup_quality` | object: `{ score: int 1-10, notes: string }` | manual entry, days later | Filled in after you actually drink it. |

### Phase 1 additions

Added in [Phase 1](phases/phase-1-crawl-instrumented-manual-roaster.md):

| Field | Type | Source | Notes |
| --- | --- | --- | --- |
| `env_temp` | time series of (offset_seconds, °F) | ESPHome | [ET](glossary.md#et-environmental-temperature) sampled at 1Hz minimum. |
| `motor_speed` | time series of (offset_seconds, rpm) | ESPHome | Paddle motor speed. Commanded value, since the Greartisan has no encoder. |
| `ror` | time series of (offset_seconds, °F/min) | HA template sensor | [RoR](glossary.md#ror-rate-of-rise) computed from `bean_mass_temp` (smoothed first derivative over 30 seconds). Stored, not just derived, so consumers don't have to recompute. |

### Phase 2 additions

Added in [Phase 2](phases/phase-2-walk-closed-loop-control.md):

| Field | Type | Source | Notes |
| --- | --- | --- | --- |
| `dimmer_duty_cycle` | time series of (offset_seconds, percent) | ESPHome | What the PID asked the AC dimmer to do. |
| `first_crack_at` | float (seconds offset) or null | ESPHome FFT | Timestamp the audio detector fired. Null if [first crack](glossary.md#first-crack) didn't fire (early dump, missed crack). |
| `profile_name` | (existing field, now usually populated) | HA dropdown | Phase 0's null behavior still works for free-pour roasts. |

### Phase 3 additions

Added in [Phase 3](phases/phase-3-run-ai-driven-roasting.md):

| Field | Type | Source | Notes |
| --- | --- | --- | --- |
| `visual_roast_level` | time series of (offset_seconds, level int 1-8, confidence float 0-1, notes string) | HA + vision model | One sample every ~10 seconds. [SCAA scale](glossary.md#scaa-roast-levels). |
| `camera_frame_refs` | list of (offset_seconds, path or url) | HA camera entity | References, not blobs. Frames live wherever HA stores camera snapshots; this list lets you find them again. |

## Storage Backend

Genuinely undecided. The right answer depends on how heavily you end up using the data, and you don't have to pick today. Two real options:

### Option A: InfluxDB

A separate time-series database, typically running as a HA add-on or Docker container.

- Pros: built for time series. Queries (downsample, derivative, rolling window) are fast and expressive. Retention policies are built in. Grafana plays well with it if you ever want a separate dashboard. Survives HA recorder purges.
- Cons: a second moving piece in the stack. Schema migration is on you. The HA InfluxDB integration is reliable but does add a network hop per sample.

### Option B: HA's built-in recorder plus long-term statistics

The default. SQLite or MariaDB backing the HA recorder; long-term statistics tables for downsampled rollups.

- Pros: zero extra infrastructure. Templating sensors and history queries from automations work natively.
- Cons: schema is constrained to HA's entity model. Structured roast records have to be denormalized into entities, or stored as a JSON blob in `input_text` with no native query support. Performance degrades over time without manual housekeeping.

### Recommendation framing

If you intend to do more than read individual roasts in the dashboard (specifically: comparing roasts side by side, fitting recipe models, exporting CSVs, training anything), pick InfluxDB. If you are primarily going to look at one roast at a time and re-read the LLM critique, the recorder is enough.

You don't have to decide right now. Phase 0 generates so little data that either backend handles it. The decision becomes painful around Phase 2, when you start having profiles to compare across roasts. Pick before then.

## Example Record

A Phase 2 roast (Phase 3 fields would be additions, not replacements):

```json
{
  "roast_id": "01HW8K3M5N7P9R2T4V6X8Z0A1B",
  "started_at": "2026-04-12T14:23:11-07:00",
  "ended_at": "2026-04-12T14:36:48-07:00",
  "bean_mass_temp": [
    [0, 78.4],
    [1, 79.1],
    [2, 80.3]
  ],
  "ambient_temp_at_start": 72.0,
  "profile_name": "ethiopia-natural-light-v2",
  "llm_critique": "Reasonable curve overall. Turning point at ~95s, a touch slow. Development time was 23%, in range but on the long side for a light roast at this RoR. Consider charging 10°F hotter next time to compress the early phase.",
  "subjective_notes": "Smelled grassy through 5 minutes, then turned bready. First crack popped clean, easy to time.",
  "cup_quality": {
    "score": 7,
    "notes": "Bright. Floral. A little flat on the finish, probably from the slow development."
  },
  "env_temp": [[0, 81.2], [1, 82.0]],
  "motor_speed": [[0, 60], [1, 60]],
  "ror": [[30, 22.1], [60, 18.9]],
  "dimmer_duty_cycle": [[0, 75], [1, 75]],
  "first_crack_at": 487.3
}
```

Time-series arrays are truncated above; in practice each one has roughly one sample per second for the duration of the roast.

## Decision Log

| Decision | Choice | Rationale |
| --- | --- | --- |
| Schema additivity | Required | Phase 0 records read by a Phase 3 dashboard must not break. Cheap to enforce now; expensive to retrofit later. |
| `roast_id` as ULID | Yes | Sortable by time, no central ID server, 26 chars in JSON. |
| `ror` stored, not just derived | Yes | Multiple consumers (dashboards, post-mortem prompts, recipe model) all need it. Compute once at sample time. |
| Camera frames as references, not blobs | Yes | Frames are heavy. Keeping them out of the roast log lets you purge frame storage independently of the roast record. |
| Storage backend | Defer | Both InfluxDB and HA recorder are reasonable. The right answer depends on Phase 2 / Phase 3 usage patterns; pick before the end of Phase 1. |
