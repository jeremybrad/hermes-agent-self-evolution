# Rules Now — C027_hermes-evolution

> Current operating rules. Edit when reality changes; don't let this drift.
> Last updated: 2026-05-04

## Active phase

**Phase 1.1** — make arxiv evolution trustworthy before scaling to other
skills. See `ROADMAP.md` Phase 1.1 for ordered tasks.

## Hard constraints

- **PC-only.** Mac is a future fork. Don't add platform branches.
- **Workbench, not runtime.** Hermes runtime lives at WSL
  `~/.hermes/hermes-agent/`. This repo reads from it; writes only with
  explicit go-ahead.
- **Run artifacts are evidence.** Commit `70_evidence/runs/<skill>/<timestamp>/`
  for any run worth citing. Datasets stay gitignored.
- **Tests must pass before any push to `main`.** `pytest 60_tests/ -q`.

## Current state, plain

- 4 commits queued on `main` ahead of `origin/main`. Not yet pushed.
  Re-run secret scan before pushing (see 2026-04-24 handoff for the prior
  scan; verify still clean).
- One honest GEPA run on arxiv: +3.9% holdout, -75% body size on
  keyword-overlap proxy. Real artifact delta. Weak metric.
- LLMJudge exists in code (`40_src/evolution/core/fitness.py`) but has
  not been used in any committed run.
- `optuna 4.8.0` is installed in `C:\Python313` but not in `pyproject.toml`.
  MIPROv2 fallback breaks on clean installs until this is fixed.
- Untracked clutter on the *parent* worktree (on `private/hermes-skill-lab`):
  ~500 `_c027_*` debris files. Don't touch them from this worktree;
  `.gitignore` now suppresses the pattern going forward.

## Active worktrees

| Path | Branch | Purpose |
|---|---|---|
| `C:\Users\jerem\SyncedProjects\C027_hermes-evolution` | `private/hermes-skill-lab` | Private-eval / skill-lab experiments. Does not push. |
| `.claude/worktrees/inspiring-kowalevski-4c52cf` (THIS) | `claude/inspiring-kowalevski-4c52cf` | Agent worktree for the Betty Protocol compliance work |

## Decisions in flight

- **Branch landing:** the Betty Protocol compliance commit lands first on
  the agent branch, then merges into `main` (rebase or fast-forward).
  Jeremy decides; default is rebase to keep `main` linear.
- **Push of the 4 queued commits + the compliance commit:** wait for explicit
  go-ahead. The 2026-04-24 handoff cleared the secret scan; need fresh
  verification.
- **C028 / Mac fork:** not created yet. When Jeremy is ready to set up
  Hermes on the Mac, fork from this repo's then-current state.

## Don't break

- `evolution` package import path. After the 2026-05-04 layout migration,
  `evolution/` lives under `40_src/`, but `pyproject.toml` declares
  `where = ["40_src"]`, so `from evolution.X import Y` continues to work.
  Don't change either side without changing both.
- `skill_text` → `predictor.signature.instructions` wiring in
  `40_src/evolution/skills/skill_module.py`. This was bug #4b in the
  2026-04-24 cascade; if it regresses, evolved skills go silently
  back to "metrics improve, artifact unchanged."
- Reproducibility convention: `70_evidence/runs/<skill>/<timestamp>/`
  with `baseline_skill.md`, `evolved_skill.md`, `metrics.json`. Don't
  scatter run artifacts elsewhere.
