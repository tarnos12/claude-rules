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

- For multi-session work, maintain a **handoff doc** (`HANDOFF.md`) with a
  "next session / start here" pointer and a "last session summary". Create it
  if it doesn't exist.
- **Update the handoff doc in the SAME commit as the code change** so the next
  session (local or cloud) knows the current state.
- Keep any design/plan doc current — mark items done, add new plans as scope grows.

## Running & verifying

- After a change, tell me **how to see it running** (dev server URL / command /
  preview link) and include it in the reply.
- **Verify before claiming done** — actually run the code / preview and check
  behavior; report failures honestly with real output.
- If the project uses cache-busting version tags (e.g. `?v=N`), bump them on any
  code change.

## Style

- Direct, outcome first. Loop me in on load-bearing findings and direction
  changes; don't narrate every step.
- For multi-part tasks, do the whole thing rather than stopping to ask
  permission for each reversible step.
