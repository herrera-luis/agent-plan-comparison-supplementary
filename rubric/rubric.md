# Evaluation Rubric — Agent Plan Comparison

> **Status**: FROZEN before any run is scored.
> **Scope**: Applied identically to every (tool × benchmark) reproduction —
> 6 runs × 10 parameters = 60 rows in `scoring/scores.csv`.
> **Score range**: integer `1..5` per parameter.
> **Aggregation**: `weighted_score = score * weight / 100`. Tool-per-benchmark
> totals = sum of weighted scores (max 5.0).

## How to use this rubric

1. Open the run's canonical artifact (`runs/<benchmark>/<tool>/...`).
2. For each of the 10 parameters below, pick the integer 1–5 score that best
   matches the rubric anchors. If a run sits between two anchors, prefer the
   lower score.
3. Append the row to `scoring/scores.csv` with a one-sentence justification
   in the `note` column referencing the artifact location.
4. **If the score is 1 or 5**, invoke the `grill-me` skill, log the
   question/answer block in `scoring/grill-me-log.md`, and only commit the
   score after `Resolved Plan: accept`.

The weights are an explicit value statement: tasks our team values
(traceability, test plans, hand-off ergonomics) carry more weight than
proxies for engineering polish (latency, cost). Weights sum to **100**.

## Parameters

### 1. Artifact completeness — weight 10

Does the planner produce the *shape* of artifacts a downstream implementer
needs (proposal, design, requirements, task list), or only a chat blob?

| Score | Anchor |
|-------|--------|
| 1 | A single chat-resident blob; no separable artifacts. |
| 2 | A single markdown document, sectioned but not split. |
| 3 | Two artifacts (e.g. a plan doc + a task list) with light cross-linking. |
| 4 | Three or more artifacts but missing one of {proposal, design, requirements, tasks}. |
| 5 | Multi-file structured set covering proposal, design, requirements/specs, and tasks. |

### 2. Requirement traceability — weight 15

Can a future reader trace each task back to a specific requirement / scenario?

| Score | Anchor |
|-------|--------|
| 1 | Tasks have no link to requirements; "implement X" with no reference. |
| 2 | Free-text references to requirements but no IDs. |
| 3 | Some tasks cite requirement names but not all. |
| 4 | Every task cites a requirement, but the format is inconsistent. |
| 5 | Every task carries an explicit `Covers: <REQ-ID>` (or equivalent stable ID) and the IDs resolve to scenarios in the spec. |

### 3. Task granularity — weight 10

Are tasks sized for individual review and check-off?

| Score | Anchor |
|-------|--------|
| 1 | One mega-task ("implement the feature") or no task list. |
| 2 | A handful of multi-day chunks; not individually reviewable. |
| 3 | ~10 medium tasks; some are still vague. |
| 4 | ~20 tasks; most are concrete but a few are still mega-tasks. |
| 5 | Tasks scoped to ≤30 minutes of work each; every task is a single PR-sized unit. |

### 4. Test-plan coverage — weight 15

Does each task carry a test plan a reviewer can execute?

| Score | Anchor |
|-------|--------|
| 1 | "Add tests" appears once globally with no specifics. |
| 2 | A separate "tests" task at the end with no per-task linkage. |
| 3 | Some tasks include a sentence-level test note. |
| 4 | Most tasks include a concrete test plan, but coverage gaps remain. |
| 5 | Every task ships with a per-task test plan (commands or scenario refs); test plans cite real fixtures or scenarios. |

### 5. Code-reference accuracy — weight 10

Are file paths, function names, and APIs cited in the plan real, or hallucinated?

| Score | Anchor |
|-------|--------|
| 1 | Referenced paths or APIs do not exist in the live repo. |
| 2 | Some references are real, but at least one is hallucinated. |
| 3 | All references are plausible; spot-check finds them mostly correct but with stale signatures. |
| 4 | All file paths verified; minor naming drift in one or two function references. |
| 5 | Every cited file/function/API is verifiable in the live repo at the planner's stated path. |

