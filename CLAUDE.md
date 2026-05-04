# CLAUDE.md — C027_hermes-evolution

> Read this first. The global `~/.claude/CLAUDE.md` covers Betty Protocol and
> workspace conventions. This file covers what's specific to C027.

## What this repo is

The **PC-only** Hermes Agent skill-evolution workbench. It uses DSPy + GEPA to
optimize SKILL.md files from the upstream Hermes runtime (which lives in WSL on
this machine), measures the result, and writes evolved variants back as
committed artifacts under `70_evidence/runs/<skill>/<timestamp>/`.

It is **not** the runtime. The runtime is the actual Hermes Agent install at
`\\wsl.localhost\Ubuntu-22.04\home\jbrad\.hermes\hermes-agent\`. This repo
reads from that path and (manually, after validation) writes back to it.

It is **not** the Mac counterpart. Mac will fork this repo — likely as a
separate C-series entry — and configure differently (probably a different
memory system and a different agent set). Don't add Mac-specific code paths
here. If you're tempted to write `if platform == "darwin"`, stop and check
whether that belongs in the Mac fork instead.

## Layout

| Path | Purpose |
|---|---|
| `40_src/evolution/` | Main package — `evolution.core.*`, `evolution.skills.*`, `evolution.tools.*`, etc. |
| `40_src/generate_report.py` | PDF generator (writes to `80_reports/`) |
| `60_tests/` | pytest suite |
| `70_evidence/runs/<skill>/<timestamp>/` | Committed run artifacts (baseline + evolved + metrics) |
| `80_reports/` | Generated PDFs and validation summaries |
| `50_data/datasets/` | Gitignored — synthetic eval data, regenerated on demand |
| `30_config/` | Model routing, skill-scope configs, env defaults |
| `00_run/` | Easy-button launchers (PowerShell + .cmd) |
| `00_admin/SESSION_CONTINUITY.md` | Last session's state, open threads, hazards |
| `20_receipts/` | Change receipts and `session_handoffs/` |

The `evolution` Python package is at `40_src/evolution/` but pyproject.toml
declares `where = ["40_src"]`, so imports stay `from evolution.X import Y`
unchanged. The `40_src/` prefix never appears in import statements.

## Commands you'll actually run

```powershell
# Setup (first time)
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -e ".[dev]"

# Tests
pytest 60_tests/ -q

# GEPA optimization run (the canonical one)
$env:HERMES_AGENT_REPO = "\\wsl.localhost\Ubuntu-22.04\home\jbrad\.hermes\hermes-agent"
python -m evolution.skills.evolve_skill `
  --skill arxiv `
  --iterations 5 `
  --eval-source synthetic `
  --optimizer-model openrouter/openai/gpt-4o `
  --eval-model openrouter/openai/gpt-4o-mini

# Generate validation report
python 40_src/generate_report.py
```

## Reproducibility convention

Committed run artifacts under `70_evidence/runs/<skill>/<timestamp>/` are the
archive. Eval datasets in `50_data/datasets/` are intentionally gitignored —
they're synthetic and regenerated on each run, so pinning them would overstate
determinism.

If a run produced numbers worth citing, the run directory must be committed.
If you lose this repo, you lose the evidence — git is the archive.

## Hazards

- **WSL UNC path is required.** Hermes runtime lives at
  `\\wsl.localhost\Ubuntu-22.04\home\jbrad\.hermes\hermes-agent\`, not on the
  Windows side of SyncedProjects. The path is brittle — if WSL isn't running,
  reads fail silently in subtle ways.
- **`optuna` is not in `pyproject.toml`.** It's installed in `C:\Python313`
  globally and is required by the MIPROv2 fallback. Clean installs will
  break MIPROv2 until this is fixed (see ROADMAP.md).
- **Keyword-overlap fitness is a weak signal.** The +3.9% on the
  2026-04-24 arxiv run is real against that metric — it's not yet validated
  against an actual agent's runtime performance. LLM-as-judge scoring is the
  highest-ROI next step (Phase 1.1).
- **The evolved skill artifact must be the optimization target.** Bug 4b in
  the 2026-04-24 cascade was that GEPA mutations weren't reflected in the
  saved `evolved_skill.md`. Fix is in `40_src/evolution/skills/skill_module.py`
  — `skill_text` becomes `predictor.signature.instructions`, not a runtime
  input field. If you refactor that module, preserve this property or you'll
  silently regress to "metrics improve, artifact doesn't change."
- **Don't run heavy GEPA passes against the WSL hermes-agent install while
  another process is mutating it.** Read-only by default; only write back
  after manual validation.

## Operating context

Read `20_receipts/session_handoffs/` (most recent first) for what the prior
session did. Then read `00_admin/SESSION_CONTINUITY.md` for current open
threads. Don't start substantive work without scanning both — the handoff
chain is how cross-session reasoning survives.

## Don'ts

- Don't add Mac-specific or VPS-specific code paths here. Fork the repo.
- Don't move `evolution` out of `40_src/` without also updating
  `pyproject.toml`'s `[tool.setuptools.packages.find] where`.
- Don't commit anything from `50_data/datasets/` — it's gitignored on purpose.
- Don't push the four queued main commits without re-running the secret scan
  (the 2026-04-24 handoff cleared it then; verify still clean now).
- Don't let the upstream NousResearch repo dictate this repo's roadmap. The
  upstream is a fork point, not a master. We diverge where it serves the PC
  use case.
