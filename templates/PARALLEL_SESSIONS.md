# Parallel Sessions — Coordination Protocol

Use this when **multiple Claude Code sessions may work on the repo at once**
(e.g. several cloud agents plus a local one). It stops two sessions from
building the same thing or stomping each other's files. Delete this file if you
only ever work solo.

## The idea

- **`master` (or your default branch) stays code-only** — the meeting point for
  merged, working code.
- **A long-lived `coordination` branch holds a shared task board** —
  `TASKS.md` — that every session reads and writes. It's intentionally *not* on
  `master`, so board churn never conflicts with code.
- Each task is built on **its own feature branch** and merged via PR.

## Before writing any code

1. `git fetch origin coordination` and read `TASKS.md`.
2. Pick a task marked `AVAILABLE`.
3. **Claim it** (see protocol below). The claim is an atomic push race — first
   push wins. If your push is rejected, someone beat you to it; pull and pick
   another.
4. Create a feature branch `claude/<short-task-slug>` and build there.
5. Open a PR into `master` when done. Keep the PR focused on one task.

## Claim protocol (atomic, first-push-wins)

```
git fetch origin coordination
git checkout coordination && git pull
# edit TASKS.md: change your task's status AVAILABLE -> CLAIMED (<your id>, <date>)
git commit -am "Claim: <task>"
git push origin coordination      # if rejected, someone else won — pull & retry with another task
```

## TASKS.md format (suggested)

```
## AVAILABLE
- [ ] <task-id> — <one-line description> — files: <likely files/areas>

## CLAIMED
- [~] <task-id> — <desc> — <session id>, <date>

## DONE
- [x] <task-id> — <desc> — merged in <PR/commit>
```

## Rules that keep it clean

- **One task = one branch = one PR.** Don't bundle unrelated work.
- **Prefer additive, self-contained modules** over editing shared hot files
  (e.g. a central UI or state file). A new feature in a new file that plugs into
  an existing extension point → far fewer merge conflicts.
- **Leave handoff notes in `TASKS.md`** for other sessions (blockers,
  decisions, "don't touch X, I'm mid-refactor").
- **Sync often** — `git fetch` before starting and before pushing.
- Keep each session's change **small and mergeable**; long-running branches
  drift and conflict.
