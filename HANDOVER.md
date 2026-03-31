# ABU 3D Visual — Handover

**Date:** 2026-03-31
**Status:** All three original GIFs complete. Repo on GitHub. Maladaptive B series: all four files implemented. Feedback round: mal1 and mal2 revised and approved.

---

## File inventory

| File | What it shows | Status |
|------|--------------|--------|
| `A-frontier.html` | GIF 1 — base graph builds, frontier A nodes spawn outward in a chain | Complete |
| `A-to-B.html` | GIF 2 — cyan laser fires from offscreen, strikes an A node, it matures into a B and the network reorganises | Complete |
| `U-book-and-person.html` | GIF 3 combined — both U1 (book) approaches and snaps in, U2 (person) wobbles and fades | Complete |
| `U-to-A.html` | GIF 3a — book U only: approach → snap (purple→red, joins graph) | Complete |
| `U-rejected.html` | GIF 3b — person U only: approach → wobble/crackle → fade out | Complete |
| `mal1-maladaptive-b-fires.html` | mal1 — B0 fires fire-red/orange pulse at external person; person burns, smokes, sphere turns red | Complete + Revised |
| `mal2-feedback-rejected.html` | mal2 — smouldering red person (mal1 end-state) sends charged purple signal at B cluster; rejected; person smokes throughout | Complete + Revised |
| `mal3-feedback-integrated-equal.html` | mal3 — scaffolded signal approaches outer A frontier, snaps as A, matures to B, forms equal binary orbit with B0 | Complete |
| `mal4-feedback-integrated-dominant.html` | mal4 — same as mal3 but newB grows to 2× scale with emissive glow; B0 shrinks to 0.5× moon | Complete |

No build step. All files are standalone HTML, CDN-only (Three.js 0.160.0, gif.js 0.2.0).

---

## Git / remote

- Local git repo initialised at this folder root.
- Remote: `https://github.com/alex-is-learning/A-B-U-visuals` (branch `main`).
- All files committed and pushed. Future workflow: make changes, `/commit`, `git push`.

---

## Architecture (applies to all files)

- **Spring layout:** custom, no d3-force-3d. KR=6000, KS=0.04, KCB=0.06, KCA=0.006, DAMP=0.85. 300 settle ticks on init, 20–60 ticks per animation event.
- **Node types:** B (green, radius 6, centre pull KCB), A (red, radius 4, centre pull KCA). B cluster sits centrally; A nodes form a radiating ring.
- **Frustum clamp:** iterative binary search (24 iterations), camera-position-agnostic. Applied to all nodes after every spring update.
- **Edge rendering:** pre-allocated `Float32Array(21×3)` per edge, updated in-place via `writeEdgeBuffer`. Curved bezier lines. No geometry disposal during animation.
- **Scene rotation:** `scene.rotation.y = t * 0.00015` (Y pan), `scene.rotation.x = sin(t * 0.0003) * 0.10` (±5.7° X sway). Identical across all files.
- **GIF export:** rebuilds base graph from scratch, replays full animation with `captureFrame` as `onFrame` callback. Blob URL worker (CORS bypass). 90s safety timeout.
- **Canvas:** 800×600, camera at z=360, FOV 60°. Frustum at z=0 ≈ ±277 × ±208 world units.

### GIF 2 specifics (A-to-B.html)
- **Laser:** straight cyan (0x00ddff) `THREE.Line` from world pos (420, 270, 0) — off-screen upper-right — to target A node.
- **Dot:** small cyan sphere (radius 2.5, high emissive) travels along the beam, then "splashes" on impact.
- **Target selection:** A node at 25th percentile by distance from origin.
- **Maturation sequence:** beam arrives → impact flash → colour snap red→green → size lerp 1.0→1.5× → spring reorganise.

---

## mal1 / mal2 person mechanics (revised this session)

### Person object pattern (mal1 & mal2)
- `personMesh` and `personIcon` are **module-level** variables, created in `buildBaseGraph()` so they are present from frame 1.
- `buildBaseGraph()` cleans them up at the top before rebuilding (safe for GIF re-export).

### mal1 end-state (the "hit" state)
- `personMesh.material.color` = `0xff0000` (red)
- `personMesh.material.emissive` = `RGB(0.5, 0, 0)`
- `personMesh.material.emissiveIntensity` = `0.6`
- Sphere is smoking continuously (see smoke system below)

### mal2 start-state
- Matches mal1 end-state exactly: red sphere, faint red emissive glow, smoking from frame 1.

### mal1 beam / dot colours
- Beam: `0xff2200` (fire red)
- Dot: `0xff6600` (orange), emissive `Color(1, 0.4, 0)`

### Smoke system (mal1 & mal2)
- `smokeParticles` array of `{ mesh, vx, vy, life, maxLife }` objects.
- **`tickSmoke(pos)`** — call every frame while smoking: spawns 1–2 new grey sphere particles at `pos`, updates all existing particles (rise + decelerate + expand + fade), culls dead ones in-place. Self-contained; no separate create/update calls needed.
- **`disposeSmoke()`** — removes and disposes all remaining particles; resets array.
- **mal1 `smokeActive` flag:** smoke only begins at impact (phase2 start sets `smokeActive = true`). Idle render loop guards on `personMesh && smokeActive`. `buildBaseGraph()` resets flag to `false` and calls `disposeSmoke()` for clean GIF re-runs.
- **mal2:** no flag needed — person is always smoking, `tickSmoke` called in every phase tick and the idle render loop.

---

## Blockers

None.

---

## Exact next steps

### Feedback round — mal3 and mal4 still to review

Workflow:
1. Ask Alex to open mal3 in the browser and describe what he sees / what he wants changed
2. Make the edit, he reviews again
3. Repeat for mal4
