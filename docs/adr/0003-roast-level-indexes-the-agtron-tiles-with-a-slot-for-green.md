# Roast level indexes the Agtron tiles, with a slot for green

Roast level is a nine-point house scale: 1 is green, unroasted coffee, and 2 through 9 are the eight tiles of the SCA Agtron roast colour set in order, #95 "Very Light" down to #25 "Very Dark". It is not an SCA standard. The docs claimed it was, and [the research notes](../research/scaa-roast-level-scale.md) established that the SCA publishes no ordinal roast level scale at all, so the choice was between inventing something honestly and adopting something real.

## Considered options

Using the Agtron tile numbers directly as the stored value was the obvious move once we knew they existed, and it fails on green. All eight tiles are roasted coffee, but `roast_level_model` samples every ten seconds from the start of the roast, when the beans genuinely are unroasted. A tile-only scale forces the model's most common early-roast answer to be an absence rather than a value, which is the value most likely to be mishandled by a template sensor or a JSON parse.

Keeping the original 1-8 with green at 1 was the incumbent, and the arithmetic does not work: eight tiles plus a green slot needs nine values. Preserving "1-8" would have meant dropping one tile, and the only defensible one to drop is #25 Very Dark, which is exactly the roast you might hit by accident and want to record.

Instrument-grade measurement was priced and rejected. The SCA tile kit runs around $290 against a Phase 0 budget of $60, and real colour meters start near $750. A printed colour card at roughly $20 carries the same eight tile numbers, which is what makes the indexing worth doing at all.

## Consequences

The scale is legible to other roasters, because every level above 1 names a tile they can look up, while staying honest that the ordering is ours. Visual comparison against tiles carries roughly a full tile step of error, so this buys shared vocabulary rather than accuracy, and the ±1 tolerance in Phase 3's agreement metric should be read with that in mind.

Nothing was logged when this landed, so no migration was needed. Once roasts exist, changing the range means rewriting history rather than editing a document.
