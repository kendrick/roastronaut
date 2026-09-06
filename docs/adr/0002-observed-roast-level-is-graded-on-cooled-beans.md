# Observed roast level is graded on cooled beans, not in the chamber

`roast_level_observed` is your reading of cooled beans at rest; `roast_level_model` is the vision model's reading of beans in the chamber, mid-roast. The reference deliberately does not match the conditions the thing being measured sees. A SCAA level is conventionally assessed on cooled beans, it is the reading you could defend to another roaster or reproduce from a photo weeks later, and it is the number you actually care about once the roast is over.

## Considered options

Grading what you see in the chamber at the moment you hit drop would have matched the model's viewing conditions exactly. Rejected on two counts. The call would land at the busiest, most time-critical moment of the roast, with beans at 400°F heading for the cooling station, which produces a rushed number that still looks like data. And by Phase 3 the model's own estimate is on the Lovelace banner in front of you, so your reading would anchor to the number it exists to check.

Recording both readings was the third option, and it buys something real: it separates a model that misreads the chamber from one that reads the chamber correctly while chamber appearance simply fails to predict cooled appearance. Skipped because `camera_frame_refs` already preserves the frames. That reading can be taken later, unhurried and blind to the model's answer, which is better data than a live call and needs no schema decision today.

## Consequences

The model is scored against something it cannot see. That is a real limitation, recorded here rather than designed away. If agreement comes back poor, grade the saved frames blind before concluding the model is at fault.
