# 🧬 Hermes Agent Self-Evolution

**Evolutionary self-improvement for [Hermes Agent](https://github.com/NousResearch/hermes-agent).**

Hermes Agent Self-Evolution uses DSPy + GEPA (Genetic-Pareto Prompt Evolution) to automatically evolve and optimize Hermes Agent's skills, tool descriptions, system prompts, and code — producing measurably better versions through reflective evolutionary search.

**No GPU training required.** Everything operates via API calls — mutating text, evaluating results, and selecting the best variants. ~$2-10 per optimization run.

## How It Works

```
Read current skill/prompt/tool ──► Generate eval dataset
                                        │
                                        ▼
                                   GEPA Optimizer ◄── Execution traces
                                        │                    ▲
                                        ▼                    │
                                   Candidate variants ──► Evaluate
                                        │
                                   Constraint gates (tests, size limits, benchmarks)
                                        │
                                        ▼
                                   Best variant ──► PR against hermes-agent
```

GEPA reads execution traces to understand *why* things fail (not just that they failed), then proposes targeted improvements. ICLR 2026 Oral, MIT licensed.

## Quick Start

```bash
# Install
git clone https://github.com/NousResearch/hermes-agent-self-evolution.git
cd hermes-agent-self-evolution
pip install -e ".[dev]"

# Point at your hermes-agent repo
export HERMES_AGENT_REPO=~/.hermes/hermes-agent

# Evolve a skill (synthetic eval data)
python -m evolution.skills.evolve_skill \
    --skill github-code-review \
    --iterations 10 \
    --eval-source synthetic

# Or use real session history from Claude Code, Copilot, and Hermes
python -m evolution.skills.evolve_skill \
    --skill github-code-review \
    --iterations 10 \
    --eval-source sessiondb
```

## What It Optimizes

| Phase | Target | Engine | Status |
|-------|--------|--------|--------|
| **Phase 1** | Skill files (SKILL.md) | DSPy + GEPA | 🟡 Proof of concept |
| **Phase 2** | Tool descriptions | DSPy + GEPA | 🔲 Planned |
| **Phase 3** | System prompt sections | DSPy + GEPA | 🔲 Planned |
| **Phase 4** | Tool implementation code | Darwinian Evolver | 🔲 Planned |
| **Phase 5** | Continuous improvement loop | Automated pipeline | 🔲 Planned |

**Phase 1 status detail.** The full pipeline (load skill → generate synthetic eval dataset → wrap as DSPy module → optimize → validate constraints → score on holdout → save artifacts) is implemented and has been executed end-to-end once on the `arxiv` skill with **DSPy BootstrapFewShot** (not GEPA), then re-run end-to-end with GEPA on 2026-04-24 (see `20_receipts/session_handoffs/2026-04-24_gepa_phase1_handoff.md`). The GEPA arxiv run produced a +3.9% holdout improvement and -75% body size on the keyword-overlap proxy. See `80_reports/legacy_reports/phase1_validation_report.pdf` for the original BootstrapFewShot run and `70_evidence/runs/<skill>/<timestamp>/` for reproducibility artifacts from each subsequent run.

## Engines

| Engine | What It Does | License |
|--------|-------------|---------|
| **[DSPy](https://github.com/stanfordnlp/dspy) + [GEPA](https://github.com/gepa-ai/gepa)** | Reflective prompt evolution — reads execution traces, proposes targeted mutations | MIT |
| **[Darwinian Evolver](https://github.com/imbue-ai/darwinian_evolver)** | Code evolution with Git-based organisms | AGPL v3 (external CLI only) |

## Guardrails

Every evolved variant must pass:
1. **Full test suite** — `pytest 60_tests/ -q` must pass 100%
2. **Size limits** — Skills ≤15KB, tool descriptions ≤500 chars
3. **Caching compatibility** — No mid-conversation changes
4. **Semantic preservation** — Must not drift from original purpose
5. **PR review** — All changes go through human review, never direct commit

## Reproducibility Convention

Runs are kept reproducible by committing run artifacts to `70_evidence/runs/` even though the eval datasets that drive them are intentionally gitignored (they're non-deterministic synthetic data, regenerated on demand). The convention:

- `70_evidence/runs/<skill>/<timestamp>/baseline_skill.md` — the skill as it was before the run
- `70_evidence/runs/<skill>/<timestamp>/evolved_skill.md` — the skill as the optimizer rewrote it
- `70_evidence/runs/<skill>/<timestamp>/metrics.json` — scores, sizes, optimizer config, elapsed time

`evolve_skill.py` writes all three automatically. Commit them with each meaningful run so the numbers in any validation report have checked-in backing evidence. If you lose the repo, you lose the evidence — git is the archive.

Datasets (`50_data/datasets/**/*.jsonl`) are gitignored by design: they're regenerated from the skill text on each run, so pinning them would overstate determinism.

## Full Plan

See [PLAN.md](PLAN.md) for the complete architecture, evaluation data strategy, constraints, benchmarks integration, and phased timeline.

## License

MIT — © 2026 Nous Research

Ecosystem placement: see C010 `registry/repos.yaml` entry `C027_hermes-evolution` for parked disposition, revival trigger, and governance contracts.
