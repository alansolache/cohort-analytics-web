# Stori Valuation Platform — Product Design

**Status:** Design exploration (no implementation)
**Audience:** Investor Relations, Finance leadership
**Author:** IR / Claude working session, July 2026

---

## 0. The core thesis

A valuation is not a number — it is **an argument with numbers attached**. The
platform's job is not to compute a valuation (a spreadsheet does that in an
afternoon); it is to make the argument **defensible, reproducible, and honest**:

1. Every number traceable to a source, a date, and a chain of adjustments.
2. Every assumption explicit, versioned, and challengeable.
3. Every output a range with stated confidence, never a false-precision point.

If the tool only produces prettier football fields than Excel, it has failed.
The moat is the **curated data + assumption discipline**, not the UI.

---

## 1. What actually makes this valuable (features ranked by value)

### Tier 1 — the reason to build it at all

| Feature | Why it matters |
| --- | --- |
| **Assumption ledger** | Every input (multiple, growth rate, FX, discount) is a first-class object with source, date, owner, and rationale. Clicking any output number walks back to its inputs. This is what makes a board deck answer "where did 9.5x come from?" instantly. |
| **Metric normalization engine** | Peers report revenue differently (gross vs. net of interest expense vs. risk-adjusted). The engine stores *raw reported* figures plus explicit normalization rules per company, so comparisons are apples-to-apples and the adjustments are visible, not baked in. |
| **Versioned valuation snapshots** | "Valuation as of the March board meeting" is frozen and reproducible forever — data, assumptions, and outputs together. Diffs between snapshots ("what changed since Q1?") are auto-generated. |
| **Private rounds database with staleness/confidence model** | See §4. The private-comp side is worthless without explicit data-quality treatment. |
| **Comp-set governance** | Named, versioned peer sets with an *inclusion and exclusion log* ("Dave excluded: no balance-sheet lending"). Cherry-picking is the #1 way valuations get discredited; the exclusion log is the antidote. |

### Tier 2 — high value, build second

- **Football-field composer** — assemble methodology ranges into the canonical
  output chart, with each bar linked to its full workpaper.
- **Multiple-vs-fundamentals regression** — fit multiple against growth,
  profitability, or Rule-of-40 across the comp set; read Stori off the line.
  This is what bankers actually do for fintech, and it neutralizes "but Nu
  trades at X" arguments by pricing the *difference in fundamentals*.
- **Historical multiple context** — where today's comp multiples sit vs. their
  own 1y/3y/5y history (percentile bands). A 12x market and a 6x market imply
  very different things; the tool should say which regime we're in.
- **Valuation bridge** — last round post-money → today, decomposed into
  metric growth, multiple change, FX, and dilution. The single most useful
  chart for an existing-investor conversation.

### Tier 3 — nice, not core

- Scheduled data refresh + alerting on comp moves.
- Export to branded PPTX/PDF memo.
- Read-only share links for board members / bankers.

### What I would explicitly **not** build

- A general-purpose modeling environment ("flexible everything"). Flexibility
  is how cherry-picking happens. Opinionated defaults with documented
  overrides beat a blank canvas.
- Automated "fair value" output with no human in the loop. The tool
  triangulates; a person concludes.
- Real-time streaming quotes. IR valuation runs on daily closes and quarterly
  fundamentals; real-time data adds cost and noise, zero decision value.

---

## 2. The workflow from the user's chair

The primary user story is not "compute a valuation." It is one of:

- **(a) Fundraise prep:** "What range can we credibly defend to a Series-X lead?"
- **(b) Board/investor update:** "How has our implied valuation moved since last quarter, and why?"
- **(c) Secondary / tender:** "What's a fair per-share price for common, given the preference stack?"
- **(d) Fire drill:** "Nu just reported. What does it mean for us?" — needs an answer in 30 minutes, not a rebuild.

Proposed flow (each step produces a saved, inspectable artifact):

