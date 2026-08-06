# less

An agent skill that removes only the complexity current evidence proves
unnecessary — and proves it with numbers.

`less` audits a repository at `HEAD` and produces a decision-ready ablation
plan: exact actions, one decisive reason each, measured → estimated KPIs, and
risk ratings ordered from lowest to highest. On approval it implements the
removals and reports the actual improvement against the estimate.

## Example

```text
This repo has gotten crufty over the years — figure out what we can safely remove.
```

| # | Change | Why | KPI (measured → estimated) | Risk / trade-off |
|---:|---|---|---|---|
| 1 | **Remove legacy TSV exporter** — delete `legacy_tsv.py` and its residue test | [FACT] no importer outside its own test | **10 → 9** modules · **-1; 10% fewer** | 🟢 [DERIVED] no CLI path reaches it |
| 2 | **Drop unused `requests` dep** — remove the line from `requirements.txt` | [FACT] never imported anywhere | **1 → 0** runtime deps · **-1; 100% fewer** | 🟢 [DERIVED] no import site exists |

Refs: 1 `logpipe/legacy_tsv.py:1` · `tests/test_legacy_tsv.py:1` — 2 `requirements.txt:2`

## Guarantees

- Analysis is read-only; implementing anything requires explicit approval.
- Only `[FACT]` (reproducible now) and `[DERIVED]` (traceable to stated facts)
  claims survive — assumptions and confidence language are discarded.
- Every item carries a KPI with a measured baseline; denominators that make
  every removal a trivial 100% win are forbidden.
- Active security checks, policy goldens, smoke gates, and tests of live
  behavior are preservation constraints, never candidates.
- Every candidate passes an adversarial check (strongest case to keep it,
  dynamic use, downstream contracts, history) before it becomes actionable.
- After implementation, results report the actual before → after delta and
  whether the estimate was met — never a forecast presented as a result.

## Install

`less` is a single [SKILL.md](SKILL.md) in the open agent-skills format.
Clone it into your agent's skills directory, e.g. for Claude Code:

```sh
git clone https://github.com/leogr/less ~/.claude/skills/less
```

Any other agent that supports the SKILL.md format works the same way — clone
into its skills directory and ask for an audit ("find what we can safely
remove", "slim this repo down"), or invoke it by name.

## Evals

`evals/evals.json` defines the benchmark suite: a planted-fixture audit
(known removables plus trap files that must be refused), a real-repository
audit, and an audit-then-implement flow — 28 assertions covering ground
truth, safety refusals, and schema conformance.

## License

[MIT](LICENSE)
