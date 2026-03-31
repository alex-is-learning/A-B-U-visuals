# ABU 3D Visual — Handover

**Date:** 2026-03-31
**Status:** GIF 1 (A-frontier) and GIF 3 (U filtering, all variants) complete. GIF 2 (A→B Maturation) not yet started.

---

## File inventory

| File | What it shows | Status |
|------|--------------|--------|
| `A-frontier.html` | GIF 1 — base graph builds, frontier A nodes spawn outward in a chain | Complete |
| `U-book-and-person.html` | GIF 3 combined — both U1 (book) approaches and snaps in, U2 (person) wobbles and fades | Complete |
| `U-to-A.html` | GIF 3a — book U only: approach → snap (purple→red, joins graph) | Complete |
| `U-rejected.html` | GIF 3b — person U only: approach → wobble/crackle → fade out | Complete |

No build step. All files are standalone HTML, CDN-only (Three.js 0.160.0, gif.js 0.2.0).

---

## What was done this session

1. **Confirmed `gif3.html` working** — all 5 animation phases read clearly.
2. **Split into two standalone GIFs** — `U-to-A.html` (book snaps in, no person) and `U-rejected.html` (person wobbles/fades, no book). The combined version is kept as `U-book-and-person.html`.
3. **Renamed all files** to semantic names (was `index.html`, `gif3.html`, `gif3-u1.html`, `gif3-u2.html`).
4. **Replaced line-art icons with emoji** — `📖` (book) and `👤` (person silhouette). Canvas 128×128, font 88px serif, sprite scale 30 (2× the original size). `👤` was chosen over `🧑` (too yellow) and `🙋‍♀️` (multi-codepoint ZWJ, less safe to render) for maximum rendering reliability.
5. **Fixed double-icon export bug in `U-book-and-person.html`** — `u1Icon` (book sprite) was never removed from the scene after the preview animation, so exporting GIF would replay the animation with the old book still visible. Fix: module-level `sprites = []` array; `buildBaseGraph()` now disposes all tracked sprites before rebuild; both icons are pushed to `sprites` when created.

---

## Architecture (unchanged from GIF 1 — applies to all files)

- **Spring layout:** custom, no d3-force-3d. KR=6000, KS=0.04, KCB=0.06, KCA=0.006, DAMP=0.85. 300 settle ticks on init, 20 ticks per spawn.
- **Frustum clamp:** iterative binary search (24 iterations), camera-position-agnostic. Applied to all nodes after every spring update.
- **Edge rendering:** pre-allocated `Float32Array(21×3)` per edge, updated in-place via `writeEdgeBuffer`. No geometry disposal during animation.
- **Scene rotation:** `scene.rotation.y = t * 0.00015` (Y pan), `scene.rotation.x = sin(t * 0.0003) * 0.10` (±5.7° X sway). Identical parameters across all files.
- **GIF export:** rebuilds base graph from scratch, replays full animation with `captureFrame` as `onFrame` callback. Blob URL worker (CORS bypass). 90s safety timeout.

---

## Blockers

None.

---

## Exact next steps

### GIF 2 — A→B Maturation

This is the only remaining deliverable. Create `A-to-B.html`.

Concept: an active thought (A node, red) matures into a belief (B node, green). The network reorganises around it.

Suggested animation sequence:

1. **Initial hold** — base graph visible and rotating (~0.5s)
2. **Target A pulses** — one A node gently pulses/brightens to draw attention (~0.5s)
3. **Colour snap** — instant red → green colour change on that node, emissive flash
4. **Size change** — node lerps from RADIUS_A (4) to RADIUS_B (6) over ~0.5s
5. **Spring reorganise** — the new B now has stronger centre pull (KCB vs KCA); run spring ticks and lerp all nodes to new positions (~1s)
6. **Hold end state** — new B settled among the other Bs (~0.5s)

Key implementation notes:
- The matured node needs its sim entry updated: `type: 'A'` → `type: 'B'`, so the spring centre-pull constant switches from KCA to KCB.
- Edge weights to/from the matured node could be updated (stronger, shorter target distance) to pull it inward toward the B cluster.
- Reuse the same `clampToView` + all-nodes clamp pattern after the spring update.
- Reuse `buildBaseGraph`, `runSpring`, `makeEdgeLine`, `writeEdgeBuffer`, `renderFrame`, `exportGif` verbatim from any existing file — the structure is identical.
- Download filename: `gif2-a-to-b.gif`.

Full spec is in `_bmad-output/implementation-artifacts/deferred-work.md`.
