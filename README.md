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

The flow — the only thing you author per project is the **GDD**:

1. **Copy `templates/CLAUDE.md`** into the repo as-is (no edits). It's the
   project-agnostic **agent-team orchestrator guide** — how the lead runs the
   team (the 3 rules, audit protocol, readiness gate, roster bench). Claude Code
   auto-loads it.
2. **Author `GDD.md`** (from `templates/GDD.md`) — the staged-roadmap design doc
   and the single home for everything unique to the project.
3. **`PROJECT.md` is generated, not copied.** On the first session, the lead
   distills `GDD.md` into a team-facing `PROJECT.md` (goal, stack, constraints,
   module→ownership map, roster, milestone exit criteria) — **once**. Once it
   exists it's the source of truth and isn't regenerated unless you ask. See
   [`examples/PROJECT.md`](examples/PROJECT.md) for a real filled-in example.

| Path | What it is | Per-project action |
|---|---|---|
| `templates/CLAUDE.md` | Generic agent-team orchestrator guide | **Copy as-is** |
| `templates/GDD.md` | Staged-roadmap design-doc skeleton | **Author** it |
| `examples/PROJECT.md` | Example of a generated `PROJECT.md` (real project) | **Reference only** — the lead generates yours from `GDD.md` |

## Files

- `RULES.md` — the standing working rules Claude should follow (the source of
  truth; the raw URL above points here).
- `templates/` — drop-in starter files for a new project.
- `examples/` — real filled-in examples to model generated files on.

## The habit that ties it together

**Keep the project's `GDD.md` current — update its status in the same commit as
every completed task.** That one habit lets any session — a different Claude
instance, a cloud agent, or me weeks later — resume cold without re-deriving
context.
