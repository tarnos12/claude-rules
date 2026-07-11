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

Copy the drop-in files from [`templates/`](templates/) into the new project.
The **only** file you author per project is the GDD — everything else is generic.

| Template | What it is |
|---|---|
| `templates/CLAUDE.md` | **Generic, portable** operating manual — copy it in as-is, no edits. It carries no project-specific info; it just points to these rules + the project's GDD. Claude Code auto-loads it. |
| `templates/GDD.md` | The **staged-roadmap** design doc (Prototype → MVP → Demo → 1.0), with a testable exit criterion per stage. The single home for everything unique to the project — design, scope, architecture, how to run it, and current status. |

## Files

- `RULES.md` — the standing working rules Claude should follow (the source of
  truth; the raw URL above points here).
- `templates/` — drop-in starter files for a new project.

## The habit that ties it together

**Keep the project's `GDD.md` current — update its status in the same commit as
every completed task.** That one habit lets any session — a different Claude
instance, a cloud agent, or me weeks later — resume cold without re-deriving
context.
