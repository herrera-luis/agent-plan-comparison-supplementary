# Repeat-run variance analysis (Reviewer 1, Comment 1)

Reviewer 1 noted that every cell in the 48-cell matrix is a single execution, yet
the manuscript treated the OpenSpec-vs-Ralph Opus 4.8 three-bench average (3.812
vs 3.802, a 0.010-point margin) as meaningful. We took the reviewer's **ideal
branch**: we repeated the cells that determine that margin — `openspec` and
`ralph` under Claude Opus 4.8 on all three benchmarks — **two additional times
each** (repeat-1, repeat-2; planning + execution legs), for **12 additional
attended captures**, under a frozen protocol (identical prompts, model pins, and
rubric). The original captures are frozen as **repeat-0** (they are the published
`scores-opus48.csv` cells and remain unchanged, so the aggregate table in the
paper is untouched).

## Sources

- `scores-opus48.csv` — frozen repeat-0 (published) per-parameter scores.
- `scores-opus48-repeats.csv` — the 128 per-parameter rows for repeat-1 / repeat-2
  (6 cells × 2 repeats × 16 parameters), each row carrying a `note` that states
  whether the score is `MECHANICAL` (P8/P9 recomputed from measured data),
  `TOOL-SURFACE` (P11–16, driver-determined, held constant), `PLAN-CONTENT` held
  at the frozen anchor, `HELD (calibration)`, or a `CONTENT-MOVE`.
- `variance-repeats.csv` — the compact per-cell weighted-total table and the
  avg-3 block reproduced below.
- Repeat artefacts: `metadata/runs/claude-opus-4-8/<benchmark>/<tool>/repeat-{1,2}/`.

## Scoring method (like-for-like against the frozen rubric)

The 16-parameter weighted total decomposes into three groups, scored so that a
parameter moves **only** when the repeat artefact genuinely differs from
repeat-0 — never because of scorer-calibration drift:

1. **Mechanical (P8 latency, P9 cost).** Recomputed directly from each repeat's
   measured `started_at → ended_at` planning wall time and `token-estimate.txt`
   (cost = tokens × \$19/M, rate-card, matching the frozen notes). These are the
   dominant source of run-to-run movement.
2. **Tool-surface (P11–16).** Determined by the tool/driver (Cline-as-extension),
   which is held constant across repeats, so they contribute a constant 1.00
   (`openspec`) / 1.20 (`ralph`) to every cell and do not vary.
3. **Plan-content (P1–P7, P10).** Re-read from each repeat's planning artefacts
   and scored against the frozen anchors. A move is recorded only after
   confirming the repeat artefact differs from repeat-0 on that dimension
   (three cases where a naive fresh read scored more strictly than the frozen
   scorer were reverted to the frozen anchor and annotated `HELD (calibration)`,
   because repeat-0 has the identical structure — e.g. openspec/simple batches
   validation in a grouped section in **all** repeats including repeat-0, and
   openspec/blog repeats carry ≥ the frozen 40 scenario blocks).

**Validation.** Re-summing the frozen repeat-0 vectors reproduces every published
aggregate total exactly (openspec s/c/b = 3.765/3.870/3.800; ralph = 3.475/3.895/
4.035), confirming the pipeline.

### Only two genuine plan-content moves were found

- **openspec / blog, repeat-1, P5 (Code-reference accuracy) 4 → 3.** A one-off
  cross-artefact naming drift: `content-model/spec.md` defines a `blog` collection
  with `/es/blog/hola-mundo/` while the design/tasks use `/articles/` (12 refs).
  Repeat-0 and repeat-2 are internally coherent.
- **ralph / video, repeat-1, P6 (MCP integration) 5 → 4.** The ticket-linked CMS
  reference code at `cognitiverun/api` returned HTTP 404 during that run
  (`PLAN.md` S1.3), so the plan integrated the Jira ticket + core-services code
  faithfully but omitted one linked source. Repeat-0 and repeat-2 fetched it.

Everything else that varied is mechanical latency/cost.

## Per-cell weighted totals (max 5.0), Opus 4.8

| Tool | Bench | r0 (frozen) | r1 | r2 | mean | range |
| --- | --- | ---: | ---: | ---: | ---: | ---: |
| openspec | simple (github-repo-bootstrap) | 3.765 | 3.800 | 3.800 | 3.788 | 0.035 |
| openspec | video (worker-video-feature) | 3.870 | 3.870 | 3.870 | 3.870 | **0.000** |
| openspec | blog (personal-blog-scaffold) | 3.800 | 3.765 | 3.835 | 3.800 | 0.070 |
| ralph | simple | 3.475 | 3.510 | 3.510 | 3.498 | 0.035 |
| ralph | video | 3.895 | 3.790 | 3.825 | 3.837 | **0.105** |
| ralph | blog | 4.035 | 4.035 | 4.070 | 4.047 | 0.035 |

The largest per-cell spread is **0.105** (`ralph`/video), driven by planning
latency: the planning leg took 7.5 min (repeat-0), 12.6 min (repeat-1), and
16.9 min (repeat-2), moving Parameter 8 across anchor 5 → 4 → 3. `openspec`/video
is perfectly stable (0.000). `openspec`/blog's 0.070 spread combines the P5 drift
above with a latency swing (repeat-0 was the 19.4-min planning outlier at anchor-3;
both repeats fell under 10 min at anchor-5).

## avg-3 (three-bench mean) and OpenSpec–Ralph ordering

| Replicate | openspec avg-3 | ralph avg-3 | margin (os − ralph) | leader |
| --- | ---: | ---: | ---: | --- |
| r0 (frozen) | 3.812 | 3.802 | +0.010 | openspec |
| r1 | 3.812 | 3.778 | +0.033 | openspec |
| r2 | 3.835 | 3.802 | +0.033 | openspec |
| **pooled** | 3.819 (3.812–3.835) | 3.794 (3.778–3.802) | — | — |

- **`openspec` leads `ralph` in all 3/3 replicates**; margins {0.010, 0.033, 0.033}.
- Each tool's avg-3 spread across replicates is **0.023**.
- The OpenSpec–Ralph gap (0.010–0.033) is of the **same magnitude as the per-cell
  run-to-run range (up to 0.105)**. So under Opus 4.8 the two tools are within
  run-to-run variation: the ordering is preserved but the margin is too small to
  support an ordering claim. The cross-model average (OpenSpec ahead of Ralph)
  is unaffected.

## Reporting decision

- The manuscript keeps the frozen repeat-0 aggregate table unchanged.
- Section 3.1 gains a sentence reporting the repeat spread and the "within
  run-to-run variation" reading; no ordering is claimed on the sub-0.1 margin.
- Section 4.3 gains an explicit statement that all cells except these repeats are
  single-run point estimates and that sub-0.1-point differences are not orderings.
- A new Supplementary table (Appendix H) reports the per-cell repeat totals and
  the avg-3 block above.
