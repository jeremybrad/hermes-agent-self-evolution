# AGENTS.md — C027_hermes-evolution

Generic agent guidance for any AI assistant working on this repo (Claude,
Codex, Copilot, Cursor, etc.). Pairs with `CLAUDE.md`, which has
Claude-specific notes.

## What you're operating

The PC-only Hermes Agent skill-evolution workbench. **Workbench, not runtime.**
You're optimizing skills that live in WSL at
`\\wsl.localhost\Ubuntu-22.04\home\jbrad\.hermes\hermes-agent\skills\`.

## Before you do anything

1. Read `00_admin/SESSION_CONTINUITY.md` — what's the current state?
2. Read the most recent file under `20_receipts/session_handoffs/` —
   what did the prior session decide and leave open?
3. Read `META.yaml` and `RELATIONS.yaml` — what does this repo own,
   what does it deliberately *not* own?
4. Skim `ROADMAP.md` — is this work part of an existing phase plan?

If those four sources contradict the user's instruction, surface the conflict.
Don't silently pick one.

## Hard rules

- **PC-only.** No Mac- or VPS-specific code paths. If the user wants Mac
  behavior, the answer is "fork this repo," not "branch the code."
- **No automatic writes to the WSL hermes-agent install.** Reads are fine.
  Writes back to skills require explicit human go-ahead per the Betty Protocol
  Communication Guardrails.
- **Receipts on non-trivial changes.** `20_receipts/YYYY-MM-DD_short_description.md`
  with what changed, why, and verification evidence.
- **Run artifacts under `70_evidence/runs/<skill>/<timestamp>/` are committed.**
  Datasets under `50_data/datasets/` are gitignored. Don't reverse this.
- **Don't add `if platform == "darwin"` here.** Mac belongs in a Mac fork.

## Your tools

- `pytest 60_tests/ -q` — must pass before any change is considered done
- `python -m evolution.skills.evolve_skill --help` — the optimization CLI
- `python 40_src/generate_report.py` — PDF reports
- `git mv` — for tracked-file moves (preserves history)

## Common failure modes

- **Imports broken after a folder move.** The `evolution` package is at
  `40_src/evolution/` but pyproject.toml's `where = ["40_src"]` makes
  `from evolution.X import Y` work. If imports start failing, that line
  is the first thing to check.
- **GEPA "metric must accept five arguments".** dspy 3.2 signature.
  `skill_fitness_metric` already accepts the variadic form; don't refactor
  it back to 3-arg.
- **MIPROv2 crashes on missing `optuna`.** It's installed globally on this
  machine but not declared in pyproject.toml. Either declare it or accept
  that MIPROv2 fallback is broken on clean installs.
- **Saved evolved_skill.md is identical to baseline.** Means GEPA mutated
  signature instructions but the saved artifact pulled from `skill_text`
  attribute. The fix (committed 2026-04-24) wires `skill_text` into
  `predictor.signature.instructions`. Verify it survives any refactor of
  `40_src/evolution/skills/skill_module.py`.

## Communication

- Be terse. The operator (Jeremy) reads diffs faster than prose.
- Lead with findings, not preamble.
- Flag uncertainty explicitly. "Consistent with X" beats "X is true."
- Never draft external comms (email, Slack, Teams) without explicit
  go-ahead per global CLAUDE.md communication guardrails.
