# <Project Name> — Design & Staged Roadmap

**Status:** Draft
**Author:** <name>

> This is the source of truth for scope. Keep it current: mark items done, add
> new plans as scope evolves. The per-session status/handoff lives in
> `CLAUDE.md`; this file is the bigger picture.

---

## 1. Vision & pillars

<One paragraph on what this is and why it's worth building. Then 2–4 design
pillars — the non-negotiables every decision is checked against.>

1. **<Pillar>** — <what it means in practice>.
2. **<Pillar>** — <…>.

---

## 2. Core loop / primary flow

<Describe the smallest complete experience — the thing that must be good on its
own before any other system is layered on. Everything else exists to give this
loop texture, not to replace it.>

---

## 3. Architecture notes (build once, use in every stage)

Decisions to get right early because they're expensive to retrofit:

1. **A pure, deterministic core function** for the central logic — takes
   inputs, returns a result, touches no I/O/UI/global state. This is what makes
   testing, tuning, and future reuse cheap.
2. **One canonical data model** shared by every variant that uses it, from day
   one — avoids a rewrite when a new variant appears.
3. **Other-party / external systems behind an interface**, not hardcoded to the
   current (possibly stub) implementation. A real implementation then swaps in
   without the rest of the code knowing.
4. **Persistence is the account.** Versioned local save with a migration path;
   structure it so it could later sync to a server without a parallel model.
5. **Modular files, not a monolith** — split by responsibility from the start.
6. **Decide time/statefulness early** (e.g. wall-clock vs session-only) — it's
   core to feel and cheap if designed in, painful if bolted on.

---

## 4. Staged roadmap

Each stage has a **goal**, what's **in / out of scope**, and an **exit
criterion** you can actually test. Don't start the next stage until the current
one's exit criterion is met.

### Stage 0 — Prototype
- **Goal:** prove the core loop is fun/viable with zero polish.
- **In:** the core loop end-to-end, one tiny slice of content, no persistence.
- **Out:** saving, balance, content breadth, art, polish.
- **Exit:** the loop is engaging at zero polish. *If it's boring here, fix the
  loop before adding anything.*

### Stage 1 — MVP
- **Goal:** the systems that make it a real thing, not a tech demo.
- **In:** persistence, the 2–3 systems the loop needs, the modular structure,
  the shared data model.
- **Out:** the "other-party" systems, breadth, art pass.
- **Exit:** a new user can go from start to a meaningful midpoint purely through
  use, saving/loading correctly.

### Stage 2 — Demo
- **Goal:** a polished, shareable slice.
- **In:** a wider slice of content with a difficulty/complexity curve, the
  stubbed "other-party" systems introduced behind their interfaces, an
  onboarding pass, a visual/UX polish pass.
- **Out:** full breadth, long-horizon balancing.
- **Exit:** a stranger can use it with zero explanation and understand what the
  full thing will be. Get external feedback here.

### Stage 3 — 1.0
- **Goal:** complete, shippable.
- **In:** full content breadth, balancing pass, packaging/store presence,
  full save export/import.
- **Out:** anything requiring a live backend.
- **Exit:** feature-complete, balanced, no known blocking issues.

### Stage 4 — Next (optional)
- **Goal:** swap the stubs for real networked/backed implementations using the
  interfaces built from Stage 0 on. Cheap *because* earlier stages coded against
  interfaces, not hardcoded stubs.

---

## 5. Open questions to resolve before Stage 0

<Resolve these first — they shape everything. Record the decision inline once
made, with the date.>

- <title/setting/tone, or the equivalent framing decision>
- <target scope / size for 1.0>
- <the time/statefulness decision from §3.6>
- <look & feel / tech direction>
