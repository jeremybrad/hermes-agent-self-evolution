# ROADMAP — C027_hermes-evolution

What's queued, in rough order. Each phase has explicit "done when" criteria
so we can decide it's actually done, not just "feels close."

## Phase 1.1 — Make arxiv evolution trustworthy (active)

The 2026-04-24 cascade got GEPA actually running and producing real artifact
deltas, but on a weak metric (keyword overlap) and a tiny synthetic eval set.
Before scaling to more skills, validate this run more thoroughly.

**In order:**

1. **Diff baseline vs evolved** for `70_evidence/runs/arxiv/20260424_184914/`.
   Produce a human-readable comparison. What did GEPA cut, keep, restructure?
   Save alongside the run as `comparison.md`.

2. **Wire and run LLMJudge** (already exists in
   `40_src/evolution/core/fitness.py::LLMJudge`) on the same baseline/evolved
   pair, same eval examples. Save `metrics_llm_judge.json` next to
   `metrics.json`. Compare: does the +3.9% keyword-overlap delta survive
   under rubric scoring, or does it collapse / invert?

3. **Document the reproducible run command** in README.md. Pin model strings
   (`openrouter/openai/gpt-4o`, `openrouter/openai/gpt-4o-mini`), document
   the WSL UNC path requirement, and consider adding a `00_run/evolve_arxiv.ps1`
   easy-button.

4. **Commit `optuna` to `pyproject.toml`** (e.g., as an optional dep) OR
   remove the MIPROv2 fallback path so a clean install doesn't crash.

5. **Triage push of the four queued main commits** (`0d1f87d`, `edb74cf`,
   `4ed2008`, `4c6e3b6`). Re-run secret scan first.

**Done when:** the +3.9% claim is either confirmed or replaced by a
rubric-based number that's more honest, AND a fresh checkout can reproduce
the arxiv run with one command.

## Phase 1.2 — Generalize beyond arxiv

Pick 1–3 next candidate skills for controlled evolution. Top contenders:

- **`github-code-review`** — has clearer ground-truth than arxiv (planted
  bugs vs not-caught bugs). Listed as primary target in PLAN.md.
- **`systematic-debugging`** — can plant bugs and use post-fix test
  pass-rate as a real auto-eval signal.
- **One short utility skill** (e.g., from `productivity/` or
  `note-taking/` in the Hermes skills tree) — to test whether GEPA's
  compression behavior generalizes or was an arxiv-specific artifact.

**Done when:** at least one of the three shows a measurable improvement
under both keyword-overlap AND LLMJudge scoring.

## Phase 2 — Tool description optimization (planned, not started)

Same DSPy + GEPA pattern, applied to tool descriptions in the Hermes
runtime. Different fitness function (likely calling-rate + correctness on
held-out tasks). Documented in the upstream PLAN.md but not yet started here.

## Phase 3 — System prompt sections (planned)

## Phase 4 — Tool implementation code via Darwinian Evolver (planned)

## Phase 5 — Continuous improvement loop (planned)

## Cross-cutting tracks

These aren't phases — they run alongside the above:

### Workbench-runtime contract

The Hermes runtime currently lives at `~/.hermes/hermes-agent/` (a clone of
upstream NousResearch + Jeremy's private skill edits). At some point it
should be a tracked repo with one branch per machine (`hermes/pc`,
`hermes/mac`, `hermes/vps`). When that happens, this repo's
`RELATIONS.yaml.writes_to` gets a real repo reference instead of a UNC
path, and skill promotion can be a `git cherry-pick` instead of a file copy.

### Mac fork

Jeremy will fork this repo (likely as a separate C-series entry) on the Mac
and diverge in:
- Memory system (different from PC's)
- Agent set (different subagent configs)
- Possibly dataset sources

The Mac fork is its own ROADMAP. Skill artifacts may flow PC → Mac
(cherry-pick) but optimizer infrastructure won't necessarily.

### Eval data quality

The synthetic-by-gpt-4o-mini eval data is the weakest link in the whole
pipeline. Real session-derived evals (via the existing
`evolution.core.external_importers`) are higher fidelity. Track:
- How much real session data exists per skill
- Whether GEPA-with-real-data outperforms GEPA-with-synthetic-data
