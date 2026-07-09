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
- **Delegate all building to subagents** — the orchestrator (the priciest model)
  never builds, even simple things. Pick each subagent's tier by
  complexity-per-token: **Haiku** for trivial/mechanical edits (doc lines,
  catalog entries, renames), **Sonnet** for simple, well-specified slices,
  **Opus** for complex, nuanced, or canonical slices — and for the **verify and
  review stages**. Batch tiny edits together so per-agent overhead doesn't
  exceed the work itself.
- **After each completed work item, report the model/token usage** — which
  model built/verified what and how many tokens each stage consumed — so model
  routing can keep being tuned.
- Each subagent **builds, verifies, and commits in its own worktree** (the Agent
  tool with `isolation: "worktree"`, so parallel edits to the same file don't
  collide); subagents **never push**.
- **Integrate results yourself, one slice at a time**, re-running tests after each
  merge — the orchestrator is the **sole serial integrator**. Land **every
  completed task as a pull request** into the default branch: open the PR when
  the task is done, merge it once tested and working (the orchestrator opens and
  merges its own PRs; nothing merges untested).
- Additive slices, a shared **data contract** agreed up front (data shapes, module
  fences, non-destructive config merges), and **verify-before-integrate** still
  hold, so independently-built slices compose in one file.
- Full protocol + per-project setup: `templates/PARALLEL_SESSIONS.md`.

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

## Model usage & reporting

- **Route work to the cheapest model that can do it well.** The top-tier main
  loop (Fable/Opus) is for orchestration, design decisions, risky/coupled
  refactors, integration + merges, and final review — not for simple tasks.
  Delegate via subagents/workflows with an explicit `model`:
  - **Haiku** — mechanical edits (comment/dead-code removal, renames, doc
    stubs), greps/audits, running verification scripts.
  - **Sonnet** — well-specified implementation slices, tests, display-only
    changes, standard bugfixes with a tight spec.
  - **Opus** — complex implementation slices, adversarial code review,
    plan/spec validation.
  - **Fable (or session top model)** — the manager only: splitting work,
    writing contracts/specs, integrating, judging, and anything genuinely hard
    or cross-cutting.
- **After every completed task/batch, report model usage:** a short table of
  which model did what (main loop + each subagent/workflow phase) and the
  subagent token counts (from the harness usage data), plus a one-line note on
  anything that ran on a bigger model than it needed and how to route it
  cheaper next time.

## Starting a new project

Copy the drop-in templates from [`templates/`](templates/) into the new
project's root and fill in the `<…>` placeholders:

- `CLAUDE.md` — per-project handoff/status doc (Claude Code auto-loads it).
- `DESIGN.md` — the staged-roadmap design doc (Prototype → MVP → Demo → 1.0).
- `PARALLEL_SESSIONS.md` — the Workflow-orchestration protocol (orchestrator on
  the default branch + worktree-isolated build/verify subagents); include only if
  you split work across subagents. See the "Parallel / multi-part work" rule above.
