# Working rules for Claude Code (Mariusz)

My standing preferences for how Claude Code should work on any of my projects.
Read this at the start of a session and follow it. A project's own `CLAUDE.md`
overrides or extends these when they conflict.

## Git & committing

- **Commit after every completed task** — one task = one focused, well-described
  commit. Frequent commits, not one big pile at the end.
- **Push after committing** when the repo has a remote.
- If on the default branch and the change is substantial, branch first.
- **Sync before starting** — `git fetch`, check the main branch; other
  sessions/machines may share the repo.

## Session continuity docs

- For multi-session work, maintain a **handoff doc** with a "next session /
  start here" pointer and a "last session summary". A repo-root `CLAUDE.md`
  works well as this doc since Claude Code auto-loads it; otherwise `HANDOFF.md`.
  Create it if it doesn't exist.
- **Update the handoff doc in the SAME commit as the code change** so the next
  session (local or cloud) knows the current state.
- Keep any design/plan doc current — mark items done, add new plans as scope grows.

## Parallel / multi-part work — orchestrate a dynamic Workflow

- When a task is big enough to split, run it as a **dynamic Workflow** that this
  session orchestrates. **This session is the planner, orchestrator, and
  reviewer:** it works on the default branch, plans the work into non-overlapping
  additive slices, and adapts the plan as results come back — it does **not** build
  the slices itself.
- **Delegate all building to subagents**, choosing each subagent's model by the
  slice's complexity: **Sonnet** for simple, mechanical, or well-specified slices;
  **Opus** for complex, nuanced, or canonical slices — and for the **verify and
  review stages**.
- Each subagent **builds, verifies, and commits in its own worktree** (the Agent
  tool with `isolation: "worktree"`, so parallel edits to the same file don't
  collide); subagents **never push**.
- **Integrate results yourself, one slice at a time**, re-running tests after each
  merge — the orchestrator is the **sole serial integrator and committer**.
- Additive slices, a shared **data contract** agreed up front (data shapes, module
  fences, non-destructive config merges), and **verify-before-integrate** still
  hold, so independently-built slices compose in one file.
- Full protocol + per-project setup: `templates/PARALLEL_SESSIONS.md`.

## Running & verifying

- After a change, tell me **how to see it running** (dev server URL / command /
  preview link) and include it in the reply.
- **Verify before claiming done** — actually run the code / preview and check
  behavior; report failures honestly with real output.
- Prefer the **fastest feedback loop that proves it** — exercise pure/core
  logic headless (a script or unit test) rather than always driving the full app.
- If the project uses cache-busting version tags (e.g. `?v=N`), bump them on any
  code change.

## Style

- Direct, outcome first. Loop me in on load-bearing findings and direction
  changes; don't narrate every step.
- For multi-part tasks, do the whole thing rather than stopping to ask
  permission for each reversible step.

## Starting a new project

Copy the drop-in templates from [`templates/`](templates/) into the new
project's root and fill in the `<…>` placeholders:

- `CLAUDE.md` — per-project handoff/status doc (Claude Code auto-loads it).
- `DESIGN.md` — the staged-roadmap design doc (Prototype → MVP → Demo → 1.0).
- `PARALLEL_SESSIONS.md` — the Workflow-orchestration protocol (orchestrator on
  the default branch + worktree-isolated build/verify subagents); include only if
  you split work across subagents. See the "Parallel / multi-part work" rule above.
