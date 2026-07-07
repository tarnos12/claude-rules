# Parallel Sessions — Coordination Protocol (Central Dispatch)

Use this when **several Claude Code sessions work the same repo at once** (e.g.
a few cloud agents plus a local one). It stops two sessions from building the
same thing, stomping each other's files, or blocking on a shared file. Delete
this file if you always work solo.

## The idea

- **`master` (your default branch) stays code-only** — the meeting point for
  merged, working code.
- **A long-lived `coordination` branch holds the coordination files** (the task
  board + per-session assignment files). It's *never merged into `master`*, so
  board churn never touches code history.
- **One session manages; the rest do the work.** Tasks are **assigned by a
  single manager**, not self-claimed.

## Why not self-claiming?

The obvious design — every session edits a shared `TASKS.md` to claim a task —
causes a **push-race**: two sessions edit the same file, one push is rejected,
and sessions block each other on that one file. Central dispatch removes the
race entirely with one rule:

> **Each session writes exactly ONE file.** No two sessions ever write the same
> file, so their pushes to `coordination` never collide.

## Roles

| Session | Role |
|---|---|
| **#1 — Manager** | **Works on the default branch (`master`/`main`) directly — not a feature branch.** Owns the board (`TASKS.md`). Assigns tasks, picks branch names, **merges PRs one at a time** (rebase + resolve conflicts), keeps the board current. Also writes each worker's assignment. |
| **#2, #3, … — Workers** | Each reads/writes **only its own `TASK_<n>.md`**. Builds the assigned task on the branch #1 named, opens a PR, waits for the next assignment. |

## File layout (on the `coordination` branch)

- `TASKS.md` — the catalog + live status of every task, and who's assigned.
  **Only #1 writes it.**
- `TASK_2.md`, `TASK_3.md`, … — one per worker. Holds that worker's current
  assignment (task, branch name, owned/shared files, notes) **written by #1**,
  and the worker's own status/log **written by that worker**. Everyone may
  *read* any file; each writes only the ones above.

## Worker loop (#2/#3/…)

0. **Always pull the default branch before accepting a new task.**
   `git fetch origin && git checkout master && git pull origin master` (use
   `main` if that's the default). Cut your task branch from this fresh base. This
   is non-negotiable: it's how you pick up newly-pushed coordination files and
   guarantees you build on merged work, not a stale checkout. A session that
   skips this can't see its `TASK_<n>.md` and ends up guessing its task.
1. `git fetch origin coordination && git checkout -B coordination origin/coordination`
2. Open **your own `TASK_<n>.md`** — it has your assignment: the task, the
   branch name to use, your owned vs. shared files, and any notes from #1.
3. Build on that branch, off `master`. Commit, push, open a PR into `master`.
4. Set the **Status** line at the top of *your* `TASK_<n>.md` to
   `IN REVIEW — PR #NN`, add a one-line note in your log, and push to
   `coordination`. That's your signal to #1.
5. Wait for your next assignment in the same file. **Never edit `TASKS.md` or
   another worker's file.**

## Manager loop (#1)

0. **Work on the default branch directly** (`master`/`main`). The manager does
   not develop on its own feature branch — it maintains the board and integrates
   every worker PR here, and it is the single point allowed to commit to the
   default branch. (Workers never push to the default branch; they PR into it.)
1. Maintain `TASKS.md` (the pool + status). Assign an `AVAILABLE` task by
   writing the worker's `TASK_<n>.md` assignment section and a branch name;
   flip the board row to `ASSIGNED`.
2. When a worker's status flips to `IN REVIEW`, review and **merge its PR
   (serially — one at a time)**, rebasing/resolving as needed.
3. Flip the board row to `DONE` and drop the worker's next task into their file.

Merging into `master` is the only serial bottleneck, and that's by design (one
clean integration point).

## Messaging between sessions (optional, still one-writer)

Give each `TASK_<n>.md` two mail sections so questions don't need a shared file:

- **`## Inbox from #1`** (top) — **only #1 writes.** Answers/directives to that
  worker. Workers read it every sync.
- **`## Outbox`** (bottom) — **only that worker writes.** Post a question tagged
  `#from→#to · OPEN`.

Routing is a **star through #1**: to ask another worker, address it in your
Outbox; #1 relays it into the other worker's Inbox and relays the answer back.
It's **pull, not push** — a session sees new mail on its next
`git fetch origin coordination`. For a truly blocking question to an idle
session, the manager can wake it out-of-band; reserve that for blockers.

## Status legend (for `TASKS.md`)

`AVAILABLE` (in pool) · `ASSIGNED` (dispatched to a worker) · `IN REVIEW`
(PR open, awaiting #1's merge) · `DONE` (merged) · `BLOCKED` (see notes).

## Setting it up in a new project

1. Create the long-lived `coordination` branch off `master`.
2. On it, add `TASKS.md` whose **header states this model** (paste the sections
   above, adapted) so every session reads the same protocol — then the task
   catalog. This header is the **live, operational source of truth**; this
   template is just the generic starting point, so keep the board header current
   and let it win if they ever differ.
3. Add one `TASK_<n>.md` per worker session.
4. In the project's root `CLAUDE.md`, add a one-line pointer: "Working in
   parallel? `git fetch origin coordination` and read the `TASKS.md` header —
   Central Dispatch, don't self-claim." Keeping that pointer consistent with the
   board is what prevents sessions getting contradictory instructions.

## Rules that keep it clean

- **Pull the default branch before every new task.** Fetch and start from the
  latest `master`/`main` before accepting an assignment or cutting a task
  branch — never build on a stale base, and it's how you see freshly-pushed
  coordination files.
- **One session writes one file** — the invariant that makes all of this
  race-free. Never write another session's file.
- **One task = one branch = one PR.** Don't bundle unrelated work.
- **Prefer additive, self-contained modules** over editing shared hot files (a
  central UI/state file). A new feature in a new file that plugs into an
  existing extension point → far fewer merge conflicts across sessions.
- **Sync often** — `git fetch origin coordination` before starting and before
  pushing; keep each change small and mergeable.
