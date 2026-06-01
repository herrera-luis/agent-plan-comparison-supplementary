# Score aggregates

Sources (primary corpus — Opus 4.8 + GPT-5.5, three benchmarks):

- [scores-opus48.csv](scores-opus48.csv) — Claude Opus 4.8 (192 rows = 4 tools × 3 benchmarks × 16 parameters; per-row `weighted_score = score × weight / 100`).
- [scores-gpt55.csv](scores-gpt55.csv) — OpenAI gpt-5-5 (192 rows; same shape).

Pilot / superseded artefacts preserved on disk for the audit trail only (they do **not** feed the tables below):

- [scores.csv](scores.csv) — Claude Opus 4.7 (the original published view, two benchmarks).
- [scores-gpt.csv](scores-gpt.csv) — OpenAI gpt-5-3-codex (rev-3 pilot).

Combined primary corpus: **384 score rows across 48 (tool × benchmark × leg → cell × model) cells** — 4 tools × 3 benchmarks × 2 models × 16 parameters. The `personal-blog-scaffold` greenfield benchmark (256 rows: 128 per model) was added in the three-benchmark revision; the simple/complex rows are the Opus 4.8 rerun that replaced the Opus 4.7 view.

The rubric carries 16 parameters (see `paper/appendix/A-rubric.tex`); parameters 1–10 keep their original relative weights scaled by 0.70 (sum 70), and parameters 11–16 each carry a flat weight of 5 (sum 30). Four operator-experience parameters are scored separately and excluded from the 16-parameter total.

Reproducible computation:

```bash
python3 tools/dump_dual_primary.py \
    --anthropic-model claude-opus-4-8 \
    --anthropic-scores scoring/scores-opus48.csv
python3 tools/compute_costs.py
```

## Per-tool weighted totals (max 5.0), both models, three benchmarks

| Tool | s Opus | s GPT | c Opus | c GPT | b Opus | b GPT | avg-3 Opus | avg-3 GPT | X-mod avg |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| **openspec** | **3.765** | 3.795 | 3.870 | 3.790 | 3.800 | 3.765 | **3.812** | **3.783** | **3.798** |
| **ralph** | 3.475 | 3.465 | 3.895 | 3.815 | **4.035** | 3.685 | 3.802 | 3.655 | 3.728 |
| **opencode** | 3.540 | 3.675 | **3.925** | **3.985** | 3.505 | 3.575 | 3.657 | 3.745 | 3.701 |
| **cursor** | 3.050 | 3.010 | 3.435 | 3.425 | 3.155 | 3.120 | 3.213 | 3.185 | 3.199 |

`s` = github-repository-bootstrap (simple), `c` = worker-video-feature (complex), `b` = personal-blog-scaffold (greenfield).

**The cross-model three-benchmark leader is `openspec` (3.798)**, ahead of `ralph` (3.728), `opencode` (3.701), and `cursor` (3.199) — a 0.097 spread among the top three. (The `openspec`/Opus video-support and single-file Latency cells were re-scored from anchor-1/anchor-3 to anchor-5 because their 394-min and 38.9-min wall spans are attended-session idle artefacts, not active latency; the blog cell at 19.4 min is kept at anchor-3. See the threats-to-validity caveat.) `openspec` leads the cross-model average clearly, but under Opus 4.8 it is statistically indistinguishable from `ralph` (3.812 vs 3.802, a 0.010 margin), leading outright only under GPT-5.5 (3.783) — so the model-dependence is concentrated in the per-bench blog flip (`ralph`↔`openspec`) rather than the aggregate.

Per-bench leaders: `opencode` wins the complex bench (3.955 cross-model vs ralph 3.855, openspec 3.830); `openspec` wins the simple bench (3.780 cross-model vs opencode 3.608); the greenfield blog bench is **model-split** — `ralph` first under Opus 4.8 (4.035, the single highest cell in the study), `openspec` first under GPT-5.5 (3.765). `opencode`, which led the original two-benchmark average, drops to third overall because the empty repository removes the Jira-bound requirement traceability (Param 2) and the rich external MCP (Param 6) that lift it on the complex bench.

## Cross-model deltas (GPT-5.5 minus Opus, weighted total)

| Tool | simple Δ | complex Δ | blog Δ | Average Δ |
| --- | ---: | ---: | ---: | ---: |
| cursor | −0.040 | −0.010 | −0.035 | −0.028 |
| openspec | +0.100 | +0.060 | −0.035 | +0.042 |
| ralph | −0.010 | −0.080 | **−0.350** | −0.147 |
| opencode | **+0.135** | +0.060 | +0.070 | +0.088 |

Headline reading: the per-tool ranking is preserved across models on the simple and complex benches (no row flips), but the greenfield bench produces the **one cross-model rank flip in the study** — `ralph` ↔ `openspec` swap the top two, because Ralph's C1–C26 capability matrix is an Opus-only strength that collapses to a free-text bullet list under GPT-5.5 (Param 2: 4→2, Param 4: 5→4). Per-cell deltas span −0.350 (ralph blog) to +0.135 (opencode simple); on 7 of 12 cells Opus 4.8 out-scores GPT-5.5. Param 13 (Human-in-the-loop validation) carries **no** cross-model delta on any cell because OpenSpec and Ralph run Cline-as-extension under both models (driver held constant), scored 4/4 on every bench including the greenfield cells.

## Per-parameter detail

