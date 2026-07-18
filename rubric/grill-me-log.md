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

---

# Operator-experience (OPX) companion rubric — written defenses

The four-parameter operator-experience rubric (O1 setup friction, O2 operator
cognitive load, O3 time-to-first-viable-plan, O4 in-flight iterability; equal
25% weights) is scored per tool, not per (tool × benchmark), because the four
dimensions are properties of the tool surface rather than of the task. The
per-tool scores are O1-O4 = cursor 5/4/5/5 (4.75), opencode 3/3/4/2 (3.00),
openspec 2/2/3/3 (2.50), ralph 2/2/3/2 (2.25). Every extreme (1 or 5) score
carries a defense below; the full opencode row is defended parameter-by-parameter
in response to the round-2 referee request that the row be finalized rather than
left as an anchor sketch.

## O1 Setup friction

- **cursor (5)**: Zero install — Plan Mode is already resident in the IDE the
  operator uses for other work; no CLI, no config file is required before a plan
  can appear. Anchor 5 ("Zero install: already in the IDE / shell the operator
  uses for other work"). Source: cursor planning docs; tool-characteristics.md
  (cursor row).
- **openspec (2)** and **ralph (2)**: CLI install plus one-time convention
  learning (OpenSpec: `npm` install + the proposal/design/spec/tasks scaffold;
  Ralph: authoring `RALPH_TASK.md` and internalising the loop/budget/GUTTER
  semantics). Anchor 2 ("CLI install plus a small number of one-time
  configuration files; conventions documented but still must be learned").
- **opencode (3)**: A single-binary install
  (`curl -fsSL https://opencode.ai/install | bash` or
  `brew install sst/tap/opencode`) followed by provider auth
  (`opencode auth login`) and a per-bench `opencode.json` that wires the MCP
  servers and provider (cell 8 additionally needs a one-time
  `opencode mcp auth Atlassian-MCP-Server` OAuth handshake). This is heavier
  than Cursor's zero-install surface but the per-bench `opencode.json` is a
  single minimal, self-documenting config rather than a multi-file scaffold or a
  set of coupled convention files, so it is read at anchor 3 ("CLI install only;
  conventions are minimal or self-documenting"). The stricter reading (anchor 2,
  counting `opencode.json` + provider/MCP auth as one-time configuration files)
  was considered and rejected at the round-2 finalization: the config is a
  single JSON with a documented schema and no cross-file coupling, and the score
  is retained at 3. Source: `HANDOFF-OPENCODE-RUN.md` Part A (install, provider
  auth, MCP config); `tools/opencode/opencode.{simple,complex}.json`.

## O2 Operator cognitive load

- **opencode (3)**: The operator authors a single prompt string and reads a
  single free-form `OPENCODE_PLAN.md`; the only auxiliary surface held in mind
  is the `session-export.json` schema used for audit/replay. "Single primary
  file plus one auxiliary surface" = anchor 3. There are no coupled sub-formats
  (contrast OpenSpec's proposal vs design vs spec deltas vs tasks). Source:
  `tool-characteristics.md` (opencode row); `runs/*/*/opencode/planning/OPENCODE_PLAN.md`.
- (No extreme score: openspec/ralph sit at anchor 2, cursor at anchor 4.)

## O3 Time-to-first-viable-plan

- **cursor (5)**: Plan content streams immediately in the chat thread and the
  operator watches the model think on screen. Anchor 5. Source: cursor planning
  docs; tool-characteristics.md (cursor row).
- **opencode (4)**: `opencode run` streams plan content within seconds with
  minimal scaffolding (no validator boot, no multi-file scaffold); the six
  captured planning legs reached a complete plan in 98-293 s (Appendix G). It is
  a headless JSON event stream rather than an on-screen chat, so it is not the
  anchor-5 "model thinking on screen" experience. Anchor 4 ("Plan content
  streams within seconds; minimal scaffolding"). Source: Appendix G plan wall
  times; opencode CLI docs (`opencode run --format json`).

## O4 In-flight iterability

- **cursor (5)**: The operator refines the plan in the same chat without
  restarting; the tool absorbs follow-up turns before the one-click hand-off to
  Agent Mode. Anchor 5. Source: cursor planning docs.
- **opencode (2)**: Revising a plan in the headless runtime means re-invoking
  `opencode run` (which overwrites `OPENCODE_PLAN.md` and starts a fresh session
  unless `--continue` is passed); there is no in-thread "revise" turn. Because a
  plain re-invocation overwrites the session, the "prefer the lower anchor"
  tie-break places it at anchor 2 ("Plan revisions require editing files by hand
  and re-running the validator/loop") rather than anchor 3. Source:
  `tool-characteristics.md` (opencode replay/hand-off); `HANDOFF-OPENCODE-RUN.md`
  Part B.
- **ralph (2)**: Refining the plan means editing `RALPH_TASK.md` and re-running
  the loop rather than taking a follow-up turn in chat. Anchor 2.

## Resolution status (OPX)

Round-2 finalization pass: `Resolved Plan: accept_all` with O1=3 for opencode
(operator-approved). The opencode row is no longer an anchor sketch; the totals
above are the canonical operator-experience scores.
