# Working Notes for Claude Code

Generic, portable operating manual — **copy this file into any repo as-is.** It
contains **no project-specific information**: everything unique to a project
lives in that project's **GDD.md**. If something here would only be true for one
project, it belongs in the GDD, not here.

## Read first, every session

1. **Canonical working rules:**
   <https://raw.githubusercontent.com/tarnos12/claude-rules/master/RULES.md> —
   fetch and follow it. It is the source of truth for how we work (including how
   we run parallel work as **agent teams**).
2. **This project's everything — design, scope, architecture, the staged phase
   roadmap with exit criteria, how to run it, and current status — is in
   [GDD.md](GDD.md)** (repo root; some projects keep it at `docs/GDD.md`). Read
   it before starting. The GDD is the only place project-specific facts live.

## Standing rules (apply to every project)

- **Verify before claiming done.** Exercise the change yourself and run the
  project's test suite(s); don't claim a result you haven't observed. After a
  change, tell the author how to see it running (URL / command).
- **One commit per completed task,** and update the project's current-status
  notes in that same commit, so any session (or the author) can resume cold.
- **Versioning.** Every author-visible change bumps the project's version and
  changelog — the codebase holds the single source of truth for both, and the
  version surfaced to the author must match it. Announce/republish the build per
  the project's process whenever the version changes.
- **Balance/tuning constants live in one config location** — never scatter magic
  numbers through the code.
- **Keep the simulation deterministic** wherever a project has one: a single
  seedable PRNG; no `Math.random` and no wall-clock reads inside sim code, so a
  given (state, inputs, seed) always reproduces.
- **Prefer additive, self-contained modules** over editing shared hot files.
  When running an agent team, **module/file boundaries are ownership
  boundaries** — no two teammates edit the same file.
- **Don't add build tooling or dependencies** unless the project already uses
  them; keep whatever "just run it" story the project has intact.
- Commit trailer: `Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>`.