Full 16-parameter score vectors with anchor-defense notes for all 48 cells live in `scores-opus48.csv` and `scores-gpt55.csv` (one row per parameter). The OpenCode per-parameter matrix across all three benches is reproduced in `paper/appendix/G-opencode-bench.tex` (Table `tab:opencode-per-param`); the cross-model per-parameter Δ matrix is in `paper/appendix/F-cross-model-robustness.tex` (Table `tab:cross-model-delta`).

## Defect convergence (two families)

1. **Regex passthrough trap (complex bench).** 5 of 8 (tool, model) complex-bench cells assert that the existing `[a-z]+` extension regex accepts `mp4`; the digit fails. Lands on Cursor and Ralph under both models, never on OpenSpec (parser broadening lives in the spec), and on OpenCode under GPT-5.5 only (its Opus 4.8 plan diagnoses the digit and broadens the class up front). Structural spec format is the model-robust avoidance mechanism.

2. **i18n/content-completeness omissions (greenfield bench).** With no live code to misread, the discriminating defect is omission, clustering under GPT-5.5: its OpenSpec spec drops `x-default`; its Cursor plan omits the root-redirect path and an article-index route; its Ralph plan collapses the capability matrix. Every Opus 4.8 plan front-loads these. Here the stronger **model** (not the structural format) is the robust completeness driver — the mirror image of the regex trap.

## Per-cell costs (end-to-end), both models, three benchmarks

OpenCode costs are cache-aware (provider-reported `cost_estimate_usd`: Opus via Bedrock session export; GPT-5.5 via `opencode_run.sh` per-line-rate backfill). The other three tools use a token-volume × weighted-rate estimate (Opus $19/M, GPT-5.5 $22.5/M, 30/70 input/output mix; `tools/compute_costs.py`). The two paths are not strictly comparable: the rate-card path overestimates real spend on heavy-cache workloads.

| Tool | Bench | Opus k-tok | Opus $ | GPT k-tok | GPT $ | GPT/Opus | Source |
| --- | --- | ---: | ---: | ---: | ---: | ---: | --- |
| cursor | simple | 142.7 | 2.71 | 103.4 | 2.33 | 0.858× | rate-card |
| cursor | complex | 270.8 | 5.15 | 204.2 | 4.59 | 0.893× | rate-card |
| cursor | blog | 282.6 | 5.37 | 115.2 | 2.59 | 0.483× | rate-card |
| openspec | simple | 200.1 | 3.80 | 146.2 | 3.29 | 0.865× | rate-card |
| openspec | complex | 263.2 | 5.00 | 189.2 | 4.26 | 0.851× | rate-card |
| openspec | blog | 223.7 | 4.25 | 102.0 | 2.29 | 0.540× | rate-card |
| ralph | simple | 180.9 | 3.44 | 123.4 | 2.78 | 0.808× | rate-card |
| ralph | complex | 297.9 | 5.66 | 151.9 | 3.42 | 0.604× | rate-card |
| ralph | blog | 249.7 | 4.74 | 113.4 | 2.55 | 0.538× | rate-card |
| opencode | simple | 5797.7 | 9.72 | 2220.8 | 2.45 | 0.252× | provider |
| opencode | complex | 15102.2 | 22.07 | 3589.8 | 3.85 | 0.175× | provider |
| opencode | blog | 40149.0 | 49.57 | 6957.4 | 8.38 | 0.169× | provider |
| **all-cells** | | **63160.4** | **121.48** | **14016.8** | **42.78** | **0.352×** | mixed |

The total dollar cost of the entire **48-cell** primary corpus is **$164.27** ($121.48 Opus 4.8 + $42.78 GPT-5.5). The six OpenCode cells contribute **$96.04 (~58%)** across 73.8 M tokens, of which $81.36 is the three Opus 4.8 cells and **$49.57 the single Opus 4.8 greenfield cell** (40.1 M tokens) — the most expensive cell in the study. Across all three benches the cheapest rate-card paths are `openspec` under Opus 4.8 ($13.05) and `ralph` under GPT-5.5 ($8.75); `cursor` is cheapest only on the simple bench (and carries the $20/mo Cursor Pro floor the open-source tools do not).

## Tool licensing & subscription-floor reading

Token-cost arithmetic ignores the subscription floor each tool's runtime imposes. Per the tools' public pricing pages ([cline.bot/pricing](https://cline.bot/pricing); [cursor.com/pricing](https://cursor.com/pricing); [opencode.ai](https://opencode.ai/)):

- **Cursor** — Hobby (free) tier does *not* include MCP transports; Pro at $20/mo unlocks the MCP picker. Both Cursor benches required Pro, so the Cursor cost figures are token-only on top of a $20/mo floor.
- **Cline** (runtime behind OpenSpec and Ralph) — open-source; MCP on the free tier; pay only the provider's per-token rate. No floor.
- **OpenSpec, Ralph** — open-source CLIs / loop drivers; no subscription floor.
- **opencode** — fully open-source single binary; ships its own provider clients, MCP picker, and headless `opencode run`. No floor; BYOK to any supported provider.

Implication: under GPT-5.5, `cursor` is the cheapest rate-card tool on the simple bench ($2.33, on top of $20/mo Pro); `opencode` is the cheapest cache-aware cell on the complex bench ($3.85) and carries no licensing layer; on the greenfield bench under Opus 4.8 `opencode` is the most expensive cell in the study ($49.57), so the cheapest open paths there are OpenSpec and Ralph.
