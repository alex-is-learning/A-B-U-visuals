# ABU 3D Visual — Handover

**Date:** 2026-03-31
**Status:** All nine GIFs complete and approved. Ready to move into website/book phase.

---

## File inventory

| File | What it shows | Status |
|------|--------------|--------|
| `A-frontier.html` | GIF 1 — base graph builds, frontier A nodes spawn outward in a chain | Complete |
| `A-to-B.html` | GIF 2 — cyan laser fires from offscreen, strikes an A node, it matures into a B and the network reorganises | Complete |
| `U-book-and-person.html` | GIF 3 combined — both U1 (book) approaches and snaps in, U2 (person) wobbles and fades | Complete |
| `U-to-A.html` | GIF 3a — book U only: approach → snap (purple→red, joins graph) | Complete |
| `U-rejected.html` | GIF 3b — person U only: approach → wobble/crackle → fade out | Complete |
| `mal1-maladaptive-b-fires.html` | mal1 — B0 fires fire-red/orange pulse at external person; person burns, smokes, sphere turns red | Complete |
| `mal2-feedback-rejected.html` | mal2 — smouldering red person (mal1 end-state) sends charged purple signal at B cluster; rejected; person smokes throughout | Complete |
| `mal3-feedback-integrated-equal.html` | mal3 — smoking person sends purple packet to outer A frontier; snaps red (A), moves closer still red, turns green (B), equal binary orbit with B0 | Complete |
| `mal4-feedback-integrated-dominant.html` | mal4 — same as mal3 but newB grows to 2× dominant; B0 shrinks to 0.5× moon | Complete |

No build step. All files are standalone HTML, CDN-only (Three.js 0.160.0, gif.js 0.2.0).

---

## Git / remote

- Local git repo initialised at this folder root.
- Remote: `https://github.com/alex-is-learning/A-B-U-visuals` (branch `main`).
- Changes from this session are NOT yet committed/pushed. Run `/commit` then `git push`.

---

## Architecture (applies to all files)

- **Spring layout:** custom, no d3-force-3d. KR=6000, KS=0.04, KCB=0.06, KCA=0.006, DAMP=0.85. 300 settle ticks on init, 20–60 ticks per animation event.
- **Node types:** B (green, radius 6, centre pull KCB), A (red, radius 4, centre pull KCA). B cluster sits centrally; A nodes form a radiating ring.
- **Frustum clamp:** iterative binary search (24 iterations), camera-position-agnostic. Applied to all nodes after every spring update.
- **Edge rendering:** pre-allocated `Float32Array(21×3)` per edge, updated in-place via `writeEdgeBuffer`. Curved bezier lines. No geometry disposal during animation.
- **Scene rotation:** `scene.rotation.y = t * 0.00015` (Y pan), `scene.rotation.x = sin(t * 0.0003) * 0.10` (±5.7° X sway). Identical across all files.
- **GIF export:** rebuilds base graph from scratch, replays full animation with `captureFrame` as `onFrame` callback. Blob URL worker (CORS bypass). 90s safety timeout.
- **Canvas:** 800×600, camera at z=360, FOV 60°. Frustum at z=0 ≈ ±277 × ±208 world units.

---

## mal3 / mal4 colour sequence (finalised this session)

The A-to-B journey now has three distinct visual beats:

1. **Purple** — packet charges up at the smoking person, travels to outer A frontier
2. **Red (A snap)** — clicks into graph at the frontier, spring settles; node stays red while pulled inward toward B cluster (deliberately slow — half-speed lerp)
3. **Green (B maturation)** — only turns green once it has arrived near the B cluster; size grows from A radius to B radius

This was achieved by reordering phases: the spring-reorganise-as-B (still red) now happens BEFORE the green colour snap, not after.

---

## mal3 / mal4 person mechanics (finalised this session)

Both mal3 and mal4 now match mal2's person pattern:

- `personMesh` (red sphere, emissive `RGB(0.5,0,0)`, intensity 0.6) + `personIcon` (👤 sprite) created in `buildBaseGraph()`, cleaned up on rebuild.
- `smokeParticles` array; `tickSmoke(pos)` called every animation frame and in the idle render loop.
- `disposeSmoke()` called at top of `buildBaseGraph()`.
- Packet spawns at `personPos` and grows during charge-up (person emissive pulses). Approach direction: person → outermost A node.
- Person smokes throughout all phases of the animation.

### Half-speed constants (mal3 & mal4)
```
SNAP_FRAMES  =  16   (was 8)
LERP_FRAMES  =  28   (was 14)
SIZE_FRAMES  =  28   (was 14)
MATURE_LERP  =  76   (was 38)
CHARGE_FRAMES = 12   (new — charge-up at person)
```

---

## Person position
```
PERSON_X = 210, PERSON_Y = 30, PERSON_Z = 0
```
Consistent across mal1, mal2, mal3, mal4.

---

## Blockers

None.

---

## Exact next steps

### Phase 2: Website / Book

Alex wants to turn the GIFs into a "book" — a website that displays the GIFs alongside explainer text. He anticipates creating more explainer GIFs later.

**Recommended next action:** Open a new context window and use a BMAD persona (suggest **Mary** the business analyst or **Winston** the architect) to plan the website structure:
- What is the book's purpose and audience?
- How many chapters/sections? What narrative arc?
- Tech stack for the site (static HTML/CSS? Next.js? Something else?)
- How do GIFs map to sections of the explainer text?
- Will more GIFs be created in this same repo, or a separate one?

The GIF source files live at: `/Users/alexanderlarge/claude/2026-03-30 - A B U 3D visual/`
GitHub remote: `https://github.com/alex-is-learning/A-B-U-visuals`
