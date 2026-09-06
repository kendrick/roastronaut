# Domain Docs

How the engineering skills should consume this repo's domain documentation when exploring the codebase.

## Before exploring, read these

- **[`docs/glossary.md`](../glossary.md)**: this repo's vocabulary.
- **`docs/adr/`**: read ADRs that touch the area you're about to work in.

**Vocabulary lives in [`docs/glossary.md`](../glossary.md).** Every phase doc links into its anchors on first use of a term, so a definition that moves breaks those links. Put new terms there, with an anchor.

One guardrail, because a skill will actively push the other way: `/domain-modeling` offers to start a `CONTEXT.md` when it resolves its first term. Decline. The glossary is this repo's answer to that file, and a second vocabulary file gives terms two homes to drift between.

Where `docs/adr/` has nothing on your area, **proceed silently**: write the work, and let `/domain-modeling` record a decision when one actually gets made.

## What the repo holds

The docs are the deliverable through Phase 0, so there is no `src/` yet, and the first code to land will be ESPHome YAML and HA automations rather than an application tree. Read the directory itself for what exists; it cannot go stale the way a copy here would.

## Use the glossary's vocabulary

When your output names a domain concept (in an issue title, a refactor proposal, a hypothesis, a test name), use the term as defined in [`docs/glossary.md`](../glossary.md). Don't drift to synonyms an entry marks under `_Avoid_`: write "drop" rather than "dump" in prose, and keep "dump" for the HA button and servo command that are literally named that.

Watch the terms that differ by one word. `roast_level_observed` and `roast_level_model` are both SCAA levels and they are not interchangeable: one is the reference, the other is the thing being measured.

If the concept you need isn't in the glossary yet, that's a signal: either you're inventing language the project doesn't use (reconsider) or there's a real gap. On a real gap, add the term to `docs/glossary.md` with an anchor, and note it for `/domain-modeling`.

## Flag ADR conflicts

If your output contradicts an existing ADR, surface it explicitly rather than silently overriding:

> _Contradicts ADR-0001 (drop temperature is a proxy, not ground truth), but worth reopening because…_
