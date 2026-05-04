# Session Continuity — C027_hermes-evolution

> Lightweight chain document. Each session updates the "Last session" block
> and "Open threads" list. Detailed handoffs go in
> `20_receipts/session_handoffs/`.

## Last session

**Date:** 2026-05-04
**Machine:** Resonance (PC)
**Agent:** Claude Opus 4.7 (1M context), explanatory output style, auto-mode
**Worktree:** `.claude/worktrees/inspiring-kowalevski-4c52cf`
**Branch:** `claude/inspiring-kowalevski-4c52cf`

**What changed:** Betty Protocol compliance — added all required and
recommended top-level files, created numbered top-level folders,
restructured `evolution/` → `40_src/evolution/`, `tests/` → `60_tests/`,
`output/` → `70_evidence/runs/`, `reports/` → `80_reports/`,
`datasets/` → `50_data/datasets/`, `generate_report.py` → `40_src/`.
Updated `pyproject.toml`, `.gitignore`, README, PLAN, and embedded path
references in the `evolution` package. Prepended a path-migration note to
the 2026-04-24 handoff. Receipt: `20_receipts/2026-05-04_betty_protocol_compliance.md`.

**Decisions made this session:**
- C027 stays PC-only. Mac will fork as a separate repo when ready.
- Hermes runtime (the WSL `~/.hermes/hermes-agent/` install) is a
  separate concern — possibly promoted into its own tracked repo with
  one branch per machine, but not yet.
- Layout changes preserved package import paths (the `evolution` Python
  package name didn't change; only the source-root prefix moved).

## Open threads

1. **Push of 4 queued main commits + the compliance commit.** Awaiting
   Jeremy's explicit go-ahead. Re-run secret scan first.
2. **Phase 1.1 — make arxiv evolution trustworthy** (see `ROADMAP.md`):
   - Diff baseline vs evolved arxiv skill, write `comparison.md`.
   - Wire `LLMJudge` and run it on the same baseline/evolved pair.
   - Document reproducible CLI in README.
3. **`optuna` not in `pyproject.toml`.** Decide: declare as optional dep
   or remove MIPROv2 fallback.
4. **Untracked `_c027_*` debris on `private/hermes-skill-lab`** (~500
   files in the parent worktree). `.gitignore` now suppresses the
   pattern going forward; existing files still need cleanup, but that's
   Jeremy's call (could be `git clean -fX` after verifying nothing is
   needed).

## Known hazards

- **WSL UNC path is brittle.** If WSL isn't running, reads from
  `\\wsl.localhost\Ubuntu-22.04\home\jbrad\.hermes\hermes-agent\skills\`
  can fail in subtle ways.
- **Keyword-overlap fitness is a weak signal.** Don't cite the +3.9%
  arxiv number as anything more than "the optimizer moved against this
  metric."
- **The agent worktree is on a `claude/*` branch, not main.** Don't
  expect commits here to be on `main` until they're merged.

## Pointer to most recent detailed handoff

`20_receipts/session_handoffs/2026-04-24_gepa_phase1_handoff.md`
(prepended on 2026-05-04 with a path-migration note — paths in the body
of the handoff reflect the pre-migration layout).
