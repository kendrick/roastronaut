# Domain Docs

How the engineering skills should consume this repo's domain documentation when exploring the codebase.

## Before exploring, read these

- **[`docs/glossary.md`](../glossary.md)**: this repo's vocabulary.
- **`docs/adr/`**: read ADRs that touch the area you're about to work in.

**This repo has no `CONTEXT.md`, and should not grow one.** The format defines `CONTEXT.md` as a glossary and nothing else, and `docs/glossary.md` already fills that role, with anchors the phase docs link into on first use of a term. A second vocabulary file would only give terms two homes to drift between. Read the glossary where another repo's instructions would say `CONTEXT.md`, and put a new term there.

If `docs/adr/` is missing something you expected, **proceed silently**. Don't flag its absence; don't suggest creating ADRs upfront. The `/domain-modeling` skill (reached via `/grill-with-docs`) writes them when a decision actually gets resolved.

## File structure

```
/
├── docs/
│   ├── glossary.md                    ← vocabulary, anchored per term
│   ├── adr/
│   │   ├── 0001-drop-temperature-is-a-proxy-not-ground-truth.md
│   │   └── 0002-observed-roast-level-is-graded-on-cooled-beans.md
│   └── phases/                        ← one doc per implementation phase
└── README.md
```

There is no `src/` yet. The docs are the deliverable through Phase 0, and the first code to land will be ESPHome YAML and HA automations rather than an application tree.

## Use the glossary's vocabulary

When your output names a domain concept (in an issue title, a refactor proposal, a hypothesis, a test name), use the term as defined in [`docs/glossary.md`](../glossary.md). Don't drift to synonyms an entry marks under `_Avoid_`: write "drop" rather than "dump" in prose, and keep "dump" for the HA button and servo command that are literally named that.

Watch the terms that differ by one word. `roast_level_observed` and `roast_level_model` are both SCAA levels and they are not interchangeable: one is the reference, the other is the thing being measured.

If the concept you need isn't in the glossary yet, that's a signal: either you're inventing language the project doesn't use (reconsider) or there's a real gap. On a real gap, add the term to `docs/glossary.md` with an anchor, and note it for `/domain-modeling`.

## Flag ADR conflicts

If your output contradicts an existing ADR, surface it explicitly rather than silently overriding:

> _Contradicts ADR-0001 (drop temperature is a proxy, not ground truth), but worth reopening because…_