```
1. Frame        → purpose (a–d above), as-of date, currency, share-count basis
2. Comp set     → pick/clone a governed peer set; log any inclusion changes
3. Metrics      → platform pulls normalized LTM/NTM metrics; flags stale or
                  low-confidence data; user resolves flags (accept / override
                  with rationale)
4. Methods      → select methodologies (defaults per purpose); each renders
                  its own workpaper with editable, logged assumptions
5. Triangulate  → football field; user weights/annotates each method,
                  writes the narrative ("we anchor on P/E of profitable
                  LatAm lenders because…")
6. Snapshot     → freeze; export memo; diff vs. prior snapshot
```

Two design rules:

- **Fast path first.** Case (d) must be: open latest snapshot → refresh market
  data → see the delta. If every session starts from a blank slate, the tool
  dies in month two.
- **Friction where it protects you.** Overriding a sourced number, excluding a
  comp, or changing a normalization rule always demands a one-line rationale.
  That's the audit trail writing itself.

---

## 3. Methodologies to support

Stori is a **balance-sheet lender**, and that dominates methodology choice.
Software-style EV/Revenue is actively misleading for a deposit-funded lender —
"enterprise value" is nearly meaningless when debt and deposits are the raw
material of the business, not financing. The tool should be opinionated here.

### Core set (launch)

1. **Trading comps — bank-style:** P/E (LTM and NTM), P/TBV, and
   P/TBV-vs-ROE regression. The natural frame for a profitable lender; how
   public LatAm fintech lenders (Nu, Inter) are actually priced.
2. **Trading comps — growth-style:** P/Revenue (equity-side, *not* EV) with an
   explicit revenue-definition per comp, and growth-adjusted variants
   (multiple ÷ NTM growth). Needed because private-market buyers still think
   in revenue multiples; the tool should show both frames side by side.
3. **Regression comps:** multiple vs. {growth, ROE, Rule of 40} across the
   set, with Stori read off the fitted line ± band. Default over raw
   median-of-multiples wherever the comp set has ≥ 6 members.
4. **Precedent private rounds:** implied multiples at raise for peer fintechs,
   plus **round-indexed marks** — take a peer's last known round valuation and
   roll it forward by the public-comp index since that date (the mutual-fund
   markdown technique). Always shown as a *range with a confidence tier*, never
   a point.
5. **Own-round anchoring:** Stori's last round terms rolled forward the same
   way — metric growth × multiple regime change × FX. This is methodology and
   bridge chart in one.

### Second wave

6. **Equity DCF / dividend-discount / residual income:** the intrinsic check.
   For a lender this must be an *equity* cash-flow model (capital generation
   above regulatory/target capital), not an unlevered FCF DCF.
7. **Scenario-weighted (PWERM-lite):** probability-weighted outcomes (IPO,
   strategic sale, continued private) — the honest frame for late-stage
   private equity value, and what a serious crossover investor runs anyway.

### Cross-cutting adjustments (explicit toggles, never silent)

- Illiquidity / DLOM (with the chosen basis documented).
- Country / FX risk treatment: value in MXN and convert, or value in USD with
  a country risk premium — pick one, state it, never both.
- **Preference-stack awareness:** headline post-money ≠ common per-share
  value. For any secondary or tender use case the tool must model the
  liquidation stack (even crudely) or clearly label outputs "headline basis."

---

## 4. Missing and incomplete private-company data

This is where most internal tools quietly rot. Principles:

1. **Confidence tiers on every private datum** —
   `A: primary document` (deck received, filing) ·
   `B: reputable secondary` (press with numbers, PitchBook-style) ·
   `C: rumor/estimate`. Tiers propagate: any output touching C-tier data is
   badged, and the user chooses to include it knowingly.
2. **Staleness decay, visible.** A round mark ages badly: display marks with
   their age, and beyond a threshold (say 18 months) refuse to show the raw
   mark without the index-adjusted companion (roll-forward by public comp
   index since the round date). Show both: "raised at $Xbn (Jan-2024, 30
   months ago); index-adjusted ≈ $Y–Z bn."
