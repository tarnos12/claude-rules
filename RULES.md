# Working rules for Claude Code (Mariusz)

My standing preferences for how Claude Code should work on any of my projects.
Read this at the start of a session and follow it. A project's own `CLAUDE.md`
overrides or extends these when they conflict.

## Git & committing

- **Commit after every completed task** — one task = one focused, well-described
  commit. Frequent commits, not one big pile at the end.
- **Push after committing** when the repo has a remote.
- **Land each completed task as a PR** — open a pull request when the task is
  done and merge it once tested and working, rather than committing to the
  default branch directly.
- If on the default branch and the change is substantial, branch first.
- **Sync before starting** — `git fetch`, check the main branch; other
  sessions/machines may share the repo.

## Session continuity docs

- For multi-session work, keep the current state where it belongs: the project's
  **GDD.md** (or a dedicated status doc it points to), with a "next session /
  start here" pointer and a "last session summary". The generic `CLAUDE.md` is
  **not** the place for project status — it stays project-agnostic.
- **Update that status in the SAME commit as the code change** so the next
  session (local or cloud) knows the current state.
- Keep the design/roadmap current — mark items done, add new plans as scope grows.

## Parallel / multi-part work — run an agent team

- When a task is big enough to split, run it as an **agent team**. Teammates are
  separate, full Claude Code sessions with their own context that **message each
  other directly** — including QA↔dev — and self-coordinate through a shared task
  list, not only through the lead. (Use a plain subagent instead when only a
  result matters with no cross-talk; use a single session for sequential,
  same-file, or trivial work.)
- **This session is the lead: integrator + gatekeeper**, not a build drone and
  not a message bus. The lead freezes the **interface contracts** up front, owns
  the integration/wiring file and the merges, routes cross-team findings,
  adjudicates disagreements, and holds **phase go/no-go**. A QA/verification
  teammate signs off against the exit criteria before anything is "done".
- **File ownership = file boundaries.** No two teammates edit the same file; the
  only shared surfaces are read-only interfaces and the single config location
  (one owner). Raise interface changes with the lead before touching a shared seam.
- **Give each teammate an explicit model** (they don't inherit the lead's), sized
  to the work: cheaper models for mechanical or tightly-specified slices, the
  stronger models for the hardest build, adversarial review, and integration.
  Name teammates by role (e.g. `[Role]-[Model]-[Task]`) so they stay addressable.
- **Verify before integrate.** The lead reviews each teammate's files, runs the
  tests, and commits per verified slice; land completed work as PRs into the
  default branch, merged once tested. Nothing merges untested.
- Enable teams once per environment (`CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` at
  session start). Keep a per-project agent-teams reference doc (e.g.
  `docs/AGENT_TEAMS.md`) for the full operating detail.

## Running & verifying

- After a change, tell me **how to see it running** — and ALWAYS include a
  link: the project's **GitHub Pages URL** if it deploys there (deploy first so
  the link reflects the change), otherwise publish an **Artifact** and link
  that. A dev-server URL alone is not enough (remote sessions' localhost is
  unreachable to me).
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

## Model selection

- **Route work to the cheapest model that can do it well.** The session's top
  model is for orchestration, design decisions, risky/coupled refactors,
  integration + merges, and final/adversarial review — not for simple tasks.
  Delegate the rest to teammates/subagents with an explicit `model`: cheap tiers
  for mechanical or tightly-specified slices, mid tiers for well-specified
  implementation and tests, top tiers for complex/nuanced slices and review.

## Starting a new project

Fill in only what's unique to the project — everything else is generic:

- **`CLAUDE.md`** — copy the generic one in as-is (no per-project edits): it
  carries no project-specific info and just points to this rules doc + the
  project's GDD. Claude Code auto-loads it.
- **`GDD.md`** (repo root or `docs/`) — the staged-roadmap design doc
  (Prototype → MVP → Demo → 1.0) and the single home for everything unique to
  the project (design, scope, architecture, how to run it, current status). This
  is the one file you author per project.
