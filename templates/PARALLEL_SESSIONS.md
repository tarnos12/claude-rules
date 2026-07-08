# Parallel Work Protocol — Workflow Orchestration

A reusable model for getting more than one Claude Code agent working on the same repo at once **without** stepping on each other. This is the current protocol. It **replaces** two earlier models: the "in-session agent team spawned ad hoc" phrasing (a manager casually spinning up helper subagents mid-conversation) and, before that, "Central Dispatch across separate sessions" (independent human-run sessions coordinating through a shared `TASKS.md`/`TASK_<n>.md` board on a coordination branch). Neither is used anymore — the unit of parallelism is now a **Workflow script** that one orchestrator authors and runs.

## Model

One agent is the **orchestrator**: planner, orchestrator, and reviewer in a single role. It does not hand-write every line of the feature work; instead it **authors a dynamic Workflow script** that fans work out deterministically and pulls it back together.

- **The Workflow is a script, not a chat.** The orchestrator composes the run out of primitives — `agent()` to spawn a single subagent with a scoped brief, `pipeline()` to sequence steps where each feeds the next, `parallel()` to fan several subagents out at once — and groups them into **phases**. Fan-out is deterministic: the same script produces the same shape of run every time, so who does what and in what order is decided up front, not improvised.
- **Model-by-complexity.** Each subagent is launched on the tier its slice warrants: **Sonnet** for simple, well-scoped, mechanical work (a self-contained module, a data table, a CSS pass); **Opus** for complex slices that need real design judgement, and for the **review/verify** pass. Don't pay for Opus on a slice Sonnet closes cleanly; don't hand a load-bearing slice to Sonnet.
- **Adversarial verify.** A phase isn't done when the builder says it's done. The orchestrator runs a separate **verification** step (its own `agent()`, typically Opus) whose job is to try to break the slice — build it, exercise it, look for the failure the builder would rationalize away — before anything is integrated.
- **Worktree isolation.** When subagents mutate files **in parallel**, each runs in its **own git worktree** so their edits can't collide on disk. A serial slice that touches files nobody else is touching doesn't need one; concurrent writers always do.
- **One serial integration point.** The orchestrator is the **single place** where finished slices come together and the **sole committer to the default branch**. Subagents build, verify, and commit **inside their worktree**, and they **do not push**. The orchestrator reviews each verified slice, integrates it into the default branch serially (one at a time), and is the only actor that writes history there.

## Workflow

1. **Plan the slices.** Break the work into **additive slices** — each a new module or a clean extension that layers over existing code rather than rewriting it — so two slices in flight rarely touch the same lines. Decide which are simple (Sonnet) and which are complex (Opus).
2. **Fix the data contract first.** Before any fan-out, nail down the **shared shape** every slice reads or writes — the interface, the object schema, the save/field layout, the function signatures at the seams. Write it down once; hand it to every subagent. Slices that agree on the contract up front integrate without renegotiation.
3. **Author the Workflow script.** Express the plan as phases of `agent()` / `pipeline()` / `parallel()` calls. Parallel slices that mutate files get their own worktree; sequential dependencies go in a `pipeline()`.
4. **Fan out and build.** Each subagent implements its slice **in its worktree**, on the right model tier, against the shared contract. It **builds and verifies its own work in-worktree and commits there** — it does not push and does not touch the default branch.
5. **Verify before integrate.** For each finished slice the orchestrator runs the **adversarial verify** step. A slice that fails goes back to its builder (or is re-sliced); only a slice that survives verification is eligible to integrate.
6. **Integrate serially.** The orchestrator pulls verified slices into the default branch **one at a time**, resolving any seam conflicts as the single integration authority, re-checking after each merge that the whole still builds and runs.
7. **Record status in the same commit.** Each integration commit **also updates the project `CLAUDE.md` status section** — the handoff/status doc moves forward in lockstep with the code, never in a separate follow-up. One integrated slice, one commit, status current.

## Rules that keep it clean

- **Additive over invasive.** Prefer a new file that plugs into an existing hook over an edit that rewrites a shared one. The fewer shared lines a slice touches, the less integration costs.
- **Contract before code.** No subagent starts until the shape it depends on is fixed. Changing the contract mid-run is an orchestrator decision, re-broadcast to everyone affected — not a thing a single slice does unilaterally.
- **Worktrees for concurrent writers.** Any two slices that could write the same file at the same time run in separate worktrees. Never let parallel subagents share a working tree.
- **Subagents don't push.** Build, verify, commit — in-worktree, full stop. The default branch has exactly one writer.
- **Verify is adversarial and separate.** The agent that verifies is not the agent that built (and is usually Opus). "It compiled" is not verification.
- **The orchestrator is the only integrator.** All merging into the default branch is serial and done by one role. This is the invariant that keeps the run race-free.
- **Right model for the slice.** Sonnet for mechanical, Opus for judgement and for review. Match the tier to the work, not to habit.
- **Status ships with the code.** `CLAUDE.md` is updated in the same commit as the integration it describes, so any agent (or the author) can resume cold.

## Status legend

Track each slice through these states — the orchestrator owns the board:

- **`planned`** — sliced and assigned a model tier; contract known; not yet started.
- **`building`** — a subagent is implementing it in its worktree.
- **`built`** — builder finished; committed in-worktree; awaiting verification.
- **`verifying`** — the adversarial verify step is exercising it.
- **`verified`** — survived verification; eligible to integrate.
- **`integrated`** — merged serially into the default branch by the orchestrator, with `CLAUDE.md` updated in the same commit.
- **`blocked`** — failed verify, or waiting on another slice / a contract decision; back to the orchestrator.

## Setting it up in a new project

1. **Pick the orchestrator.** One agent takes the planner/orchestrator/reviewer role for the whole run. It is the sole committer to the default branch.
2. **Write the data contract.** Draft the shared interfaces / schemas / signatures the slices will meet at, and put them somewhere every subagent reads.
3. **Slice additively and tier them.** Cut the work into layer-over slices; tag each Sonnet (simple) or Opus (complex). Keep shared-file overlap near zero.
4. **Author the Workflow script.** Encode the phases with `agent()` / `pipeline()` / `parallel()`. Give parallel file-mutating slices their own worktrees; chain dependent slices in a `pipeline()`. Add an Opus **verify** step per phase.
5. **Run, verify, integrate.** Fan out; let each subagent build+verify+commit in its worktree without pushing; run adversarial verify; integrate verified slices serially into the default branch.
6. **Keep `CLAUDE.md` in lockstep.** Update the project status doc in the same commit as each integration.

The one invariant that makes it safe: **exactly one agent writes the default branch, and it only writes verified work.**