3. **Never silently impute.** If a peer's revenue is unknown, the cell says
   *unknown* — with an optional, clearly-labeled estimate ("management est.,
   triangulated from headcount + take-rate, ±40%"). An imputed number that
   looks like a real number is the most dangerous object in the system.
4. **Ranges as the native type.** Private metrics are stored as
   low/base/high + tier + source, and every downstream calculation carries
   the interval, not just the base.
5. **Survivorship flag.** Dead and down-round companies stay in the database
   and in the precedent set by default. A precedent analysis built only on
   the winners that kept raising is structurally inflated.
6. **Capture workflow, not just schema.** The DB only stays alive if entering
   a datapoint takes < 2 minutes: paste a link/article, tag company + round +
   figures + tier, done. Qualitative notes are first-class (a "their NPLs are
   worse than reported" note from a diligence call is often worth more than
   the revenue figure).

---

## 5. Visualizations that earn their place

Ranked by decision value:

1. **Football field** — one bar per methodology, each annotated with key
   assumption and confidence; Stori's last round and any live offer overlaid.
   The output of record.
2. **Multiple vs. fundamental scatter + regression** — comp set on
   growth/ROE axes, fitted line, Stori positioned with its private-company
   band. Kills more bad arguments than any other chart.
3. **Valuation bridge (waterfall)** — last round → today: metric growth,
   multiple regime, FX, dilution. The board chart.
4. **Multiple time series with percentile bands** — is today's comp multiple
   the 20th or 80th percentile of its own history? Regime context for every
   conversation.
5. **Sensitivity table / tornado** — implied valuation vs. the two or three
   assumptions that actually move it. Also serves as an honesty check: if an
   assumption barely moves the answer, stop debating it.
6. **Round step-up ladder** — peers' valuation trajectories across rounds
   (time vs. valuation, log scale), Stori's path overlaid.
7. **Data-quality heatmap** — comps × metrics grid colored by
   freshness/confidence. Unglamorous; builds more trust in the tool than
   anything else on this list.

Anti-patterns: dashboard sprawl (a wall of 20 tiles), spider/radar charts for
comps (unreadable), and any single-number "estimated valuation" headline
widget.

---

## 6. Assumptions that must always be explicit

Non-negotiable, surfaced on every output (a standing "assumptions block"):

- **As-of date** for market data and for fundamentals (they differ).
- **Metric definitions**: which revenue (gross / net / risk-adjusted), LTM vs
  NTM, calendarization method.
- **Estimate source** for forward metrics: consensus vs. management plan —
  and these must never be mixed within one comp table.
- **FX**: rate, date, and whether valuation is computed in MXN or USD.
- **Share count basis**: basic / fully diluted / treasury method; option pool
  treatment; headline vs. preference-adjusted equity value.
- **Comp set version** and the exclusion log.
- **Discounts applied** (DLOM, size, country) with basis.
- **Confidence composition**: what share of inputs are tier A/B/C, oldest
  private datapoint used.

Rule of thumb: if two reasonable analysts could pick differently, it's an
assumption and it gets logged; if it changes the answer by >5%, it also
appears in the sensitivity chart.

---

## 7. Mistakes valuation tools make (and how we avoid them)

1. **False precision.** Outputting "$2.83bn" from C-tier inputs. → Ranges as
   the native type; point estimates only exist inside ranges; significant
   figures capped by input confidence.
2. **Frictionless cherry-picking.** Toggle comps until the number looks
   right, no trace. → Exclusion log + snapshot diffs make every change
   visible and dated.
3. **Metric definition mixing.** One comp's gross revenue against another's
   net. → Normalization engine with per-company rules; raw and normalized
   both stored.
4. **EV/equity confusion for financials.** Applying EV/Revenue to a
   deposit-funded lender. → Methodology defaults are entity-type-aware; the
   tool warns (not blocks, warns loudly) on category errors.
5. **Headline valuation ≠ common value.** Ignoring liquidation preferences
   and participation. → Preference-stack module; outputs labeled by basis.
6. **Stale private marks treated as current.** → Staleness decay + mandatory
   index-adjusted companion figure (§4.2).
7. **Survivorship bias in precedents.** → Failures stay in the set by
   default (§4.5).
8. **Mean-of-multiples worship.** Averages dominated by one outlier. →
   Median by default, regression preferred, outliers flagged not dropped
   silently.
9. **The tool as answer machine.** Making it so easy to get a number that
   nobody reads the reasoning. → The output artifact is a memo (narrative +
   charts + assumption block), not a KPI tile. Slightly *harder* to get a
   bare number, deliberately.
10. **Data plumbing rot.** Free APIs change/break; nobody notices; numbers
    drift. → Ingestion is checkpointed and validated (cross-source checks,
    jump alerts); every figure carries its fetch timestamp and source.

---

## 8. Challenges to the initial framing

Pushing back on three of the founding assumptions, as requested:

- **"OpenBB / Yahoo Finance as the data source."** Fine for a prototype;
  not IR-grade for the numbers that matter most. The multiples that drive
  fintech valuation are **forward (NTM) consensus** multiples, and free
  sources are weak-to-wrong on consensus estimates, LatAm tickers, and
  restated financials. Plan the architecture around a *source-agnostic
  ingestion layer with provenance* so you can start free and swap in
  Koyfin/FactSet/Visible Alpha per-field later — and budget for at least one
  paid consensus source before this is used in a live fundraise.
- **"Flexible valuation methodologies."** Flexibility is the failure mode,
  not the feature. What IR needs is a small opinionated set done rigorously,
  with overrides that are possible but logged. "Flexible" tools converge on
  whatever number the loudest person in the room wanted.
- **"Gradually build the private database."** The schema is easy; the
  discipline is hard. Treat the private DB as a *product with a capture
  workflow and an owner*, not a table someone fills when they remember.
  If entry takes >2 minutes or nobody is accountable, it will be 60% stale
  within a year and will silently poison every precedent analysis.

And one strategic alternative worth considering: **phase 1 might not be a web
app at all.** The highest-value pieces — normalized comp data with provenance,
the private DB with confidence tiers, snapshot discipline — could live as a
governed data layer + templated workbook for a quarter or two. Build the UI
once the data layer has proven it stays alive. The cohort-analytics tool in
this repo earned its keep by doing one thing well; this platform should earn
its UI the same way.

---

## 9. The best version of this I can imagine

- **It's an argument builder.** The unit of work is a *valuation memo*:
  narrative, football field, bridge, sensitivity, assumptions block — every
  number clickable back through its full lineage to a source document.
- **It has a time machine.** Any valuation reproducible exactly as of any
  past snapshot; diffs between any two snapshots auto-narrated ("range moved
  +12%: +9% metric growth, +6% multiple expansion, −3% FX").
- **It's opinionated about being a lender.** Bank-frame methodologies are the
  defaults, category errors trigger warnings, and the revenue-multiple frame
  is available but always shown *next to* the P/E and P/TBV frames — because
  the gap between those frames *is* the negotiation with investors.
- **It argues back.** A "red team" view auto-generates the bear case against
  the current draft: the stalest data used, the most outlier-dependent
  method, the assumption with the biggest sensitivity, the excluded comp
  that would hurt most. Walking into an IC having already seen your weakest
  flank is the entire game.
- **It's boring where it counts.** Data validation, provenance, and snapshot
  integrity get more engineering effort than charts. Trust compounds;
  features don't.

---

## Appendix A — Illustrative data model (entities, not schema)

- `Company` (public or private; entity type: lender / payments / software…)
- `MetricObservation` (company, metric, period, value or range, source,
  fetch/entry date, confidence tier, normalization rule applied)
- `FundingRound` (company, date, headline valuation, structure notes,
  preference terms if known, confidence tier, sources, qualitative notes)
- `CompSet` (versioned membership, inclusion/exclusion log)
- `Assumption` (key, value, rationale, owner, timestamp)
- `Methodology run` (method, inputs → outputs, workpaper)
- `Snapshot` (frozen bundle of all of the above + narrative)

## Appendix B — Suggested phasing

1. **P0 (prove the data layer):** public comp ingestion with provenance +
   normalization; private DB with capture workflow and confidence tiers;
   outputs via templated workbook/memo.
2. **P1 (the platform):** web UI for comps/methods/football field; snapshots
   and diffs; regression comps; bridge chart.
3. **P2 (the edge):** PWERM, preference-stack module, red-team view,
   scheduled refresh + alerting, board-ready exports.
