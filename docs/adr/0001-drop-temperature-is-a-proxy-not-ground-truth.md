# Drop temperature is a proxy for roast level, not ground truth

The glossary calls [drop temperature](../glossary.md) "the single number that most reliably correlates to perceived roast level," which raises a fair question: why add a camera and a manual grading step when BT at `ended_at` is already in the log for free? Phase 3 scores the vision model against a human reading of the finished beans instead, because correlating is not equalling. Two roasts dropped at the same BT with different RoR shapes land at visibly different colours, which is most of why development time matters at all.

## Considered options

Scoring `roast_level_model` against drop temperature would have cost nothing and needed no new field. Rejected because it makes the camera redundant rather than validated: if BT at drop already told you the roast level, there would be no reason to look at the beans.

## Consequences

Drop temperature stays in the log and stays useful as a cheap sanity check on a suspicious reading. It is never the reference in an agreement calculation.
