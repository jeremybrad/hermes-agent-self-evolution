# Contributing — C027_hermes-evolution

This is a personal-workspace repo (Jeremy Bradford). External contributions
aren't expected, but the conventions here apply equally to AI agents working
on this code.

## Branching

| Pattern | Use |
|---|---|
| `main` | Stable PC workbench. Pre-push hooks gate. |
| `feature/<short-name>` | New capabilities (Phase 2 tools, new optimizers, etc.) |
| `fix/<short-name>` | Bug fixes |
| `docs/<short-name>` | README / CLAUDE / handoff updates only |
| `refactor/<short-name>` | Layout changes, no behavior change |
| `chore/<short-name>` | Dependency bumps, gitignore tightening |
| `wip/<short-name>` | Work-in-progress; converted from a stash if one happens |
| `claude/<agent-id>` | Agent worktree branches — short-lived, merged into a typed branch above before landing on main |
| `private/<...>` | Local-only branches that intentionally don't push (e.g., `private/hermes-skill-lab`) |

## Commit messages

Conventional commits — `type(scope): description`.

| Type | Use |
|---|---|
| `feat` | New capability |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `refactor` | Restructure without behavior change |
| `test` | Test-only changes |
| `chore` | Tooling, deps, config |

Examples from this repo's history:
```
fix(skills): make skill text the actual GEPA optimization target
fix(skills): bring GEPA + constraints + metric in line with dspy 3.2 API
docs: soften Phase 1 status and document reproducibility convention
feat: add Hermes session importer + fix short skill name matching
```

Scopes that mean something here: `skills`, `core`, `fitness`, `constraints`,
`importers`, `report`, `gepa`, `dspy`, `handoff`.

## What gets a receipt

Every non-trivial change drops a receipt in `20_receipts/`:

```
20_receipts/YYYY-MM-DD_short-description.md
```

Receipts include:
- What changed and why (the PR description, basically)
- Verification evidence — output of tests, smoke runs, file diffs
- Any known regressions or follow-ups

Session handoffs (the longer form) live in
`20_receipts/session_handoffs/YYYY-MM-DD_topic.md`.

## Pre-push checklist

Before pushing to `main`:
1. `pytest 60_tests/ -q` passes
2. `git status` clean (or explicitly documented WIP)
3. No `_c027_*` debris files staged accidentally (the `.gitignore` should
   catch them; verify if you've been running launcher scripts)
4. Receipt(s) written for the change(s) being pushed
5. If the change touched `40_src/evolution/skills/skill_module.py`, manually
   verify GEPA mutations still reach the saved `evolved_skill.md` (run a
   small evolve and diff baseline vs evolved)

## Don't

- Don't merge PRs without running the test suite (`pytest 60_tests/`).
- Don't add `_*.sh` or `_*.py` debris files; if a launcher needs to be
  reusable, it goes in `00_run/` with a real name.
- Don't push secrets. The 2026-04-24 handoff documented a clean scan; if
  you're unsure, run it again before pushing.
- Don't bypass pre-push hooks (`--no-verify`). If a hook fails, fix the
  underlying issue.
