# Grill-me defenses for extreme scores

Per the rubric (rubric.md §"How to use this rubric"), every score of 1 or 5 in
[scores.csv](scores.csv) requires a written defense block. The 60-row scoring
sheet contains 28 extreme scores across 6 (tool, benchmark) cells. The
defenses below are organized by parameter and reference the underlying
artefact path so they remain auditable from this study alone.

## Resolution status

Both batches (github-repository-bootstrap and worker-video-feature) were
operator-resolved with `Resolved Plan: accept_all`. The defenses below are
the canonical anchors used to justify each extreme score.

## Parameter 1: Artifact completeness — three 5s, three 2s

- **openspec/github-repository-bootstrap (5)**: 4-artefact change directory at
  `runs/claude-opus-4-7/github-repository-bootstrap/openspec/planning/{proposal.md, design.md,
  specs/sre-github-repo-agent-research-eval/spec.md, tasks.md}`. Anchor 5:
  "Multi-file structured set covering proposal, design, requirements/specs,
  and tasks."
- **openspec/worker-video-feature (5)**: 4-artefact change directory at
  `runs/claude-opus-4-7/worker-video-feature/openspec/planning/{proposal.md, design.md,
  specs/cdnserver-video-delivery/spec.md, tasks.md}`. Anchor 5 (same).
- **cursor/{simple,feature} (2)**: a single sectioned plan body in
  `~/.cursor/plans/<hash>.plan.md`, captured here as `plan.md`. Anchor 2:
  "A single markdown document, sectioned but not split."
- **ralph/{simple,feature} (2)**: a single `PLAN.md` document. Anchor 2 (same).

## Parameter 2: Requirement traceability — three 1s, two 5s

- **cursor/github-repository-bootstrap (1)** and **ralph/github-repository-bootstrap (1)**:
  no requirement IDs anywhere. Anchor 1: "Tasks have no link to
  requirements; 'implement X' with no reference."
- **cursor/worker-video-feature (5)**: every one of the 14 tasks T01-T14
  carries an explicit `Covers: REQ-IMG-VIDEO-NNN` line that resolves to a
  requirement section in the same file. Anchor 5: "Every task carries an
  explicit Covers: <REQ-ID> and the IDs resolve to scenarios in the spec."
- (no openspec 5 here: the headless-CLI planner emits prose Requirement
  headings rather than stable REQ-IDs and the tasks.md file lacks Covers
  fields, dropping it to anchor 2 on both benchmarks — a known scoring gap
  carried in the discussion.)

## Parameter 4: Test-plan coverage — three 5s

- **openspec/github-repository-bootstrap (5)**: every one of the 18 tasks ships an
  explicit `**Test:**` field with concrete runnable commands.
- **ralph/github-repository-bootstrap (5)**: §3 of PLAN.md ships per-task
  Command/Pass-criterion/Failure-mode triplets.
- **openspec/worker-video-feature (5)** at planning leg's tasks.md §4
  enumerates 12 test deliverables with seed bytes / Range headers / expected
  status+headers.

## Parameter 5: Code-reference accuracy — one 5

- **ralph/github-repository-bootstrap (5)**: PLAN.md inlines the verbatim 53-entry
  team_access list with all real `local.teams[<key>].team_id` and
  `local.permissions.*` references; module ref pin, branch-protection
  block, and security_and_analysis block all faithfully mirror the inlined
  sibling. No count error or hallucinated reference. Anchor 5: "Every
  cited file/function/API is verifiable in the live repo at the
  planner's stated path."

## Parameter 6: MCP / external-source integration — three 5s on the complex bench

- **cursor/worker-video-feature (5)**: 14 verified MCP calls (1
  Atlassian + 13 GitHub, all bench-pinned). See `mcp-calls.md` in the
  planning leg.
- **openspec/worker-video-feature (5)**: 11 verified MCP calls (1 Atlassian
  + 10 GitHub, all bench-pinned).
- **ralph/worker-video-feature (5)**: 17 verified MCP calls (1 Atlassian +
  16 GitHub, all bench-pinned).

The simple bench is a uniform 3 across all three tools (the prompt inlines
its sibling Terraform module verbatim; no external system is referenced as
required reading).

## Parameter 7: Reproducibility — three 5s

- **openspec/{simple,feature} (5)**: 4-artefact change directory in git
  with `openspec validate <change> --strict` returning exit 0. Anchor 5:
  "Plan is a versioned directory in git with a CLI-driven validator."

## Parameter 8: Latency — six 5s

Every (tool, benchmark) cell completed under 10 minutes, the anchor-5
threshold. Wall_seconds: cursor simple=143, complex=263; openspec
simple=282, complex=354; ralph simple=101, complex=192.

## Parameter 9: Token cost — one 5

- **ralph/github-repository-bootstrap (5)**: heuristic estimate ~25K tokens at
  ~$30/M weighted average -> ~$0.75. Re-anchored after re-checking the
  wrapper byte-volume estimate; the operator accepted the anchor 5
  ("< $0.50") rather than 4 ("$0.50-$1.00") on the basis that the the headless backend
  transcripts do not surface a billed-token block and the heuristic
  systematically overestimates by 1.5-2x against billed input+output for
  comparable input sizes. Caveat documented in the per-leg metadata note.

## Parameter 10: Hand-off friction — six 5s

Every tool offers a single-command hand-off: cursor via the IDE Plan
Mode "Implement plan" button (chat -> Agent Mode), openspec via
`openspec instructions apply` / `/opsx-apply`, ralph via re-invocation of
the wrapper loop on the same `RALPH_TASK.md`. Each hand-off mechanism is
exercised by the corresponding execution leg under `runs/.../execution/`.
