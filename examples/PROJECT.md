# PROJECT.md — Project-Specific Instructions

Project detail for the agent team. **For team-running rules (the 3 rules, audit protocol, readiness
gate, hygiene), see `CLAUDE.md`.** This file holds *what* is built; `CLAUDE.md` holds *how* the team
operates. Keep the split clean.

---

## Goal

A browser-based 2D side-view **colony sim** in the spirit of *Craft the World*. The player is an
unseen overseer who never controls a dwarf directly — they issue orders (dig, build, craft) and 20+
autonomous dwarves path, prioritize, and execute across a fully destructible, procedurally generated
**512×192** world. Core loop: **dig → gather → craft → build → defend → expand**.

**No win condition.** A sandbox anchored by three systems: a large visible tech tree (~150–180
recipes) as the soft goal, deep-layer content holding the rarest materials, and endlessly escalating
monster waves that keep pressure on forever.

**Design authority:** the full GDD ([GDD.md](GDD.md)) is the source of truth; this file summarizes
it for the team.

## Stack & structure

- HTML Canvas 2D + vanilla ES modules. **No build tooling, no dependencies. Refresh-to-run.**
- Module layout is the ownership map:
  `/src` → `main.js`, `config.js`, `world/`, `entities/`, `systems/`, `render/`, `ui/`, `save/`.

## Hard constraints (enforce on every teammate)

- `config.js` is the **only** home for balance constants. No magic numbers in systems code.
- Simulation is **deterministic**: consumes only (previous state, inputs, seed). No `Math.random`,
  no wall-clock reads inside sim code. (This also keeps future multiplayer possible.)
- Module boundaries are file-ownership boundaries — no two teammates edit the same file.

---

## Instantiated roster (map `CLAUDE.md` archetypes → this project)

Spawn only the 3–5 the current phase needs. *(subagent)* roles are spawned by the relevant teammate.

| Role | Model | Owns / does |
|---|---|---|
| **Lead / Integrator & Architect** | Opus | Module interfaces, `main.js` game loop, sequencing, integration passes |
| **World & Terrain** | Sonnet | `world/`: worldgen, tiles/chunks, water sim, lighting; publishes map API |
| **Entities & AI** | Opus | `entities/`: dwarves, monsters, items, utility task selection, budgeted A* |
| **Systems** | Sonnet | `systems/`: task/priority scheduling, crafting/tech tree, stockpile, needs, waves, spells |
| **Render** | Sonnet | `render/`: chunked terrain canvases, layer compositing, camera/zoom, atlas |
| **UI** | Sonnet | `ui/`: tech-tree panel, HUD, priority/jobs board, order painting, rally flag, god hand |
| **Design & Balance** | Fable | **Sole writer of `config.js`**: balance tables, curves, caps; playtests for feel |
| **Test Author** | Sonnet | Deterministic sim harnesses (seed → expected state after N ticks), unit + regression tests |
| **QA / Verification** | Opus | Runs suite, hunts deadlocks/stranded dwarves, verifies fixes, gates phase exits |
| **Save & Persistence** *(subagent)* | Sonnet | `save/`: versioned schema + migration, seed + tile-diff serialization, export/import |
| **Asset Pipeline** *(subagent)* | Haiku | Placeholder CC pixel-art, sprite-geometry mapping doc + license credits |

**Shared-file owner:** only **Design & Balance** writes `config.js`; everyone else requests a
constant change.

---

## Milestone exit criteria (QA gates each before it's "done")

- **Phase 1 — Playable dirt-box:** mark a region → one dwarf excavates with budgeted A* and hauls to
  a chest, on the full 512×192 world at target framerate. Module skeleton + `config.js` established.
- **Phase 2 — Crafting & building:** full loop wood/stone → tools → a walled, lit, door-sealed
  shelter built entirely via orders; tech-tree panel + ~40 tier-1/2 nodes.
- **Phase 3 — Survival:** needs, day/night + lighting, water sim, ambient spawns, dwarf
  auto-combat — undefended colony gets hurt at night, a walled+lit one survives, water fills a moat.
- **Phase 4 — Waves & scale:** a 16+ dwarf colony survives wave 8 on default balance using built
  defenses, holding 60fps/20tps; jobs board, farming at scale, god hand + mana + 3 spells.
- **Phase 5 — Deep band & completion:** tier-4 deep content, tech tree 100% completable with
  celebration, late wave compositions, save/export polish, tutorialized first 10 minutes, full
  balance pass — genuine escalating pressure past hour 20 with no dead-end states.
