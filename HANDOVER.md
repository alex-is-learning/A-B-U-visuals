# ABU 3D Visual — Handover

**Date:** 2026-03-31
**Status:** All three original GIFs complete. Repo is on GitHub. Maladaptive B series: PRD complete, all four files implemented.

---

## File inventory

| File | What it shows | Status |
|------|--------------|--------|
| `A-frontier.html` | GIF 1 — base graph builds, frontier A nodes spawn outward in a chain | Complete |
| `A-to-B.html` | GIF 2 — cyan laser fires from offscreen, strikes an A node, it matures into a B and the network reorganises | Complete |
| `U-book-and-person.html` | GIF 3 combined — both U1 (book) approaches and snaps in, U2 (person) wobbles and fades | Complete |
| `U-to-A.html` | GIF 3a — book U only: approach → snap (purple→red, joins graph) | Complete |
| `U-rejected.html` | GIF 3b — person U only: approach → wobble/crackle → fade out | Complete |
| `mal1-maladaptive-b-fires.html` | mal1 — B0 fires green pulse at external person (grey sphere + 👤), person burns orange, B unchanged | Complete |
| `mal2-feedback-rejected.html` | mal2 — smouldering person sends charged purple signal at B cluster; rejected via wobble/fade mechanic; B unchanged | Complete |
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
- **Laser:** straight cyan (0x00ddff) `THREE.Line` from world pos (420, 270, 0) — off-screen upper-right — to target A node. Straight line is visually distinct from the curved bezier edges.
- **Dot:** small cyan sphere (radius 2.5, high emissive) travels along the beam, then "splashes" on impact (scales up 4× while fading).
- **Target selection:** A node at 25th percentile by distance from origin — close enough for visible inward snap after maturation, not already inside the B cluster.
- **Maturation sequence:** beam arrives → impact flash (cyan) → colour snap red→green → size lerp 1.0→1.5× → spring reorganise (type switches A→B, centre pull 10× stronger, edges to B nodes upgraded to strong weight) → hold.
- **Beam hidden on init:** `beamLine` and `dotMesh` are added to scene before phase 0 but set `visible = false`; made visible only when the laser fires in phase 1.

---

## Blockers

None.

---

## Exact next steps

### Maladaptive B series — COMPLETE

All four files implemented. Open each in a browser to review before exporting GIFs.

**Orbital mechanic notes (mal3/mal4):**
- Orbit centre = midpoint of B0 and newB post-maturation positions
- Orbital radius = `max(22, halfDist)` — minimum 22 world units ensures visible orbit
- Orbit in XY plane at `ORBIT_SPEED = 0.018 rad/frame` (~10.5s/revolution)
- B0 and newB start at angle `atan2(toB0.y, toB0.x)` and `angle + π` respectively — no jump on orbit start
- All edges connected to either orbiting node are updated per frame via `writeEdgeBuffer`
- mal4 delta: over first 45 orbit frames, newB scale 1.5→2.0 + emissiveIntensity 0→0.8; B0 scale 1.0→0.5
