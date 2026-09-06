---
title: Roast Level Scale Research
status: reference
created: 2026-09-06
related:
  - roastronaut
---

# Roast Level Scale Research

Two questions about the roast level scale these docs lean on. Is "SCAA" still the organization's name? And is the eight-point scale in [glossary.md](../glossary.md) something the Specialty Coffee Association actually publishes?

Short answers: the name is nine years stale, and the scale does not exist.

This file establishes facts and stops there. It changes nothing in the glossary, the schema, or the phase docs. The last section lists what a follow-up would have to touch.

## Access constraint, stated up front

**No primary source in this file could be fetched directly.** This environment's egress policy refuses `sca.coffee` and every subdomain, `agtron.net`, `static1.squarespace.com` where the SCA hosts its own PDFs, Wikipedia, ProPublica, and the third-party mirrors of the SCAA standards. The proxy notes say to report a policy denial rather than route around it, so nothing here was substituted from an archive or a cache.

Web search did work. It reads pages and returns quoted text from them. The wording quoted below is therefore the SCA's own, but it arrived through a search index rather than through a fetch anyone can reopen and read in full. Claims carry one of three tags:

- Primary, mediated: text owned by the SCA or the SCAA, quoted from their own page or document, reached through search rather than directly.
- Secondary: trade press or a vendor describing the above.
- Unverified: a lead worth chasing that nothing here confirms.

