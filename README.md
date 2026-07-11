# claude-rules

Reusable Claude Code project-starter kit — the operating manual, the agent-teams
feature reference, a PROJECT.md example, and the settings that turn agent teams
on. Kept here (public) so any session, including **cloud / remote** ones that
can't see a local `~/.claude/`, can pull them from git with no auth.

## What's in here (and where each file goes in a real project)

| File in this repo | What it is | Copy it to |
|---|---|---|
| **`CLAUDE.md`** | The **operating manual** — the agent-team orchestrator guide (3 rules, audit protocol, readiness gate, roster bench) plus the general working rules. Project-agnostic. | the new project's **repo root**, as `CLAUDE.md` |
| **`AGENT_TEAMS.md`** | The Claude Code agent-teams **feature reference** — enabling, inter-agent messaging, the shared task list, limits. The *mechanics* to CLAUDE.md's *methodology*. | the new project's **repo root** (next to CLAUDE.md) |
| **`.claude/settings.json`** | Turns agent teams on for every session (`CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`). | the new project's **`.claude/`** |
| **`templates/PROJECT.md`** | An **example** of a generated `PROJECT.md` (real project). Reference only — see below. | — (don't copy verbatim) |

> **Why CLAUDE.md, not README, is the entry point Claude reads:** Claude Code
> **auto-loads a repo-root `CLAUDE.md`** as standing instructions at the start of
> every session. It does **not** auto-load `README.md` — README is for humans.
> So the operating manual must be `CLAUDE.md` at the project root.

## Start a new project

1. **Copy `CLAUDE.md`, `AGENT_TEAMS.md`, and `.claude/settings.json`** into the
   new repo (root, root, and `.claude/` respectively). No edits — they're generic.
2. **Author `GDD.md`** at the repo root — the design authority: goal, scope,
   architecture, staged roadmap with a testable exit criterion per phase. This is
   the only file that's truly unique to the project.
3. **`PROJECT.md` is generated, not copied.** On the first session the lead
   distills `GDD.md` into a team-facing `PROJECT.md` (goal, stack, constraints,
   module→ownership map, roster, milestone exit criteria) — **once**. After that
   it's the source of truth and isn't regenerated unless you ask. Model it on
   [`templates/PROJECT.md`](templates/PROJECT.md).

So a live project's root ends up with: `CLAUDE.md`, `AGENT_TEAMS.md`, `GDD.md`,
`PROJECT.md`, and `.claude/settings.json`.

## The habit that ties it together

**Keep the project's status current — update it in the same commit as every
completed task** (in `PROJECT.md` or a status doc it points to). That one habit
lets any session — a different Claude instance, a cloud agent, or you weeks later
— resume cold without re-deriving context.
