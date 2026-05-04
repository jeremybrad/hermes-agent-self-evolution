# 00_run — Easy-button launchers

Reusable launchers that wrap common workflows. PowerShell (`*.ps1`) and
Windows command (`*.cmd`) only — this is the PC repo.

## Conventions

- One launcher per workflow, named for what it does (`evolve_arxiv.ps1`,
  not `run.ps1`).
- Every launcher echoes the resolved command before running it, so the
  invocation is reproducible from logs.
- Launchers must work from a clean checkout: any required env vars
  documented in the launcher header.
- Don't put one-off debug scripts here. Those live nowhere — they
  shouldn't exist after the session that needed them.

## Planned launchers (Phase 1.1)

- `evolve_arxiv.ps1` — the canonical successful run from 2026-04-24:
  `optimizer=gpt-4o`, `eval=gpt-4o-mini`, 5 iterations, synthetic eval
  source. Reproduces `70_evidence/runs/arxiv/20260424_184914/` ±
  randomness in synthetic data generation.
- `judge_run.ps1` — runs `LLMJudge` against a previously-saved run
  directory and writes `metrics_llm_judge.json` alongside `metrics.json`.

These haven't been written yet. Add them as Phase 1.1 lands.
