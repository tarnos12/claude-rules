# claude-rules

My working rules for Claude Code **and** the reusable project-starter templates
that go with them — kept here (public) so any session, including **cloud /
remote sessions** that can't see my local `~/.claude/CLAUDE.md`, can load them
from git with no auth.

## Use the rules in a session

Paste this at the start of a session:

> Read and follow my working rules from
> https://raw.githubusercontent.com/tarnos12/claude-rules/master/RULES.md and
> apply them for the rest of this session.

Claude fetches that URL (public, no auth) and follows it.

## Start a new project

Copy the drop-in files from [`templates/`](templates/) into the new project's
root and fill in the `<…>` placeholders. Commit them first, before writing code,
so the rules are in place from commit #1.

| Template | What it is |
|---|---|
| `templates/CLAUDE.md` | Per-project **handoff/status doc**. Claude Code auto-loads it, so it's the single source of truth for "where are we, what's next, hold these conventions, strip these testing hacks." Update its status section in the same commit as every task. |
| `templates/DESIGN.md` | The **staged-roadmap** design doc (Prototype → MVP → Demo → 1.0), with a testable exit criterion per stage and "resolve the open questions before Stage 0." |
| `templates/PARALLEL_SESSIONS.md` | **Parallel-work coordination** — the *dynamic Workflow-orchestration* model: a planner/orchestrator/reviewer authors a Workflow that delegates slices to Sonnet or Opus subagents by task complexity, integrating each result serially. One integrator, no cross-session machinery. Include only if you split work across subagents. |

## Files

- `RULES.md` — the standing working rules Claude should follow (the source of
  truth; the raw URL above points here).
- `templates/` — drop-in starter files for a new project.

## The habit that ties it together

**Keep the project's `CLAUDE.md` "Current status" section current in the same
commit as every completed task.** That one habit lets any session — a different
Claude instance, a cloud agent, or me weeks later — resume cold without
re-deriving context.
