# <Project Name> — Working Notes for Claude Code

This file is the handoff/status doc (it plays the `HANDOFF.md` role the working
rules describe). **Keep it current: update the "Current status" section in the
same commit as every completed task**, so any Claude Code session (or the
author) can resume cold. Author is <name> (GitHub `<username>`).

> **Working rules:** follow
> <https://raw.githubusercontent.com/tarnos12/claude-rules/master/RULES.md>
> (canonical; cloud/remote sessions that can't see local config should fetch
> this raw URL and follow it). Design/scope source of truth: [DESIGN.md](DESIGN.md).
> Parallel sessions? see [PARALLEL_SESSIONS.md](PARALLEL_SESSIONS.md). Read these
> before starting.

## What this is

<One paragraph: what the project is, the stack, and any hard constraints
(e.g. "no build step", "offline-first", "targets X"). Link the design doc as
the source of truth for scope.>

Repo: <url> (branch `<default-branch>`).

## Run & test

- Dev server / build / run command: `<command>` — <how to launch it>.
- **After a change, tell the author how to see it running** (URL / command /
  preview link) and verify it yourself before claiming done.
- Prefer the fastest feedback loop that proves it: exercise pure/core logic
  headless (a script or unit test) rather than always driving the full app.
- <Environment quirks worth knowing, e.g. "screenshots time out here — verify
  via DOM inspection instead">.

## Current status (update this section every commit)

**<Current stage / phase>.**

Done:
- <bullet per shipped capability — enough that a cold reader knows what exists>

Next (recommended order):
1. <the immediate next task>
2. <then this>

## Architecture conventions (hold these)

<The load-bearing rules that keep the codebase coherent. Fill in yours. Common
patterns that tend to pay off:>
- **One pure core.** Keep the central logic a deterministic, side-effect-free
  function (e.g. `resolve(input, seed)`); never leak I/O, UI, or global state
  into it. It's what makes it testable and reusable.
- **One canonical data shape** shared across the variants that use it, instead
  of parallel schemas.
- **Single aggregation/derivation pipeline** — derive computed values from
  sources every time; never mutate-in-place. New sources plug into the one
  pipeline.
- **External/other-party systems behind an interface**, even when stubbed now,
  so a real implementation swaps in without touching callers.
- **Prefer additive, self-contained modules** over editing shared hot files —
  fewer conflicts, especially with parallel sessions.
- **Persistence is versioned** with a migration path; persist timestamps so
  wall-clock/offline behavior is free.

## TESTING-ONLY — strip before ship

<List every debug/testing convenience and exactly how to remove it, so the
pre-release cleanup is mechanical. Mark them `TESTING ONLY` in code too.>

## Workflow

- One commit per completed task; **update "Current status" above in that same
  commit.** (Full rules: the RULES.md link at the top.)
- <commit message trailer / signing convention, if any>.
- <platform quirks, e.g. "CRLF warnings on Windows are expected/harmless">.
