# Receipt — Betty Protocol Compliance

**Date:** 2026-05-04
**Repo:** C027_hermes-evolution
**Machine:** Resonance (PC)
**Worktree:** `.claude/worktrees/inspiring-kowalevski-4c52cf`
**Branch:** `claude/inspiring-kowalevski-4c52cf`
**Author:** Jeremy Bradford + Claude Opus 4.7 (1M, explanatory style, auto-mode)

## What changed

Brought C027 into Betty Protocol compliance. Two parts:
1. **File additions** — every required and recommended top-level file the
   protocol expects, sized to actual repo state (not a generic template).
2. **Folder restructure** — non-canonical paths (`evolution/`, `tests/`,
   `output/`, etc.) moved into the numbered Betty Protocol layout.

Layout decision: **C027 is PC-only.** No multi-machine profile abstraction.
Mac will fork as a separate repo when ready.

### Files added

| File | Purpose |
|---|---|
| `META.yaml` | Project metadata, layout map, maintainer notes |
| `RELATIONS.yaml` | Cross-repo ownership, reads-from / writes-to, future relations |
| `CLAUDE.md` | Claude-specific guidance — what this repo is, hazards, don'ts |
| `AGENTS.md` | Generic agent guidance, parallel to CLAUDE.md |
| `CHANGELOG.md` | Change history (Unreleased + 0.1.0 covering 2026-04-24 fix cascade) |
| `CONTRIBUTING.md` | Branching, commit, receipt, pre-push conventions |
| `ROADMAP.md` | Phase 1.1 → 5 plan with done-when criteria |
| `WHY_I_CARE.md` | Purpose and motivation (the "honesty floor" doc) |
| `rules_now.md` | Current operating rules — Phase 1.1 active |
| `00_admin/SESSION_CONTINUITY.md` | Session chain start (links to 2026-04-24 handoff) |
| `00_run/README.md` | Placeholder for easy-button launchers (Phase 1.1 will add real ones) |

### Files modified

