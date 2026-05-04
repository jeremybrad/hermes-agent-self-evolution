# Why I Care

## The asymmetry

A skill is just text. Maybe 5–15 KB of instructions an LLM reads to know
how to perform a task. Hand-writing one takes hours. Hand-tuning one
across hundreds of trials takes days, and most of the trials don't make
it into the prompt because nobody remembers what worked. The skill
plateaus at "Jeremy's best guess from his last 30 minutes of editing."

GEPA flips that. It reads execution traces — what actually happened when
the skill ran, not what the author hoped — and proposes targeted edits.
Hundreds of trials become hundreds of trials. The plateau is wherever the
metric runs out of signal, not wherever the author runs out of patience.

That's worth caring about.

## Why on a workbench instead of in the runtime

Skills evolve under selection pressure. Selection pressure is a metric.
Metrics that exist in the runtime are runtime metrics — slow, noisy,
expensive to gate on. Metrics that exist in the workbench can be
synthetic, deterministic, and cheap to iterate against.

So: optimize on the workbench, deploy to the runtime, monitor for
regression. Two systems, one feedback loop. The workbench (this repo)
is where I get to be unromantic about what works. The runtime is where
results matter.

## Why PC-only

PC is where I do real work most days. PC has the WSL Hermes install with
my private skill edits already merged. PC has the GPU if I ever need it.
Mac is for portability and conversation, not for running 5-iteration GEPA
passes that take three minutes and $0.15 each.

The Mac fork will optimize for what Mac is good at, not what PC is good at.
Forcing one repo to serve both is the kind of premature abstraction that
costs more than it saves. Two repos with deliberate divergence is honest.

## What I'm actually after

Not "an AI that improves itself." That phrase carries too much weight and
not enough specifics. What I'm after is:

- A trustworthy way to know whether a skill rewrite is *better* than the
  original, where "better" is defined and measurable.
- A way to iterate on prompts faster than I can iterate by hand.
- An archive of what worked and what didn't, so I'm not relearning the
  same lessons in six months.
- Eventually, a way to keep the skills good as the underlying models
  change. Skills written for Sonnet 4.5 will rot on Opus 5; the workbench
  is how I notice and fix that without re-doing the work from scratch.

Phase 1 is the first proof that any of this is possible. Phase 1.1 is
the proof that I'm not Goodharting on my own metric. Everything after
is scale.

## Honesty floor

I'm one person. The runtime install is in WSL because that's where it
fits, not because that's the right architecture. The eval data is
synthetic because I haven't built the real-data pipeline yet. The
+3.9% on arxiv is a small number against a weak metric on a tiny
sample. None of this is a finished system. All of it is worth the
hours it took.

If the next session of work doesn't move at least one of those facts
in a better direction, the session was a waste. If it does, the
investment compounds.
