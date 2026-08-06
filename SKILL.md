---
name: less
description: Analyze software repositories for safely removable complexity and produce or implement concise, measurable ablation plans with before/after KPIs, ordered from lowest to highest risk. Use for requests to generate, refresh, prioritize, adversarially review, or implement an ablation or cleanup list across code, architecture, public surfaces, documentation, tests, CI, assets, or developer tooling.
---

# Less

Remove only complexity that current evidence proves is unnecessary. Keep every
output short, measurable, and decision-ready.

## Rules

- Analyze the current project and current `HEAD`; never reuse an old audit as fact.
- Read repository instructions before inspecting candidates.
- Keep analysis read-only unless the user explicitly asks for implementation.
- Preserve user constraints and documented security, lifecycle, data, and compatibility invariants.
- Treat active security checks, policy goldens, smoke gates, validation, and tests
  of live behavior as preservation constraints, never ablation candidates.
- Remove obsolete coverage only as residue of an approved functional removal.
- Treat external audits as candidate sources only; reproduce every relied-on claim.
- Keep only `FACT` and `DERIVED` claims. Discard assumptions, guesses, confidence
  language, and conclusions that cannot be derived from stated facts.
- Require explicit user approval before implementing functional candidates.
- Re-baseline every remaining candidate after each implementation batch or commit.
- Measure every implemented candidate again with the same KPI method used at
  audit time; report the actual improvement percentage, never just the estimate.
- Do not commit, push, or make unrelated changes unless the user asks.

## Workflow

1. Record the commit, worktree state, repository instructions, and preservation
   constraints.
2. Inspect shipped behavior from source, generated help or API surfaces, tests,
   smoke coverage, CI, assets, current documentation, and recent history.
3. Find exact producers, consumers, dynamic references, external contracts, and
   replacement paths. Use repository-appropriate compilers and static tools.
4. Describe each candidate's exact action, decisive reason, measurable result,
   concrete downside, alternative, and strongest reason to keep it.
5. Run the feature-impact check, define and measure at least one KPI, explain the
   risk rating, tag every material claim, and adversarially test the removal.
6. Put only confirmed items in the actionable table. Present fact-backed
   verification opportunities as suggestions after it. Omit weak or rejected
   candidates unless asked.
7. Order actionable items from lowest to highest risk.

## Feature-impact check

Run this check for every candidate:

1. Does runtime or user-visible behavior change?
2. Does a public command, API, configuration, output, or error contract change?
3. Does a supported use case disappear or require another workflow?
4. Do defaults, lifecycle, persistence, data, or migration semantics change?
5. Do security, confinement, observability, or integration guarantees change?

Classify the feature impact as `Non-functional` only when all five answers are no
and supported by `FACT` or `DERIVED` claims. Classify it as `Functional` if any
answer is yes. If an answer lacks that support, block the candidate. Retain it as
a verification suggestion only when a `FACT` identifies an exact useful check;
otherwise omit it.

`Non-functional` means all product features and contracts remain intact.
`Functional` means at least one product feature or contract changes.

## KPI discipline

Link at least one KPI to every actionable item and verification suggestion.

- Name the metric and scope. Prefer a direct outcome measure over a vague score.
- Use the smallest stable system boundary that includes the target and its peers
  or replacement. Do not define the denominator as only "items selected for
  deletion" or another scope that makes every removal a trivial 100% gain.
- Measure a non-zero baseline from the current tree or a reproducible benchmark.
- Estimate the post-change target from a diff sketch, deterministic count, or
  benchmark. Use a range when a point estimate would imply false precision.
- Show `baseline → estimated target`, absolute delta, and percentage improvement.
  For example: `duplicate encoders: 2 → 1 (-1; 50% fewer)` or
  `focused test median: 4.2 s → ~3.4 s (-0.8 s; ~19% faster)`.
- Treat the left side of `KPI (measured → estimated)` as `FACT` and the right
  side as `DERIVED`; the column heading carries that meaning to reduce clutter.
  Mark exceptions with an epistemic tag. State the measurement command
  or method compactly when another agent could not otherwise repeat it.
- Use LOC only when its scope is explicit and the deleted lines represent real
  maintained complexity; exclude vendored and generated content unless it is the
  target. Do not use lines changed, files touched, or test count as a proxy for
  value without explaining what cost they measure.
- Do not claim a performance payoff without a relevant repeatable benchmark.
- If no credible baseline, target, or improvement percentage can be produced,
  omit the candidate until it can be measured.

## Epistemic tagging

Allow exactly two tags to survive analysis and appear in output:

- `[FACT]` — directly reproducible from the current source, generated surface,
  test, runtime probe, compiler, deterministic count, or benchmark
- `[DERIVED]` — a conclusion, calculation, risk assessment, or forecast derived
  only from explicitly stated `FACT` claims

Prefix every material claim with one of these tags unless a fixed column heading
already establishes it. Make every non-obvious `[DERIVED]` claim traceable to the
`[FACT]` in the same row or immediately preceding context. Never promote an
assumption, missing fact, source conflict, or confidence judgment to `[DERIVED]`.
Remove such claims from the analysis. Keep a verification suggestion only when a
remaining `[FACT]` supports it and the required check can be stated exactly.

