# Grill-me Stage-4 Write gate (rev-3 cross-model extension)

**Gate status:** DEFERRED-on-data. The full Stage-4 Write gate cannot
close in this chat because the rev-3 deliverable's primary inputs (the
six GPT-5-3-Codex cell artefacts) do not yet exist on disk. The
follow-up agent that ingests the operator-captured artefacts under
`runs/gpt-5-3-codex/` MUST run the gate to completion (i.e.\ obtain
explicit user approval of the `Resolved Plan` block below or a revised
version of it) before authoring any new prose in
`paper/appendix/F-cross-model-robustness.tex` beyond the scaffold
already in place.

The scaffold itself (Tables F.1, F.2, F.3 with `TBD` cells; the rate-
substitution narrative; the per-parameter expectation list) is
SCAFFOLDING, not deliverable prose, and predates this gate. New
deliverable prose (the actual cross-model findings narrative, the
populated $\Delta$ table, the populated rev-3 OPX table, and any
revisions to §3 / §4 / §5 / abstract that fold the GPT data in)
requires the gate to close first.

## Proposed Resolved Plan (for the follow-up agent to confirm with the user)

```text
Resolved Plan
- Goal: Fold rev-3 GPT-5-3-Codex cells (12 leg-runs, 6 cells) into the
  existing Opus paper without re-scoring the v1 cells, producing a
  cross-model robustness appendix and updated tables/figures.
- Stage: Write
- Inputs:
    research/agent-plan-comparison-final/runs/gpt-5-3-codex/
      <bench>/<tool>/{planning,execution}/metadata.json    (12 files)
      <bench>/<tool>/{planning,execution}/{plan.md,
        plan_source.md, source-diff/*.patch,
        test-output.txt, deviations.json,
        mcp-calls.md, screenshots/}                         (artefacts)
    research/agent-plan-comparison-final/runs/gpt-5-3-codex/_rates.json
    research/agent-plan-comparison-final/scoring/scores.csv  (60 v1 rows;
        will append 60 rev-3 rows)
    research/agent-plan-comparison-final/scoring/gpt-cells-scaffold.md
    research/agent-plan-comparison-final/HANDOFF-MULTIMODEL-RERUN.md
- Structure:
    1. Update §3 Method Cross-model setup subsection (already present)
       only if the operator hit any methodology surprises during the
       rev-3 runs. Otherwise leave unchanged.
    2. Update §4 Results to fold the rev-3 cells in:
         - Table 1 (`tab:aggregate`): add a `model` column or a second
           model-block.
         - Table 3 (`tab:cost`): add a model-block per row group.
         - Table 5 (`tab:exec`): append rev-3 exec rows.
         - Add one paragraph in §4.1 on cross-model agreement /
           disagreement (which tools' weighted totals shifted by what
           and why).
    3. Update §5 Threats: keep the rev-3 paragraph as-is unless the
       runs surfaced a new methodology threat (e.g. Cline asymmetry
       turned out to materially change the OPX result).
    4. Populate Appendix F:
         - Replace the `TBD` cells in tables F.2 and F.3 with the
           computed values.
         - Add a 1-paragraph narrative per (tool, benchmark) pair on
           the most-and-least model-sensitive parameters.
         - Add the cross-model defect-convergence finding (does GPT
           reproduce or avoid the v1 regex-passthrough trap?
           does GPT inline the verbatim 53-entry team_access?).
    5. Add at most ONE new figure (panel-per-model variant of the
       existing aggregate-bars figure). Do not redesign Fig 3 unless
       the cross-model heatmap is qualitatively different from the
       single-model one.
- Style reference: existing v1 paper sections; same voice (third-
  person, hedged where appropriate, assertive where the data is
  unambiguous), same paragraph length, same citation density.
- Deliverable:
    paper/main.pdf            (rebuilt clean, zero undefined refs)
    paper/appendix/F-cross-model-robustness.tex (populated)
    scoring/scores.csv        (60 v1 rows + 60 rev-3 rows = 120 rows)
    scoring/score-aggregates.md (regenerated with two model blocks)
- Done when:
    - tectonic build clean
    - bash tools/guard/scan_forbidden.sh passes
    - all `TBD` markers in Appendix F resolved
    - the follow-up agent's user-visible summary cites which numbers
      moved (v1 -> rev-3) for sanity check
```

## What the gate is NOT closing on now (and why)

- **Per-paper-vs-cross-corpus framing.** Settled by P5 above (per-cell
  $\Delta$ table + cross-model narrative). No further interview needed.
- **Citation style.** Inherits from v1 paper unchanged.
- **Figures plan.** At most ONE new figure (rev-3 model-faceted Fig 2).
  The v1 figures stand.
- **Length and depth budget.** Appendix F is bounded at $\sim 2$ pages.
- **Tone and voice.** Inherits from v1.

The only items that genuinely require user input at gate-close time
are: (a)~which Cline/MCP surface details belong in §3 vs §5 if the
operator's runs surfaced new findings; (b)~whether to add the rev-3
panel to Fig 2 inline or only in Appendix F.

## Gate-close obligation for the follow-up agent

After the operator drops the artefacts and the metadata reconstruction
is done, the follow-up agent MUST emit the `Resolved Plan` block above
(or a revised version) to the user, wait for explicit `approved`, and
only then begin authoring deliverable prose. This file is the standing
context for that interaction.
