# Parallel Work — In-Session Agent Team

Use this when multi-part work should be split across workers. Delete this file if
you always work solo.

**One model:** a single **manager session** works on the default branch and runs
the workers as **worktree-isolated subagents it spawns** (the Agent tool,
`isolation: "worktree"`). One integrator, one filesystem, no cross-session
machinery. (This replaces the older "central dispatch across separate real
sessions" model — we don't use that anymore.)

## Model

- **Manager (this session):** works on the default branch (`master`/`main`)
  directly and is the **sole committer** to it. Owns the board (`TASKS.md`) and a
  shared **data contract**. Splits a phase into non-overlapping slices, **spawns
  one subagent per slice**, and integrates each result in a defined merge order,
  resolving conflicts.
- **Workers = subagents:** spawned via the Agent tool with
  **`isolation: "worktree"`**, so each builds on its own checkout cut from the
  latest default branch and parallel edits to the same hot file don't collide.
  Each gets one task, builds it, runs its test/smoke check, commits inside its
  worktree, and returns its branch/SHA + diff + how-to-verify. Subagents are
  **ephemeral** — no durable per-worker sessions, no per-worker files, no
  `coordination` branch.

## Workflow

1. **Split & contract.** Break the phase into non-overlapping slices; write a
   shared data contract in `TASKS.md` so the slices compose (shared data shapes,
   namespaces, non-destructive config merges, module fences).
2. **Fan out.** Spawn one worktree-isolated subagent per slice, each with its task
   spec and the contract. Independent slices run in parallel; a slice that others
   depend on is integrated first, then the rest fan out on the updated base.
3. **Build & verify.** Each subagent builds in its worktree, runs the headless
   test / smoke check, commits in-worktree (does not push), and reports.
4. **Integrate.** Manager merges each subagent's branch into the default branch in
   the stated merge order, runs the test suite after each, resolves conflicts, and
   updates `TASKS.md` + the project `CLAUDE.md` status in the same commit.

Serial integration into the default branch is the one intentional bottleneck —
the single clean integration point.

## Rules that keep it clean

- **The manager keeps the default branch current and is its only committer.**
  Each subagent's worktree is cut from the latest default branch, so workers
  always build on merged work.
- **One subagent = one slice = one branch.** Don't bundle unrelated work; build
  only the assigned slice, not the whole phase.
- **A shared data contract up front.** Agreed data shapes / namespaces / config
  keys are what let independently-built slices compose in one file at merge.
- **Prefer additive, self-contained modules** over editing shared hot files.
  Fence each slice's additions in clearly-marked blocks and add config via
  non-destructive merges — far fewer conflicts at integration.
- **Verify before integrating.** A slice lands only with its test/smoke check
  green; the manager re-runs the suite after each merge.

## Status legend (for `TASKS.md`)

`assigned` · `in progress` (subagent running) · `returned` (subagent done,
awaiting integration) · `merged` (integrated into the default branch) ·
`blocked` (see notes).

## Setting it up in a new project

1. In `TASKS.md`, paste this model as the header (so any session reads the same
   protocol), then the task board + the shared data contract.
2. In the project's root `CLAUDE.md`, add a one-line pointer: "Parallel work?
   Manager works on the default branch and spawns worktree-isolated subagents per
   slice — see `TASKS.md`." Keep that pointer consistent with the board.