## Adversarial check

Before confirming removal:

- Make the strongest case for keeping it.
- Check dynamic use, generated code, integrations, downstream contracts, and
  operator or contributor workflows.
- Check security boundaries, data ownership, migration, rollback, teardown, and
  forensic value.
- Read the introducing history when the rationale is unclear.
- Verify the proposed replacement covers the lost workflow.
- Resolve disagreements between code, tests, help, and documentation.

Rank risk as `🟢` low, `🟡` medium, or `🔴` high. Always follow the icon with the
most important concrete failure mode, implementation cost, or user-visible loss;
never emit an icon or rating alone. Consider blast radius, reversibility,
evidence strength, migration needs, and public contracts.

## Implementation

When the user approves items:

1. Re-check each item against the current `HEAD`.
2. Remove the smallest coherent slice.
3. Update related code, help, completion, tests, smoke paths, CI, documentation,
   migration guidance, and terminology.
4. Preserve coverage for behavior that remains.
5. Re-run every KPI with the same scope and method as its recorded baseline.
6. Report the actual before/after delta, improvement percentage, estimate miss,
   real losses, and unrun validation. Never present a forecast as an actual result.
7. Run focused validation, the repository's full check, and a residue search.
8. Follow the user's commit strategy; otherwise leave changes uncommitted.
9. Re-baseline the remaining list after a batch or commit.

## Output

For an audit, use this compact Markdown schema:

```markdown
# Ablation audit

Risk: 🟢 low · 🟡 medium · 🔴 high

Baseline: [FACT] `<commit>` (`<branch>`), worktree `<clean or relevant changes>`, `<checks passed>`.  
Not run: [FACT] `<important validation was not run>`.

| # | Change | Why | KPI (measured → estimated) | Risk / trade-off |
|---:|---|---|---|---|
| 1 | **`<short title>`** — `<exact action, short names only>` | [FACT] `<one decisive reason>` | **`<baseline> → <target>`** `<metric>` · **`<delta>; <percent> better`** | 🟢 [DERIVED] `<specific reason the risk is low, failure mode, or lost behavior>` |

Refs: 1 `<repo-relative/path.ext:line>` · `<second ref>` — 2 `<ref>`

## Suggestions

- **`<short title>`** — [FACT] `<evidence supporting a possible simplification>`. Verify `<exact missing check>`. Potential KPI: **`<measured baseline> → <estimated target>`** `<metric>` (**`<delta>; <percent> better`**).
```

Use the heading, risk legend, baseline lines, column names, and column order
verbatim. Put only actionable candidates in the table and order them from lowest
to highest risk. Keep the table to these five columns. Make the title, KPI
numbers, percentage, and risk icon visually prominent so the key points scan
vertically. Keep `Change` to a short title plus
one exact action, `Why` to one decisive tagged sentence, and `Risk / trade-off`
to the icon plus one specific reason or consequence. Do not repeat a fact in
multiple cells.

Write every cell as one source line with no HTML: `<br>` renders as literal text
in terminal UIs. Separate the title from the action with ` — ` and KPI parts
with ` · `; renderers wrap cell text to the column width on their own. Never put
a full path, command line, or URL inside a cell — an unbreakable token longer
than ~25 characters inflates the column's minimum width and forces terminal
renderers out of the grid layout. Name files in cells by basename
(`file.go:123`) and put full repo-relative references on the `Refs` line below
the table: `path/from/root.ext:line` is clickable in editors, terminals, and
code hosts, and media that render Markdown links may use `[file.ext:line](path)`
instead. Put measurement commands there too. Add a `Refs` entry only for items
whose full reference or command did not fit in a cell; omit the line when no
item needs one. Never output abstract value labels such as `Higher reliability`,
change-type labels such as `refactor`, or feature-impact labels such as
`Non-functional`: they consume space without explaining the decision.

Add `Suggestions` only when fact-backed verification opportunities exist, after
the actionable table. Use one line per suggestion; include the supporting fact,
exact check, and linked KPI. If no candidate is actionable, write
`No actionable items.` instead of emitting an empty table. Do not add candidate
detail sections, narrative preambles, recommendations, or closing summaries.
Provide more detail only when asked.

After implementing one or more candidates, replace the audit format with:

```markdown
# Ablation results

Before: [FACT] `<commit or tree>`.  
After: [FACT] `<commit or tree>`, `<validation passed>`; [FACT] `<important checks were not run>`.

| # | Change | KPI (before → actual) | Estimate | Validation |
|---:|---|---|---|---|
| 1 | **`<short title>`** | **`<before> → <actual>`** `<metric>` · **`<delta>; <percent> better`** | **`<Met/Exceeded/Missed>`** — [DERIVED] `<difference from target>` | [FACT] `<focused and full checks passed or were not run>` |
```

Use the original baseline measurement, repeat its method after implementation,
and calculate the actual percentage. The `before` and `actual` values are `FACT`
by definition. The same cell rules apply: one source line per cell, no HTML,
` — ` and ` · ` separators, no unbreakable token over ~25 characters.
Explain material regressions or scope changes in `Estimate`.