### 6. MCP / external-source integration — weight 10

When the prompt references external systems (Jira ticket, GitHub PR), does the
planner actually read them?

| Score | Anchor |
|-------|--------|
| 1 | Planner ignores the external link entirely; produces a plan from the prompt body alone. |
| 2 | Planner names the external system but quotes nothing from it. |
| 3 | Planner summarizes the external source but with at least one factual error. |
| 4 | Planner integrates the external source faithfully; minor omission. |
| 5 | Planner reads, summarizes, and cites Jira/GitHub correctly; integrates ticket comments and linked code into the plan. |

### 7. Reproducibility (artifact persistence) — weight 10

If the user closes the chat, can the plan be re-opened verbatim?

| Score | Anchor |
|-------|--------|
| 1 | Plan lives only in volatile chat history; no on-disk artifact. |
| 2 | User can manually copy the plan out, but no native export. |
| 3 | Plan has a native on-disk surface, but no version control integration. |
| 4 | Plan is on disk and gits cleanly, but lacks a validation/lint step. |
| 5 | Plan is a versioned directory in git with a CLI-driven validator (`openspec validate --strict` or equivalent). |

### 8. Latency to "ready to implement" — weight 5

Wall-clock time from prompt-submitted to a complete plan being ready.

| Score | Anchor |
|-------|--------|
| 1 | > 60 minutes. |
| 2 | 30–60 minutes. |
| 3 | 15–30 minutes. |
| 4 | 10–15 minutes. |
| 5 | < 10 minutes. |

### 9. Token / API cost — weight 5

Approximate API spend for the plan run, taken from `metadata.json.token_estimate`.

| Score | Anchor |
|-------|--------|
| 1 | > $5.00. |
| 2 | $2.00–$5.00. |
| 3 | $1.00–$2.00. |
| 4 | $0.50–$1.00. |
| 5 | < $0.50. |

### 10. Hand-off friction to implementation — weight 10

Once the plan exists, how much friction is there to start coding from it?

| Score | Anchor |
|-------|--------|
| 1 | Manual re-typing required; no path to feed the plan back to a coding agent. |
| 2 | Plan can be copy-pasted into a fresh agent prompt, but no first-class hand-off tool. |
| 3 | One-step manual hand-off (copy to file, point an agent at it). |
| 4 | Native hand-off via a documented command, requiring one explicit user action. |
| 5 | Single-command hand-off (`/opsx-apply`, `cursor → agent mode`, Ralph loop) with no manual re-typing. |

## Weight check

| # | Parameter | Weight |
|---|---|---|
| 1 | Artifact completeness | 10 |
| 2 | Requirement traceability | 15 |
| 3 | Task granularity | 10 |
| 4 | Test-plan coverage | 15 |
| 5 | Code-reference accuracy | 10 |
| 6 | MCP / external-source integration | 10 |
| 7 | Reproducibility (artifact persistence) | 10 |
| 8 | Latency to "ready to implement" | 5 |
| 9 | Token / API cost | 5 |
| 10 | Hand-off friction to implementation | 10 |
| **TOTAL** | | **100** |

Verification (target the Weight-check table only — its rows have a 4th
pipe-delimited field carrying the integer weight; the per-parameter anchor
tables have only 3 columns and are correctly skipped):

```bash
awk -F'|' '$4 ~ /^ *[0-9]+ *$/ {gsub(/ /,"",$4); s+=$4} END {print "weight_sum =", s}' \
  research/agent-plan-comparison-final/rubric.md
# expected: weight_sum = 100
```

## Frozen-as-of

This rubric is frozen at the commit that introduces this file. Any change to
parameters, weights, or anchors after a single row has been written to
`scoring/scores.csv` REQUIRES re-scoring all prior rows. The rubric MUST NOT
be edited inside the same PR after scoring has begun (per
`REQ-AGENT-PLAN-COMPARISON-002 → Rubric is frozen before scoring begins`).
