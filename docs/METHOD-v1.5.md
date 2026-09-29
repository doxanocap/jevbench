# JevBench v1.5 — scoring method (FROZEN)

**Status:** frozen on 2026-09-25, before any v1.5 entrant result exists. The SHA-256 of this file and
the freeze time are posted on the agent board (thread #13). This file changes only through a
versioned method revision (v1.5-M2, …) with a public changelog note. The note must say whether the
author had seen v1.5 results. Supersedes `jevbench-v15-prep-20260924/METHOD-v1.5-DRAFT.md`, which
remains the source for adapter and metric details not restated here (§9).

**Disclosure (integrity rule).** The weights below were fixed from design principles and the
measurement-noise arithmetic in §2. They were not fixed from simulated rankings. Before freezing, the
author read only three kinds of v1.4.2 information:
- anonymous field statistics, including the distribution of sealed and public accuracy, gaps and
  item discordance;
- the public v1.4.2 top-five note, which says decider-4b v2 is #1 and Jev 1.13.0 has the higher
  Intelligence (53.1 vs 49.4);
- the v1.4.2 #1 row's fields.

No v1.5 weight was compared against rankings before this freeze. The rank simulation is in
`SIMULATION.md` and was run after the hash was posted.

## 0. What changes against v1.4 — summary

| | v1.4.2 | v1.5 (frozen) |
|---|---|---|
| Open (non-rotating) decisions per system | 534 (231 published) | **904** (**601** published) |
| Sealed decisions per system | 308, all hard-style | **720**, same tier mix as the open set |
| Sealed pool | 308 fixed | **2,805** authored and critic-reviewed; each release draws a fresh sample |
| Sealed share of Intelligence | 20 % | **50 %** |
| Tier weights easy/standard/judge/hard | 14/28/28/30 | **10/20/30/40** (judge + hard 58 % → **70 %**) |
| Request types | Choice-centred | **Choice 50 %, Noul 25 %, Score 25 %**, each scored natively |
| State tracking | none | **new family in all three types** |
| Chance baseline | tier-average; sealed fixed 0.293 | **per item** (Choice 1/options, Noul 0.5, Score uniform-level MAE) |
| Overfit penalty | gap > 25 raw points | **excess gap over the field median > 8 CC points** |
| Axis weights I/C/S/$ | 25/25/25/25 | **40/20/20/20** (option B, §6); A and C are frozen presets |
| Ties | not shown | **paired bootstrap; statistical ties shown; "#1" only if significant** |

## 1. Inventory

### 1.1 Open set: 904 decisions, all scored for every system

| Source | n | Published? | Status |
|---|---:|---|---|
| Frozen v1.2 items, as in v1.4 (easy 72 / standard 96 / judge 146 / hard 220) | 534 | 231 published; 303 held-out/imported stay unpublished, as before | frozen since 19 Sep |
| v1.5 public drafts: Noul 60 (48 tool-call guardrail, 12 state tracking), Score 60 (48 lead qualification, 12 state tracking) | 120 | yes | authored; blind-critic 120/120 (prep job) |
| Drawn from the sealed pool into public (§1.3): Choice 130 including 30 state tracking, Noul 60, Score 60 | 250 | yes | authored + critic-reviewed (pool jobs) |
| **Total** | **904** | **601 published** | |

The 303 unpublished v1.2 items have already been sent to operator APIs, so they no longer count as a
holdout. They stay in the open set, which is **not** treated as unseen. The sealed set is the holdout.

**Type reconciliation (the draft's open point).** An item's request type is the `type` field of its
frozen item file. The v1.2 public files contain 139 Choice, 74 Noul and 18 Score items. The freeze
script reports the full 904-item type × tier matrix, and that matrix is scored as reported. No item
is retyped by hand.

### 1.2 Sealed set: 720 decisions per release

Each release uses a stratified sample: Choice **360**, Noul **180**, Score **180**, which matches the
50/25/25 type weights. The tier item mix is 14/28/28/30 % in every type, the same difficulty profile
as the authored pool and the open set:

| Type | easy | standard | judge | hard | Σ |
|---|---:|---:|---:|---:|---:|
| Choice | 50 | 101 | 101 | 108 | 360 |
| Noul | 25 | 50 | 50 | 55 | 180 |
| Score | 25 | 50 | 50 | 55 | 180 |

- Within each type × tier cell, families are allocated proportionally to the pool, using
  largest-remainder rounding.
- State tracking gets ≥ 12 % of every type: Choice pool 15 %, Noul 20 %, Score 19 %.
- Paraphrase-robustness groups are drawn whole.

**Why this fixes "sealed too hard".** The v1.4 sealed set consisted only of hard-style items.
- For the median ranked system, sealed accuracy was 0.292 against a chance level of 0.293, so the
  chance-corrected sealed score was **0**.
- 93 % of ranked systems scored below 20.
- 72 of 89 ranked systems carried the gap penalty.

That result does not measure generalisation; it measures a difficulty mismatch. From v1.5 the sealed
sample has the open set's tier mix, and each item is chance-corrected on its own options.

### 1.3 Pool, draws and rotation

The pool holds 1,579 Choice, 608 Noul and 618 Score items, 2,805 in total, stored under
`/home/flori/jevbench-sealed/pool-v1.5/`. Every item has a single gold answer, a blind review by the
other engine, and a SHA-256.

1. **Seed.** `seed = int(SHA-256(METHOD-v1.5.md)[:16], 16)`. The sampler script is hashed and posted
   on the board before it runs. It runs once.
2. **Public draw first:** 250 items (§1.1), stratified 14/28/28/30 by tier and proportional by family.
   They leave the pool permanently. The pool that remains is 2,555, which meets the ≥ 2,000 floor.
3. **Sealed sample second:** 720 items (§1.2). The sample manifest hash (item ids plus item SHA-256s)
   is posted before any entrant run.
4. The sample stays fixed for all v1.5.x point releases, so late entrants get the same items. When an
   operator API receives an item, the item is marked `burned_for += [operator]`. Every v1.x release
   redraws its sample from unused, unburned items. Rule-generated families, including state tracking,
   are topped up with new generator seeds. A family whose unburned stock falls below 1.5 × its next
   quota needs new authoring before the release.
5. **No item is selected, dropped or re-tiered on the basis of any benchmarked system's answers.** An
   item may be removed only for a gold error found by review, and every entrant is then rescored
   without that item.

The 308 v1.4 sealed items are retired from scoring. They stay sealed until Florian decides whether to
publish them as an archive.

## 2. Size justification: confidence intervals

The noise model uses paired comparisons on shared items. Among 109 v1.2 system pairs whose accuracy
differs by less than 3 points, the median item discordance was δ = 0.143 (IQR 0.10–0.19). The
chance-correction factor is about 1/(1 − 0.33). The 95 % half-width of a difference between two
systems is:

| Design | Intelligence difference | Composite, option A (25 % I) | Composite, option B (40 % I) |
|---|---:|---:|---:|
| v1.4.2: 534 + 308, 20 % sealed | ±4.0 | ±1.2 | ±1.9 |
| v1.5 draft: 351 + 240, 20 % sealed | ±4.9 | ±1.5 | ±2.4 |
| **v1.5 frozen: 904 + 720, 50 % sealed** | **±2.8** | **±0.8** | **±1.3** |

Notes on the table:
- The composite rows use ∂H/∂I = w_I·H²/I² at H ≈ 60 and I ≈ 55.
- Speed and cost measurement noise is not included.
- A single system's Intelligence has SE ≈ 1.6. The SE of its open-minus-sealed gap falls from 4.6 to
  **3.2** points, which lets the overfit allowance tighten from 25 to 8 (§4).
- Resolving a 1-point Intelligence difference would take about 12,000 decisions per system, which is
  not feasible or sensible.

**Consequence, frozen:** v1.5 does not claim that 1-point differences are real. Every release
computes a **paired stratified bootstrap**:
- B = 2,000 resamples of items within split × type × tier strata;
- the same resample for all systems;
- Intelligence, Calibration and the composite recomputed per resample, with Speed and Cost held
  fixed.

For each pair of adjacent ranks, the leaderboard shows `≈` (statistical tie) when the 95 % interval
of the difference contains 0. Ranks keep point-estimate order. A system is called **"#1"** only if it
beats #2 in ≥ 95 % of replicates. Otherwise the wording is "joint leaders (statistical tie)". The
same rule applies to "beats" claims in posts. The published top-5 approval gate is unchanged.

## 3. Intelligence

### 3.1 Per-type competence, split x ∈ {open, sealed}, per tier

- **Choice:** CC = 100 · (acc − c̄)/(1 − c̄). Here c̄ is the mean over the tier's items of
  1/(number of options). Invalid or failed answers count as wrong.
- **Noul:** P(yes) ≤ 0.20 is No, P(yes) ≥ 0.80 is Yes, and anything in between is an abstention,
  counted wrong. CC = 100 · (acc − 0.5)/0.5.
- **Score:** CC = 100 · (1 − mean nMAE / mean nMAE_chance). nMAE = |pred − gold|/(K − 1). The
  prediction is the expected position if a distribution is returned, otherwise the level.
  nMAE_chance for an item is the mean of |ℓ − gold|/(K − 1) over its K levels, which is the uniform
  random guess. This replaces the draft's uncorrected 100 · (1 − nMAE): a random guess would have
  scored about 60 under the draft.

Per-tier values are **not clipped**, so noise below chance is not truncated.

### 3.2 Aggregation

Tier weights: **easy 0.10, standard 0.20, judge 0.30, hard 0.40**. They are renormalised only when a
tier has no items.

    CC_{t,x} = Σ_tier w_tier · CC_{t,x,tier}
    I_x      = Σ_t W_t · CC_{t,x}          W: Choice 0.50, Noul 0.25, Score 0.25,
                                            renormalised over supported types (§3.4)
    base     = 0.50 · I_open + 0.50 · I_sealed

### 3.3 Rationale for the weights

- **50 % sealed.** Florian's range was 40–50 %; this takes the top of it.
  1. The open set is published or operator-exposed. Anyone can train on it, and the v1.4.2 data
     shows systems with public–sealed gaps of up to 57 points.
  2. With the sealed sample now at the same difficulty, weighting it no longer punishes every system
     for the benchmark's own difficulty mismatch.
  3. For similar item counts (904 vs 720), the variance-minimising weight is close to 50/50. The
     40 % alternative has the same noise, so the tiebreaker is contamination resistance.
- **Judge + hard at 70 %.** Easy and standard items separate systems little: in v1.4.2 the field
  means were 0.95 and 0.82 accuracy. They mostly confirm basic competence. Judge and hard items carry
  the discriminating signal, and they represent the decisions where a Jev-class system is worth
  deploying. Easy stays at 10 % so a system that fails basics is not rescued by lucky hard answers.
- **Choice 50 / Noul 25 / Score 25.** This follows the draft and the three Jev request types. Choice
  is the most general request type. Noul and Score are native, calibrated request types that were
  under-represented in v1.4.

### 3.4 Support

A type that a system does not support is "Not supported" and is excluded, never scored as zero. The
row shows its support signature and planned-weight denominator. Only systems covering all three types
are ranked in the full-coverage ranking. Partial rows are listed separately.

Label-only systems use the fixed adapters in the draft:
- Noul becomes a Yes/No label with no abstention.
- Score becomes the levels as a Choice list, and the chosen label maps to its position.

A label-only system is therefore supported, with competence scored and calibration zero. A system
declared supported whose outputs are later invalid is scored wrong on those items. Support cannot be
withdrawn after results are seen.

## 4. Overfit penalty, relative to the field

    gap        = I_open − I_sealed                        (CC points, same weighting on both sides)
    G_med      = median gap over ranked full-coverage systems of the v1.5.0 measurement batch
                 (frozen at v1.5.0; reused unchanged for v1.5.x point releases)
    excess     = gap − G_med
    penalty    = max(0, 1 − max(0, excess − 8) / 100)
    Intelligence = clip(base · penalty, 0, 100)

- The open set (v1.2 plus new drafts) and the sealed pool come from different authoring routes, so a
  system-independent difficulty offset is expected. The field median absorbs it. What remains
  measures **one system's** excess drop, which is the overfitting signal.
- 8 points is about 2.5 × the SE of one system's gap (§2).
- The slope stays at 1, as in v1.4.
- **Difficulty-mismatch flag (report only):** if |G_med| > 10, the release notes say so and the next
  sealed draw re-stratifies. Nothing is re-weighted after results.

## 5. Calibration, Speed, Cost

- **Calibration:** typed calibration as in the draft:
  - Choice: ECE and TVD on probability items.
  - Noul: ECE on P(yes), with Brier score reported.
  - Score: normalised RPS and top-level ECE.

  The typed values are computed on open and sealed items pooled, to reduce noise, and combined with
  the 50/25/25 type weights over supported types. Label-only outputs contribute 0, as in v1.4.
- **Speed:** unchanged formula, score(s) = 100 − 20 · log10(s / 0.1 s), mean of p50 and p95. It is
  measured on a serial run over all open-set standard + judge items, with the v1.4 self-host/demo
  adjustment of ×2 plus 0.15 s on our own servers.
- **Cost:** unchanged formula, 100 − 30 · log10($/1,000 decisions / 0.001). Tokens are pooled over
  all 1,624 decisions. Prices follow the 24 Sep price rule: a system without a public, bookable price
  gets an estimate from its base model's reference price, labelled as an estimate.
- Speed and Cost stay serial, one decision per request. A batched view (several questions per state)
  may be reported as an unscored diagnostic. It does not enter the v1.5 composite.

## 6. Composite and axis-weight options

The composite is a weighted harmonic mean (power mean p = −1) of Intelligence, Calibration, Speed and
Cost. For each of Intelligence, Speed and Cost separately, an axis value below 50 multiplies the
composite by (axis/50)². These gates are unchanged from v1.4.

Three weight options are frozen. **B is the headline**; A and C are published presets on every
release.

| Option | I / C / S / $ | Intelligence gate | Principle |
|---|---|---|---|
| A: equal | 25/25/25/25 | < 50 → (I/50)² | Status quo. No preference among axes, which suits a drop-in decision primitive where latency and price matter as much as accuracy. |
| **B: validity-weighted (headline)** | **40/20/20/20** | < 50 → (I/50)² | An axis's weight should reflect how directly it measures decision value and how reliably it is measured. In v1.5, Intelligence rests on 1,624 decisions, half of them unseen. It is the direct measure of correct decisions, and its noise has fallen about 1.5×. Speed includes an assumed ×2 load factor, and Cost is often a base-model estimate; both are indirect, model-based numbers. For a sub-second, sub-cent system, a wrong decision usually costs more than the latency or price difference between contenders. |
| C: floor-gated equal | 25/25/25/25 | < 60 → (I/60)² | Keeps equal weights but answers the open "minimum Intelligence for #1" question with a stricter floor: a system with weak judgement cannot be ranked high on speed and cost alone. |

The presets "Intelligence only", "Emphasis on Accuracy 60:20:20", "Speed 20:60:20", "Cost 20:20:60"
and "Balanced 33:33:33" remain as sort views.

**Switching the headline from B to A or C after seeing v1.5 or simulated results is result-informed.**
It requires a versioned method revision whose public note says so.

## 7. Integrity and freeze sequence

1. This file's hash is posted on the board. **(done at freeze)**
2. Scorer, adapters, sampler and bootstrap are implemented and unit-tested against this file. An
   independent reviewer (Opus or Devin/Opus) confirms that the code matches this file, and the code
   hashes are posted.
3. The public draw and the sealed sample are made with the committed seed, and the manifest hashes
   are posted.
4. A pilot runs on the **open set only**, with three systems. It checks the harness only, and no
   weight or item may change afterwards.
5. The official measurements run. Sealed items go only to evaluator-controlled offline pods, or to
   operator APIs with the API flag set, under the 24 Sep sealed-run rule.
6. The aggregate is computed. The #890 new-#1 verification gate runs: pricing basis, gap against the
   median, overlap, and an independent recomputation. The top-5 preview goes to Florian, then
   publication.

Training or calibration jobs must never read the pool or the sample (`/home/flori/jevbench-sealed/`).
The rules against contamination from the public side (training on open items is allowed and
disclosed) are unchanged.

## 8. Reporting per row

Each row reports:
- I_open, I_sealed, gap, excess and penalty;
- per-type and per-tier CC for both splits, with n;
- typed calibration parts;
- raw and adjusted latency, and the cost basis;
- support signature, bootstrap 95 % interval of the composite, and tie markers;
- API flag and adapter id.

Sealed item-level data is never published.

## 9. Carried over unchanged from the draft

The following are unchanged from the draft: adapter definitions and the ban on per-system prompt
tuning; Noul thresholds; Score probability metrics (RPS, 90 % interval coverage); the Spearman
diagnostic, which does not score; exposure rules; and "not supported" handling.
