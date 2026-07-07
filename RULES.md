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

## Parallel / multi-part work — use an in-session agent team

- When a task is big enough to split, run it as an **in-session agent team**, not
  as separate sessions. **This session is the manager:** it works on the default
  branch, splits the work into non-overlapping slices, and spawns **one
  worktree-isolated subagent per slice** — the Agent tool with
  `isolation: "worktree"`, so parallel edits to the same file don't collide.
- **Integrate results yourself, one slice at a time**, re-running tests after each
  merge — the manager is the single serial integration point. Subagents build and
  commit in their own worktree; they don't push.
- Agree a shared **data contract** up front (data shapes, module fences,
  non-destructive config merges) so independently-built slices compose in one file.
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
- `PARALLEL_SESSIONS.md` — the in-session agent-team protocol (manager on the
  default branch + worktree-isolated subagents); include only if you split work
  across subagents. See the "Parallel / multi-part work" rule above.
