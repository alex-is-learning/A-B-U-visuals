---
title: 'ABU 3D Base Graph + GIF 1 — A Frontier Growth'
type: 'feature'
created: '2026-03-30'
status: 'done'
baseline_commit: 'NO_VCS'
context: ['_bmad-output/brainstorming/brainstorming-session-2026-03-30-2100.md']
---

<frozen-after-approval reason="human-owned intent — do not modify unless human renegotiates">

## Intent

**Problem:** The A/B/U epistemological framework has no visual representation for the book. Readers need to see the network to grasp how beliefs (Bs), active thoughts (As), and external inputs (Us) relate spatially.

**Approach:** Build a single `index.html` using Three.js that renders a 3D force-directed network (base state: 5 green Bs centrally clustered, ~15 red As in radiating layers), then animates GIF 1 — 5 new As appearing sequentially at the outer frontier while the network subtly leans toward the growth area — and exports it as an 800×600 GIF at 30fps.

## Boundaries & Constraints

**Always:**
- All code lives in a single `index.html` with CDN imports only — no npm, no bundler
- Three.js for 3D rendering; d3-force-3d for layout computation
- Green spheres = Bs, red spheres = As; pure black background; fixed camera (no user rotation)
- Curved edges with opacity encoding connection strength (thicker/brighter = stronger)
- 800×600 canvas; 30fps GIF export

**Ask First:**
- If d3-force-3d produces a visually bad layout (nodes overlapping badly or too spread to read) — pause and show Alex before animating

**Never:**
- Mouse/keyboard camera controls or interactive UI beyond a "Download GIF" button
- GIF 3 or GIF 2 content (deferred)
- Server-side code, build tools, or npm dependencies

## I/O & Edge-Case Matrix

| Scenario | Input / State | Expected Output / Behavior | Error Handling |
|----------|--------------|---------------------------|----------------|
| Page load | Browser opens index.html | Scene renders within 2s: 5 green Bs clustered centrally, ~15 red As in radiating layers, curved edges | White error text on black background |
| Animation plays | Base graph settled | 5 new red As appear one by one at outer frontier; network shifts toward growth area after each | Animation stalls visibly — note in console |
| GIF export | User clicks "Download GIF" | 800×600 GIF named `gif1-frontier-growth.gif` downloads | Button shows "Export failed" if gif.js errors |

</frozen-after-approval>

## Code Map

- `index.html` — sole file; contains Three.js scene, d3-force-3d layout, GIF 1 animation, and gif.js export

## Tasks & Acceptance

**Execution:**
- [x] `index.html` -- create: Three.js scene setup — PerspectiveCamera (FOV 60, fixed position z=200), WebGLRenderer 800×600, black background, ambient + point lighting -- scaffolds the rendering environment
- [x] `index.html` -- add: base graph — 5 Bs (green `#44ff88`, radius 6) at center cluster, 15 As (red `#ff4444`, radius 4) in 2–3 radiating layers; assign random edge weights (0.2–1.0); use d3-force-3d to run 300 synchronous ticks for a settled layout before first render -- establishes the shared base graph state
- [x] `index.html` -- add: curved edges — for each edge, draw a QuadraticBezierCurve3 (midpoint offset 15 units perpendicular to edge direction); `LineBasicMaterial` with opacity = edge weight, transparent = true -- encodes connection strength visually
- [x] `index.html` -- add: GIF 1 animation — after base renders, sequentially spawn 5 new red As at frontier positions (furthest from center); one every 600ms; after each spawn run 20 additional d3-force-3d ticks and update all node positions so the network leans toward the new node -- implements A Frontier Growth
- [x] `index.html` -- add: gif.js export — "Download GIF" button (white, top-right overlay); clicking it re-runs the full animation while gif.js captures each frame at 30fps; on completion triggers download as `gif1-frontier-growth.gif` -- enables saving the output

**Acceptance Criteria:**
- Given the page loads, when the scene initialises, then 5 green spheres appear clustered centrally and ~15 red spheres radiate outward in visible layers with curved edges connecting them
- Given the base graph is visible, when the animation plays, then 5 new red spheres appear one by one at the outer frontier and the existing network visibly shifts toward the growth area after each addition
- Given the animation has completed, when the user clicks "Download GIF", then an 800×600 GIF file downloads and plays back correctly in a browser or image viewer

## Design Notes

- **Edge thickness:** Three.js WebGL `linewidth > 1` is not supported on most platforms — encode strength via opacity only (or use `TubeGeometry` for thick edges if opacity alone reads poorly)
- **d3-force-3d layout:** Run `simulation.tick(300)` synchronously before first render; no live simulation needed after that — positions are fixed until animation spawns new nodes
- **gif.js worker:** Use `workerScript: 'https://cdn.jsdelivr.net/npm/gif.js/dist/gif.worker.js'`; if cross-origin worker is blocked, inline the worker script as a Blob URL

## Verification

**Manual checks:**
- Open `index.html` in Chrome — scene renders within 2 seconds; Bs and As are clearly distinguishable by colour and cluster position
- Animation plays through all 5 new As without freezing or layout explosion
- Clicking "Download GIF" produces a valid file that opens and loops correctly

## Spec Change Log

**Loop 1 — intent_gap AA-2/AA-3 (2026-03-30)**
- **Trigger:** First implementation added a "Replay Animation" button (violating the Never constraint) and did not auto-play the animation (violating AC2). Spec left ambiguous whether auto-play was required.
- **Amended:** Alex confirmed: remove Replay button; animation auto-plays on page load. Never constraint stands.
- **Known-bad state avoided:** Keeping Replay would contradict the frozen Never constraint.
- **KEEP:** gif.js worker blob-URL fallback; in-place edge geometry update (writeEdgeBuffer); Map-based startPos; custom spring layout replacing d3-force-3d.

## Suggested Review Order

**Layout & physics (the foundation everything else builds on)**

- Custom spring simulation — repulsion + spring + centre pull; 300 settle ticks before first render
  [`index.html:69`](../../index.html#L69)

**Scene and graph construction**

- Scene setup: camera z=200, black background, ambient + point light
  [`index.html:46`](../../index.html#L46)

- buildBaseGraph: dispose → create simNodes/simEdges → runSpring → meshes + edges
  [`index.html:115`](../../index.html#L115)

- makeEdgeLine / writeEdgeBuffer: pre-allocated buffer, updated in-place each lerp frame
  [`index.html:141`](../../index.html#L141)

**Animation**

- runGif1Animation: Promise-based, auto-plays on load; guard before Promise constructor
  [`index.html:172`](../../index.html#L172)

- spawnNext: frontier radius → new node → nearAs edges → spring update → lerp
  [`index.html:191`](../../index.html#L191)

- lerpFrame: ease-in-out lerp, all edges updated in-place via writeEdgeBuffer
  [`index.html:247`](../../index.html#L247)

**GIF export**

- createGifEncoder: fetches worker as blob URL to avoid cross-origin worker block
  [`index.html:290`](../../index.html#L290)

- exportGif: rebuild → animate with captureFrame → 90s safety timeout
  [`index.html:306`](../../index.html#L306)
