# Supplementary Materials — Benchmarking Planning in AI Coding Agents

Supplementary materials and reproducibility artifacts for the paper:

> **Benchmarking Planning in AI Coding Agents: A Cross-Model Study of Four Planning Surfaces**
> Luis Herrera-Izquierdo (Independent Researcher), 2026.

This repository hosts the supporting data referenced by the manuscript's
*Supplementary Materials* and *Data Availability* statements. It is a companion
to the paper, not the paper itself.

## Study at a glance

- **4 planning surfaces:** Cursor Plan Mode, OpenSpec, Ralph, opencode.
- **3 reproducible benchmarks:** a single-file Terraform change
  (`github-repository-bootstrap`), a ticket-driven multi-file Cloudflare Worker
  feature (`worker-video-feature`), and a greenfield bilingual blog
  (`personal-blog-scaffold`).
- **2 frontier models:** Anthropic Claude Opus 4.8 and OpenAI GPT-5.5.
- **48 captured cells**, each scored on a frozen 16-parameter weighted
  engineering-rigor rubric and a 4-parameter operator-experience rubric.

## Contents

| Path | What it holds |
|------|---------------|
| `supplementary-materials.pdf` | The compiled Supplementary Materials document (Appendices A–H, Tables S1–S13). Appendix H reports the repeat-run variance for the close-margin OpenSpec/Ralph Opus 4.8 cells. |
| `source/` | LaTeX source for the supplementary document: `supplementary.tex`, `appendix/*.tex`, native TikZ `figures/*.tex`, and the `references.bib` database. |
| `rubric/` | The full engineering-rigor rubric and anchors (`rubric.md`) plus the grill-me decision-gate logs that fixed the scoring scheme before any cell was scored. |
| `prompts/` | The three frozen benchmark prompt bodies issued verbatim to every surface/model. |
| `scoring/` | Per-cell scoring CSVs for both models (`scores-opus48.csv`, `scores-gpt55.csv`) and the aggregate roll-up (`score-aggregates.md`). Repeat-run variance for the close-margin cells: full per-parameter repeat vectors (`scores-opus48-repeats.csv`), compact per-cell totals (`variance-repeats.csv`), and the analysis narrative (`variance-analysis.md`). |
| `metadata/` | Per-leg `metadata.json` for all 48 cells (planning + execution), preserving the `runs/<model>/<benchmark>/<surface>/<phase>/` layout. For the six close-margin OpenSpec/Ralph Opus 4.8 cells, the two additional repeats are published as full captures (plan artifacts, timing, token counts, and screenshots) under `.../repeat-{1,2}/`; the original single run is the metadata-only `planning/` and `execution/` legs (repeat-0). |

## How to read the artifacts

- **Scoring CSVs** are the source of truth for every number in the paper's
  tables and figures. Each row is one cell × one rubric parameter; weighted
  totals are rolled up in `scoring/score-aggregates.md`.
- **`metadata.json`** records the per-leg provenance for each cell (model,
  surface, phase, timing, verified MCP-call counts, and the Git refs / branches
  the agent operated on).
- **Prompt bodies** are immutable: the single-file and video-support benchmarks
  pin a pre-feature reference state by Git ref; the greenfield blog benchmark
  pins an empty seed repository and commits only to never-merged dedicated
  branches.
- **Repeat runs** (`.../repeat-{1,2}/`) re-execute the six close-margin
  OpenSpec/Ralph Opus 4.8 cells twice more under the frozen protocol, to bound
  the run-to-run variance of the sub-0.1-point margin between the two tools. See
  `scoring/variance-analysis.md` and Appendix H of the PDF; the original run is
  frozen as repeat-0.

## Rebuilding the supplementary PDF

The files under `source/` are provided for transparency. The document targets
the MDPI *Engineering Proceedings* (`engproc`) LaTeX class and is compiled with
[Tectonic](https://tectonic-typesetting.github.io/). It is extracted from the
full manuscript build and expects the MDPI class assets to be present; the
pre-compiled `supplementary-materials.pdf` is the canonical rendered output.

## Citation

If you use these artifacts, please cite the paper. A BibTeX entry is available
in `source/references.bib`.

## License

The artifacts and data in this repository are released for academic
reproducibility. The LaTeX class assets used to render the PDF remain under
their respective upstream licenses.