Re-run this from an unrestricted network and three documents settle almost everything: the [SCA standards index](https://sca.coffee/research/coffee-standards), the [2018 Coffee Standards PDF](https://sca.coffee/s/Coffee-Standards-compressed.pdf), and the [October 2025 roast color white paper](https://static1.squarespace.com/static/584f6bbef5e23149e5522201/t/69033dc059316a64ee34b7c5/1761820096879/SCA_Roast+Color+White+Paper_Oct302025_SECURED.pdf). Until someone does, treat this file as a map of where to look.

## Question 1: the organization's name

**The organization is the Specialty Coffee Association (SCA), and has been since January 2017.** "SCAA" names a body that stopped existing that month.

The SCA's own about page: "The SCA was established in 2017 through the unification of two longstanding industry organizations: The Specialty Coffee Association of America (SCAA) and the Speciality Coffee Association of Europe (SCAE)." *(Primary, mediated: [sca.coffee/about](https://sca.coffee/about))*

So the merger premise in the brief is confirmed, not refuted. The predecessors were the Specialty Coffee Association of America, founded 1982, and the Speciality Coffee Association of Europe, founded 1998. *(Secondary: [Daily Coffee News](https://dailycoffeenews.com/2017/01/03/finally-a-name-for-the-scaascae-specialty-coffee-association/), [Perfect Daily Grind](https://perfectdailygrind.com/2017/01/specialty-coffee-association-sca-officially-launched/))*

An SCA news post from February 2017 catches the change mid-sentence: "The Specialty Coffee Association of America—now the Specialty Coffee Association (SCA), after the recent unification with the Speciality Coffee Association of Europe." *(Primary, mediated: [scanews.coffee](https://scanews.coffee/2017/02/08/specialty-coffee-standards-and-best-practices/))*

Membership ratified it across 2016. SCAE members voted in the first half of the year, roughly 86% in favor. SCAA members voted in late summer, roughly 62% in favor. The unified body launched that January. *(Secondary: [CoffeeBI](https://coffeebi.com/2017/01/11/scaascae-merge-form-specialty-coffee-association/), [Vending Market Watch](https://www.vendingmarketwatch.com/coffee-service/news/12293513/scae-scaa-officially-become-specialty-coffee-association))* Those vote percentages are the least load-bearing numbers here, and the only ones resting on trade press alone.

Two things worth knowing before anyone edits text.

The European body's name is spelled both ways in the wild, and the SCA's own pages are inconsistent about it. The about page renders it "Speciality Coffee Association of Europe"; other SCA pages use "Specialty". Nothing turns on this, but a search-and-replace assuming one spelling will miss the other.

The SCA also still ships products under the older acronym. Its store sells the "SCA Agtron Roast Color Kit" at a URL ending `scaa-agtron-roast-color-kit`, and the EU store calls the same product the "Agtron Roast Colour Classification system". Finding "SCAA" on an SCA page in 2026 usually means a legacy product name, not a live organizational one.

One lead nobody could chase: the US legal entity appears to have kept the SCAA's original EIN, 52-1312827, and now files under a name in the "Association for Specialty Coffee" family. That would make the merger a rename of a continuing entity rather than a fresh incorporation. Mildly interesting, irrelevant to these docs, and unconfirmed because ProPublica's copies of the filings are blocked. *(Unverified)*

## Question 2: the scale

**The SCA publishes no ordinal 1-8 roast level scale. As of September 2026 it publishes no roast level scale at all.** The glossary describes something that does not exist.

### What the SCA does publish

Where SCA standards mention roast level, they express it as an **Agtron number**: a reflectance reading off an instrument, not a position in an ordered list.

The 2018 Coffee Standards set the cupping roast at 63.0 on the Agtron "Gourmet" scale for ground coffee, with a tolerance of ±1.0 units. The reading is taken between 30 minutes and 4 hours after roasting, on coffee ground to the SCA Standard Grind for Cupping and held at room temperature. The matching SCAA color standard gives equivalents on other instruments: Agtron "Commercial" 48.0, Colortrack 62.0, Probat Colorette 3b 96.0. *(Primary, mediated: [SCA Coffee Standards, revised 2018](https://sca.coffee/s/Coffee-Standards-compressed.pdf); [SCAA Standard: Coffee Color](https://www.coffeelabequipment.com/SCAA_COFFEE_COLOR.pdf), prepared by the SCAA Standards Committee)*

Higher Agtron numbers mean lighter roasts, because the number is reflected light. The Gourmet scale runs 0-100 by construction and about 25-95 in practice.

### The eight tiles, which are probably where the confusion started

An eight-item SCA artifact does exist, and it is almost certainly the source of the repo's "eight-point scale". It is a set of physical color tiles, and it is not numbered 1-8.

The SCA's own store copy says the kit "contains eight color disks, numbered in 10% increments ranging from 'Very Light' (tile #95) to 'Very Dark' (tile #25)", supplied with two petri dishes for sample preparation and a manual by Carl Staub. *(Primary, mediated: [store.sca.coffee](https://store.sca.coffee/products/scaa-agtron-roast-color-kit), [eustore.sca.coffee](https://eustore.sca.coffee/products/agtron-roast-colour-classification-system))*

The tile numbers are therefore 95, 85, 75, 65, 55, 45, 35, 25. Eight of them, descending, each one an Agtron Gourmet value rather than an index. A roaster matching tile #55 records 55, not 5.

Three consequences for the glossary.

The count is right and everything attached to it is wrong. Eight tiles exist. A 1-8 scale does not.

**Green is not on the scale.** All eight tiles are roasted coffee, from Very Light down to Very Dark. Unroasted coffee has no tile and no Agtron classification, so the glossary's "1 (green)" has nothing behind it. Neither does the vision prompt's `1=green`.

The direction is inverted. The repo's numbers rise as the roast darkens; Agtron numbers fall. Anyone mapping this field onto real Agtron readings gets the sign backwards unless they catch it.

**The six intermediate tile names could not be established.** Both endpoints are confirmed from SCA's own copy: "Very Light" at #95, "Very Dark" at #25. The names between them are not. Sources disagree over whether the third tile is "Moderately Light" or "Medium Light", whether "Light Medium" is distinct from "Medium Light", and where "Moderately Dark" sits. Every candidate list came from a vendor or a blog. None came from the SCA or Agtron, and both of those sites are blocked here. This is an open gap, not a settled fact. Anyone rewriting the glossary around tile names needs the kit manual or an SCA page to close it.

### The SCA is currently building the standard it does not have

This is the strongest evidence available, because it is the SCA saying so.

A white paper dated 30 October 2025, "Developing Roast Color Standards for the Specialty Coffee Industry", sets out why the SCA "intends to adopt a standardized scale for roast level measurement based on visible light measurements". An expert group was to review a draft at the end of 2025, with a Standards Development Panel review in 2026. The draft rests on CIELAB rather than proprietary instrument readings, the stated motive being that Agtron analyzers cost too much and their numbers mean nothing to consumers. The same paper commits the SCA to a 2026 project to "understand and standardize roast color naming conventions". *(Primary, mediated: [SCA roast color white paper, October 2025](https://static1.squarespace.com/static/584f6bbef5e23149e5522201/t/69033dc059316a64ee34b7c5/1761820096879/SCA_Roast+Color+White+Paper_Oct302025_SECURED.pdf); see also [Coffee Decoded: Why Roast Color Matters](https://sca.coffee/sca-news/coffee-decoded-4-roast-color))*

An organization does not announce a project to standardize roast color naming if it already publishes a named eight-point roast level scale.

Whether that draft standard has landed since is unknown, because the SCA's site is unreachable from here. The timeline puts a panel review somewhere in 2026, so check it before rewriting the glossary. *(Unverified)*

### So what is the repo's 1-8 scale?

Nothing published, as far as this search could reach. No SCA standard, no other standards body, no vendor scale, and no widely used informal convention numbers roast levels 1 through 8 with green at 1.

The likeliest account is that it was assembled rather than inherited. Take the true fact that the SCA/Agtron classification has eight tiles, re-index those tiles 1-8 as though they were ordinal, then add green at the bottom to give the vision model something to say about unroasted beans.

The label "8 (Italian / Vienna)" has a second problem. Those are two different roast levels, and informal trade ordering puts Vienna well short of Italian: Vienna is the lightest of the dark roasts, French is darker, Italian is darkest. Pairing them as one endpoint merges levels the naming convention keeps apart. That convention is itself unstandardized, since City, Full City, Vienna, French and Italian mean different things at different roasters. This is exactly the problem the SCA's 2026 naming project exists to address. *(Secondary: general trade usage. No primary source defines these names, because no body publishes them.)*

None of this makes the repo's scale useless. A 1-8 ordinal is a serviceable thing for one person to grade their own roasts against, and a reasonable target for a vision model to be scored on. It is a house scale. The docs present it as an industry standard.

## Confidence

| Claim | Confidence | Basis |
| --- | --- | --- |
| The organization is the Specialty Coffee Association, since January 2017 | High | SCA's about page and a 2017 SCA news post, corroborated by several independent trade outlets |
| Formed by SCAA (1982) and SCAE (1998) merging | High | Same, though the founding years rest on trade press |
| The SCA publishes no ordinal 1-8 roast level scale | High | SCA standards express roast level as Agtron numbers, and SCA's own white paper says a roast level standard is still in draft |
| Eight Agtron/SCAA tiles, numbered #95 down to #25 in tens | High | SCA store copy on two storefronts, consistent across every corroborating source |
| Green has no tile and no place on the scale | High | Follows from the tile range, which runs Very Light to Very Dark |
| The six intermediate tile names | Unresolved | Sources conflict and none is primary; the SCA and Agtron sites are both blocked |
| The repo's 1-8 corresponds to no published scale | Moderate | A negative finding. Nothing turned up, but a restricted network cannot prove absence |
| Whether the SCA's draft standard has landed since October 2025 | Unknown | Not checkable from here |

## What would have to change

Nothing below has been edited. `SCAA` appears 14 times across 8 files, one more than the brief's count of 13. The extra is a second occurrence in `docs/glossary.md`, which carries it on both line 25 and line 31. Regenerate the count with:

```bash
grep -rn SCAA --include='*.md' .
```

There are two independent problems here, and they can be fixed in separate commits:

1. The name is mechanical. `SCAA` becomes `SCA` nearly everywhere.
2. The scale is substantive. `1-8` describes a house convention rather than an SCA standard, so the docs have to either say so or move to Agtron numbers.

Two mechanical traps are worth knowing before starting.

Renaming the glossary heading on line 25 changes its anchor away from `#scaa-roast-levels`. Four inbound links break silently unless they move in the same commit: `docs/roast-log-schema.md` lines 37 and 67, `docs/phases/phase-3-run-ai-driven-roasting.md` line 19, and `docs/phases/phase-0-smoke-test-hand-cranked-validation.md` line 164.

The two vision prompts do not spell the range the same way. `docs/tooling-claude-code.md` line 86 writes `1-8` with a hyphen, while `docs/phases/phase-3-run-ai-driven-roasting.md` line 112 writes `1–8` with an en dash. Grepping for the hyphen form finds one and misses the other.

| File | Line | What is there | Problem |
| --- | --- | --- | --- |
| `docs/glossary.md` | 25 | `### SCAA roast levels` | Name. The anchor `#scaa-roast-levels` is linked from four places |
| `docs/glossary.md` | 27 | "The Specialty Coffee Association's eight-point scale running from 1 (green) through 8 (Italian / Vienna). Common waypoints are light at 2-3, medium at 4-5, dark at 6-7." | Attributes to the SCA a scale the SCA does not publish. Green is on no SCA scale, and the direction is inverted against Agtron |
| `docs/glossary.md` | 31 | "a SCAA level is conventionally assessed on cooled beans" | Name. The underlying claim survives: Agtron readings are taken off cooled, ground coffee |
| `docs/roast-log-schema.md` | 37 | `level: int 1-8`, "SCAA roast level", link to `#scaa-roast-levels` | Name, scale, anchor. Field shape is a schema decision, so re-pinning it is not free |
| `docs/roast-log-schema.md` | 67 | `level int 1-8`, "SCAA scale", link to `#scaa-roast-levels` | Same |
| `docs/phases/phase-3-run-ai-driven-roasting.md` | 19 | "SCAA roast level", link to `#scaa-roast-levels` | Name, anchor |
| `docs/phases/phase-3-run-ai-driven-roasting.md` | 112 | Vision prompt: "Estimate the current SCAA roast level on a 1–8 scale (1=green, 8=Italian/Vienna)" | Name and scale, inside a prompt the model is actually given. Also merges Vienna and Italian, which are different levels |
| `docs/phases/phase-3-run-ai-driven-roasting.md` | 165 | "within ±1 SCAA point", "your realistic range is roughly 3 through 6" | Name. The baseline argument survives any renumbering, but the literal 3-6 range is scale-specific and moves if the scale moves |
| `docs/phases/phase-3-run-ai-driven-roasting.md` | 198 | `SCAA roast level reference: https://sca.coffee/research/coffee-standards` | The URL is live and correctly the SCA's, but it documents no 1-8 scale. It is cited for something it does not say |
| `docs/phases/phase-0-smoke-test-hand-cranked-validation.md` | 164 | "SCAA roast level", link to `#scaa-roast-levels` | Name, anchor |
| `docs/tooling-claude-code.md` | 86-87 | Prompt: "Estimate the current SCAA roast level on a 1-8 scale (1 = green, 4 = medium, 8 = Italian / Vienna)" | Same defects as phase-3 line 112, in a second prompt. The two prompts should stay in step |
| `docs/tooling-claude-code.md` | 126 | "My target roast level is {target_level} (SCAA scale)." | Name |
| `docs/agents/domain.md` | 33 | "are both SCAA levels" | Name |
| `docs/adr/0002-observed-roast-level-is-graded-on-cooled-beans.md` | 3 | "A SCAA level is conventionally assessed on cooled beans" | Name. ADRs are usually superseded rather than edited, so this one is a judgment call |
| `README.md` | 50 | `← BT, ET, RoR, SCAA, the boom, etc.` | Name |

Whoever picks this up should also decide what the field means, not only what it is called. Three options, roughly:

- Keep 1-8 and label it a house scale.
- Move to Agtron numbers, and accept that reading one off a photo is a harder task than picking a bucket.
- Keep 1-8 for logging, and note which Agtron tile each level approximates. That buys a path to real numbers later without a migration now.

## Sources

Primary, mediated through search. None was directly fetchable from this environment:

- [About the SCA](https://sca.coffee/about): the 2017 unification and the predecessor names.
- [Specialty Coffee Standards and Best-Practices, February 2017](https://scanews.coffee/2017/02/08/specialty-coffee-standards-and-best-practices/): the SCAA-to-SCA transition in the SCA's own words.
- [SCA Coffee Standards, revised 2018](https://sca.coffee/s/Coffee-Standards-compressed.pdf): roast level for cupping as an Agtron target.
- [SCAA Standard: Coffee Color](https://www.coffeelabequipment.com/SCAA_COFFEE_COLOR.pdf): instrument equivalences, on a third-party mirror of an SCAA document.
- [SCA Agtron Roast Color Kit](https://store.sca.coffee/products/scaa-agtron-roast-color-kit) and [Agtron Roast Colour Classification system](https://eustore.sca.coffee/products/agtron-roast-colour-classification-system): eight tiles, #95 to #25.
- [Developing Roast Color Standards for the Specialty Coffee Industry, 30 October 2025](https://static1.squarespace.com/static/584f6bbef5e23149e5522201/t/69033dc059316a64ee34b7c5/1761820096879/SCA_Roast+Color+White+Paper_Oct302025_SECURED.pdf): the roast level standard still in draft.
- [Coffee Decoded: Why Roast Color Matters](https://sca.coffee/sca-news/coffee-decoded-4-roast-color): the SCA on Agtron and CIELAB.
- [SCA standards index](https://sca.coffee/research/coffee-standards): the standards that do exist.

Secondary:

- [Daily Coffee News, 3 January 2017](https://dailycoffeenews.com/2017/01/03/finally-a-name-for-the-scaascae-specialty-coffee-association/)
- [Perfect Daily Grind, January 2017](https://perfectdailygrind.com/2017/01/specialty-coffee-association-sca-officially-launched/)
- [CoffeeBI, 11 January 2017](https://coffeebi.com/2017/01/11/scaascae-merge-form-specialty-coffee-association/)
- [Vending Market Watch](https://www.vendingmarketwatch.com/coffee-service/news/12293513/scae-scaa-officially-become-specialty-coffee-association)
- [HunterLab on the SCAA roast color standards](https://www.hunterlab.com/blog/what-are-the-scaa-roast-coffee-standards/)
- [Daily Coffee News, 19 December 2025](https://dailycoffeenews.com/2025/12/19/weekly-coffee-news-towards-a-roast-color-standard-east-africa-investment/)
- [Perfect Daily Grind on coffee color analysis, December 2025](https://perfectdailygrind.com/2025/12/analysing-coffee-colour-what-roasters-should-know/)

Blocked and never reached: `sca.coffee` and all subdomains, `agtron.net`, `static1.squarespace.com`, `en.wikipedia.org`, `projects.propublica.org`, `pmc.ncbi.nlm.nih.gov`, `web.archive.org`, `coffeegeek.com`, `hunterlab.com`, `coffeelabequipment.com`, `tsdr.uspto.gov`.