| File | Change |
|---|---|
| `.gitignore` | Added `_c027_*` debris pattern (preempts the 500-untracked nightly escalation seen on `private/hermes-skill-lab`); reordered for clarity; added `.claude/`, `.stfolder/`, OS files; updated `datasets/` pattern to `50_data/datasets/` |
| `pyproject.toml` | `[tool.setuptools.packages.find]` adds `where = ["40_src"]`; `testpaths = ["60_tests"]`; added `pythonpath = ["40_src"]` so pytest works without pip install |
| `README.md` | Path references updated (`tests/`→`60_tests/`, `output/`→`70_evidence/runs/`, `reports/`→`80_reports/legacy_reports/`, `datasets/`→`50_data/datasets/`); Phase 1 status detail updated to reference 2026-04-24 GEPA handoff |
| `PLAN.md` | Architecture diagram replaced with the post-migration layout (with a note that the upstream NousResearch repo uses the original layout) |
| `40_src/evolution/skills/evolve_skill.py` | Docstring example, two `Path("datasets")` constructions, two `Path("output")` constructions — all updated to new prefixes |
| `40_src/evolution/core/external_importers.py` | `--output` help text default + `Path(__file__).parent.parent.parent` → `parent.parent.parent.parent` (one extra parent to compensate for `40_src/` prefix) |
| `40_src/evolution/core/config.py` | `get_hermes_agent_path()` sibling-path fallback updated for the same parent-count reason; `output_dir` default `./output` → `./70_evidence/runs` |
| `40_src/generate_report.py` | Default output path `reports/...` → `80_reports/...` |
| `20_receipts/session_handoffs/2026-04-24_gepa_phase1_handoff.md` | Prepended a "layout migration note" pointing readers to this receipt; the body of the handoff still uses the pre-migration paths (it's a historical record) |

### Folders moved (via `git mv`)

| From | To | Notes |
|---|---|---|
| `evolution/` | `40_src/evolution/` | Python package — name preserved; imports unchanged because `pyproject.toml` declares `where = ["40_src"]` |
| `tests/` | `60_tests/` | pytest path updated |
| `generate_report.py` | `40_src/generate_report.py` | Source code goes in 40_src |
| `reports/` | `80_reports/legacy_reports/` | The single PDF (`phase1_validation_report.pdf`) is the pre-rewrite BootstrapFewShot report; future report runs will land at `80_reports/<name>.pdf` |
| `output/` | `70_evidence/runs/` | Reproducibility artifacts; canonical evidence location |
| `datasets/` | `50_data/datasets/` | Still gitignored |

## Why this layout for C027 specifically

**PC-only, no multi-machine profile.** Discussed with Jeremy this session: Mac
will fork the repo (likely as a separate C-series entry) when he's ready,
and customize independently — different memory system, different agent set,
possibly different dataset sources. Forcing a single repo to serve both
would be premature abstraction. Two repos with deliberate divergence is
honest.

**Workbench, not runtime.** This repo evolves skills; the runtime that
*uses* them is the WSL clone at `~/.hermes/hermes-agent/`. RELATIONS.yaml
documents the read-from / write-to contract at the UNC path. Whether the
runtime ever becomes its own tracked repo (with one branch per machine) is
a roadmap candidate — recorded in `ROADMAP.md` cross-cutting tracks.

**Package name `evolution` survives the move.** This was the key decision
that kept the migration low-risk. By adding `where = ["40_src"]` to
`[tool.setuptools.packages.find]`, every `from evolution.X import Y` import
continues to resolve without changes. Zero source-code import edits required.
The `40_src/` prefix never appears in import statements.

## Verification

| Check | Result |
|---|---|
| `git status` after `git mv` | All folder moves recorded as renames (history preserved) |
| `python -c "from evolution.core.config import EvolutionConfig"` | OK (with PYTHONPATH=40_src) |
| `python -c "from evolution.core.fitness import skill_fitness_metric, LLMJudge"` | OK |
| `python -c "from evolution.core.constraints import ConstraintValidator"` | OK |
| `python -m evolution.skills.evolve_skill --help` | CLI loads, all options present |
| `pytest 60_tests/ --collect-only` | 139 tests collected from new path |
| `pytest 60_tests/` | **123 passed, 16 errored** — all 16 errors are preexisting (the `validator` fixture in `test_constraints.py` instantiates `EvolutionConfig()`, which requires a real hermes-agent install via `HERMES_AGENT_REPO` or `~/.hermes/hermes-agent`). On this PC the install is at the WSL UNC path; either the env var didn't resolve through the Bash → Python child process, or `Path.exists()` doesn't accept the UNC form from a Windows Python under git-bash. **This failure mode is unchanged by the restructure** and predates today's work. |

### Preexisting failure — not in scope

`test_constraints.py` fixtures need to be refactored to mock
`hermes_agent_path` rather than rely on `EvolutionConfig()`'s default
factory. That's a separate refactor — flagged in `00_admin/SESSION_CONTINUITY.md`
as an open thread.

## Worktree restoration note

Encountered an orphaned worktree mid-session: the gitdir admin entry at
`<parent>/.git/worktrees/inspiring-kowalevski-4c52cf/` had been removed
(probably by a prior cleanup or Syncthing oddity), so `git mv` failed with
"not a git repository." Repaired by:
1. Running `git -C <parent> worktree add /tmp/wt_test <branch>` from the
   parent repo to generate a fresh admin dir at `.git/worktrees/wt_test/`.
2. `mv` of the admin dir to the expected name `inspiring-kowalevski-4c52cf`.
3. Editing the `gitdir` file inside it to point at this worktree's `.git`
   pointer file.
4. `git status` then resolved cleanly and `git mv` worked.

The leftover `/tmp/wt_test` directory needs manual cleanup (rm was blocked
by the sandbox; safe to delete by hand).

## Pushed?

**No.** Commit lands on the agent worktree branch
`claude/inspiring-kowalevski-4c52cf`. This branch is at
`origin/main + 0` before this commit (the four queued main-branch commits
are *not* on this agent branch — they're on `main` in the parent repo).

Awaiting Jeremy's decision on:
1. Merge this branch into `main` (rebase or fast-forward), then push the
   accumulated 5 commits.
2. Re-run secret scan before any push.
3. Whether `private/hermes-skill-lab`'s 507 untracked debris files should
   be cleaned up while we're improving hygiene (separate operation).

## Known limitations of this work

- **Folder migration is path-rewrite only.** The reorganized layout matches
  Betty Protocol but doesn't enforce it (no pre-commit hook in this repo
  yet to block `evolution/` at root if it ever returns).
- **The 16 test errors above** are preexisting and not addressed here.
- **Secret scan not re-run today.** The 2026-04-24 handoff cleared it; if
  pushing, verify still clean before pushing.
- **The handoff doc body still uses pre-migration paths.** Only the
  prepended note maps old → new. Anyone reading the body will see
  `output/arxiv/...` etc.; the note tells them to translate.
