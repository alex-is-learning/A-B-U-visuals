---
title: 'ABU 3D GIF 3 — U Filtering'
type: 'feature'
created: '2026-03-31'
status: 'review'
baseline_commit: 'NO_VCS'
context:
  - '_bmad-output/brainstorming/brainstorming-session-2026-03-30-2100.md'
  - '_bmad-output/implementation-artifacts/spec-abu-base-gif1.md'
---

<frozen-after-approval reason="human-owned intent — do not modify unless human renegotiates">

## Intent

**Problem:** Readers of the A/B/U book need to see how external inputs (Us) are evaluated — strong ones are absorbed as new active thoughts (As), weak ones wobble and disappear. No visual exists for this filtering mechanic.

**Approach:** Create `gif3.html` (a new standalone file, copy-scaffolded from `index.html`) that renders the same base graph as GIF 1, then animates GIF 3 — two purple U nodes approach from outside, one from a "book" source and one from a "person" source. The strong U snaps into the graph as a new A (purple→red colour change). The weak U wobbles near its target, detaches, and fades away. Export as an 800×600 GIF at 30fps.

## Boundaries & Constraints

**Always:**
- All code lives in a **new** `gif3.html` at the project root — no modifications to `index.html` (GIF 1 must not regress)
- Same CDN imports: Three.js 0.160.0, gif.js 0.2.0 — no npm, no bundler
- Same base graph: 5 green Bs clustered centre, 15 red As in radiating layers, custom spring layout, curved weighted edges
- Reuse all infrastructure from `index.html` verbatim: `runSpring`, `clampToView`, `makeEdgeLine`, `writeEdgeBuffer`, `disposeEdge`, `renderFrame`, `createGifEncoder`, `rnd`, `shuffle`, `buildBaseGraph`
- U nodes are purple `#aa44ff`, radius 4 (same as A)
- The animation auto-plays on page load (no replay button)
- GIF exports as `gif3-u-filtering.gif`
- Scene rotation identical to GIF 1 (`renderFrame` pattern unchanged)

**Ask First:**
- If the wobble amplitude or frequency feels too subtle or too chaotic after a first pass — pause and show Alex before continuing to export

**Never:**
- Modify `index.html` — GIF 1 must remain untouched
- Mouse/keyboard camera controls or interactive UI beyond "Download GIF"
- GIF 1 or GIF 2 content
- Server-side code, build tools, or npm dependencies

## I/O & Edge-Case Matrix

| Scenario | Input / State | Expected Output / Behavior | Error Handling |
|----------|--------------|---------------------------|----------------|
| Page load | Browser opens gif3.html | Scene renders within 2s: same base graph as GIF 1 | White error text on black background |
| Animation plays | Base graph settled | U1 (book source) and U2 (person source) appear at outer positions; both travel inward; U1 snaps into graph as A (purple→red, spring update, lerp); U2 wobbles near target, detaches, drifts outward while fading to opacity 0 | Animation stalls visibly — note in console |
| GIF export | User clicks "Download GIF" | 800×600 GIF named `gif3-u-filtering.gif` downloads and loops correctly | Button shows "Export failed" if gif.js errors |
| U2 fully fades | opacity reaches 0 | U2 mesh and icon sprite removed from scene; animation marked complete | — |

</frozen-after-approval>

## Architecture Notes for Developer

### File structure

`gif3.html` is a full copy of `index.html` with these changes:
- Title updated to `"A/B/U 3D Network — GIF 3: U Filtering"`
- `runGif1Animation` removed and replaced with `runGif3Animation`
- `exportGif` updated: calls `runGif3Animation`, downloads as `gif3-u-filtering.gif`
- `downloadAuditLog` can be removed or adapted (low priority)
- Constants section gains `COLOR_U`, `U_RADIUS`, `WOBBLE_CYCLES`, `WOBBLE_AMPLITUDE_START`, `APPROACH_FRAMES`, `WOBBLE_FRAME_PERIOD`, `FADE_FRAMES`

### Node types in GIF 3

| Type | Colour hex | THREE colour int | Radius | Count |
|------|-----------|-----------------|--------|-------|
| B | `#44ff88` | `0x44ff88` | 6 | 5 |
| A | `#ff4444` | `0xff4444` | 4 | 15 |
| U (purple) | `#aa44ff` | `0xaa44ff` | 4 | 2 (spawned during animation) |
| A_absorbed | `#ff4444` | `0xff4444` | 4 | 1 (U1 after snap) |

### Source icons

Each U node gets a `THREE.Sprite` companion placed 14 units below its sphere. The sprite texture is a 32×32 canvas with a white symbol drawn on a transparent background:

- **U1 (book icon):** draw a small open-book outline — two rectangles with a centre spine — using `fillRect` / `strokeRect`
- **U2 (person icon):** draw a filled circle (head) + vertical rectangle (body) using `arc` + `fillRect`

Create icon with:
```javascript
function makeIconSprite(symbol) {
  // symbol: 'book' | 'person'
  const canvas = document.createElement('canvas');
  canvas.width = 32; canvas.height = 32;
  const ctx = canvas.getContext('2d');
  ctx.strokeStyle = '#ffffff'; ctx.fillStyle = '#ffffff'; ctx.lineWidth = 2;
  if (symbol === 'book') {
    // left page
    ctx.strokeRect(4, 6, 10, 20);
    // right page
    ctx.strokeRect(18, 6, 10, 20);
    // spine
    ctx.beginPath(); ctx.moveTo(14, 6); ctx.lineTo(14, 26); ctx.stroke();
  } else {
    // head
    ctx.beginPath(); ctx.arc(16, 9, 5, 0, Math.PI*2); ctx.fill();
    // body
    ctx.fillRect(12, 16, 8, 10);
  }
  const tex = new THREE.CanvasTexture(canvas);
  const mat = new THREE.SpriteMaterial({ map: tex, transparent: true, opacity: 1 });
  const sprite = new THREE.Sprite(mat);
  sprite.scale.set(10, 10, 1);
  return sprite;
}
```

Place sprite at `(u.x, u.y - 14, u.z)`. When the U node moves, move the sprite with it. When U2 fades, also reduce `sprite.material.opacity` to match the mesh opacity. Remove sprite from scene when the U is cleaned up.

### Animation sequence

Total animation duration: ~6.5 seconds before GIF export hold frames.

```
t=0ms      Base graph visible, scene rotating
t=600ms    U1 (book) and U2 (person) appear at outer positions + icons, scale-pop effect
t=600ms    Both Us begin approaching their target A nodes simultaneously (APPROACH_FRAMES lerp)
t=~2200ms  U1 reaches proximity → SNAP: colour purple→red, spring update (20 ticks), lerp all nodes
t=~2900ms  U2 begins wobble (WOBBLE_CYCLES × WOBBLE_FRAME_PERIOD frames)
t=~4700ms  U2 detaches: moves outward along its outward direction, opacity lerps 1→0 over FADE_FRAMES
t=~5700ms  U2 fully transparent → remove from scene → animation complete
```

### U approach path

Select **target A nodes** before animation starts:
- `targetA1`: the A node with the smallest index in the outermost ring (i ≥ 10)
- `targetA2`: the A node with index `targetA1_idx + 5` (mod BASE_A), so the two targets are ~120° apart on the ring

U1 start position: `targetA1.pos` scaled out to `maxR + 60` in the direction away from origin, then clamped.
U2 start position: `targetA2.pos` scaled out to `maxR + 60` in the opposite direction, then clamped.

Both approach positions are **fixed in world space** (not updated by spring during approach) — the Us travel along a straight lerp from start to a point 18 units from their target A.

### U1 snap mechanic

When U1 lerp completes (arrived at proximity):
1. Change `u1Mesh.material.color.setHex(COLOR_A)` — instant colour snap, no transition
2. Change `u1SimNode.type = 'A'`
3. Add 2 edges: from U1's sim index to `targetA1_idx` (weight 0.8) and to its second-nearest A (weight 0.4)
4. `makeEdgeLine` for each new edge, push to `edges`
5. `runSpring(simNodes, simEdges, 20)` — let U1 settle into the A layer
6. Clamp all nodes (same all-nodes clamp used in GIF 1)
7. Lerp all nodes over `LERP_FRAMES` frames (same pattern as GIF 1's `lerpFrame`)
8. U1's icon sprite should stay at its world position (it follows the mesh)

### U2 wobble mechanic

U2 has arrived at proximity of `targetA2`. It does **not** snap. Instead:

```javascript
// Wobble: oscillate position around arrival point over WOBBLE_CYCLES full cycles
let wobbleFrame = 0;
const totalWobbleFrames = WOBBLE_CYCLES * WOBBLE_FRAME_PERIOD;
const arrivalPos = { x: u2SimNode.x, y: u2SimNode.y, z: u2SimNode.z }; // arrival point
// Perpendicular wobble axis (perpendicular to approach direction in XY plane)
const wobbleAxis = { x: -approachDir.y, y: approachDir.x, z: 0 };

function wobbleStep() {
  wobbleFrame++;
  const phase = (wobbleFrame / WOBBLE_FRAME_PERIOD) * Math.PI * 2;
  // Amplitude decays slightly each cycle to suggest weak, losing grip
  const amplitude = WOBBLE_AMPLITUDE_START * (1 - wobbleFrame / totalWobbleFrames * 0.3);
  u2Mesh.position.set(
    arrivalPos.x + wobbleAxis.x * Math.sin(phase) * amplitude,
    arrivalPos.y + wobbleAxis.y * Math.sin(phase) * amplitude,
    arrivalPos.z
  );
  u2IconSprite.position.set(u2Mesh.position.x, u2Mesh.position.y - 14, u2Mesh.position.z);
  renderFrame();
  if (wobbleFrame < totalWobbleFrames) requestAnimationFrame(wobbleStep);
  else startFade();
}
```

### U2 fade mechanic

After wobble completes, U2 drifts outward (back along its approach direction) while opacity fades to 0:

```javascript
let fadeFrame = 0;
const driftDir = { x: -approachDir.x, y: -approachDir.y, z: -approachDir.z }; // outward
const fadeStartPos = { x: u2Mesh.position.x, y: u2Mesh.position.y, z: u2Mesh.position.z };

function fadeStep() {
  fadeFrame++;
  const t = fadeFrame / FADE_FRAMES;
  const opacity = Math.max(0, 1 - t);
  u2Mesh.material.transparent = true;
  u2Mesh.material.opacity = opacity;
  u2IconSprite.material.opacity = opacity;
  // Drift outward 30 units over FADE_FRAMES
  u2Mesh.position.set(
    fadeStartPos.x + driftDir.x * 30 * t,
    fadeStartPos.y + driftDir.y * 30 * t,
    fadeStartPos.z + driftDir.z * 30 * t
  );
  u2IconSprite.position.set(u2Mesh.position.x, u2Mesh.position.y - 14, u2Mesh.position.z);
  renderFrame();
  if (fadeFrame < FADE_FRAMES) requestAnimationFrame(fadeStep);
  else {
    scene.remove(u2Mesh); u2Mesh.geometry.dispose(); u2Mesh.material.dispose();
    scene.remove(u2IconSprite); u2IconSprite.material.map.dispose(); u2IconSprite.material.dispose();
    onComplete(); // resolve the promise
  }
}
```

### GIF export

Same pattern as GIF 1:
1. `buildBaseGraph()` → `renderFrame()`
2. Call `runGif3Animation(captureFrame)`
3. 20 hold frames at end
4. Download as `gif3-u-filtering.gif`

The export re-runs the full animation deterministically (same PRNG seed is not needed — the base graph layout will differ slightly but that's acceptable).

### Constants to add

```javascript
const COLOR_U             = 0xaa44ff;  // purple
const U_RADIUS            = 4;
const APPROACH_FRAMES     = 40;        // lerp frames for Us travelling inward
const WOBBLE_CYCLES       = 5;         // full oscillation cycles for U2
const WOBBLE_FRAME_PERIOD = 12;        // frames per wobble cycle (~25Hz at 30fps)
const WOBBLE_AMPLITUDE_START = 14;     // world units peak displacement
const FADE_FRAMES         = 30;        // frames to fade U2 from opacity 1 → 0
```

## Tasks & Acceptance

**Execution:**
- [x] `gif3.html` — create: copy `index.html` verbatim; update page title to "A/B/U 3D Network — GIF 3: U Filtering"; remove `runGif1Animation` and `downloadAuditLog`; update `exportGif` to call `runGif3Animation` and download `gif3-u-filtering.gif`; add `COLOR_U`, `U_RADIUS`, and the five animation constants to the constants block — establishes the GIF 3 scaffold with working base graph and GIF export infrastructure
- [x] `gif3.html` — add: `makeIconSprite(symbol)` helper; for U1 create 'book' sprite, for U2 create 'person' sprite; attach each sprite 14 units below its respective U sphere; sprites travel with their U mesh throughout the animation — provides source icons
- [x] `gif3.html` — add: `runGif3Animation(onFrame)` — approach phase: spawn U1 and U2 meshes at clamped outer positions (scale-pop effect, same emissive flash as GIF 1); lerp both simultaneously toward proximity of their target A nodes over `APPROACH_FRAMES` frames — implements the inbound approach
- [x] `gif3.html` — add: snap phase inside `runGif3Animation`: when approach lerp completes, immediately change U1 colour to `COLOR_A`, add 2 edges to nearest As, run 20 spring ticks, clamp all nodes, lerp all nodes over `LERP_FRAMES`; U1 icon sprite stays put — implements U1 absorption
- [x] `gif3.html` — add: wobble + fade phase inside `runGif3Animation`: when wobble begins, create a faint edge from U2 to `targetA2` (weight 0.15); each `wobbleStep` randomises the edge opacity (0.05–0.25) to produce a flickering unstable bond; after `WOBBLE_CYCLES` cycles, `disposeEdge` the U2 edge immediately, then run `fadeStep`; on fade complete remove U2 mesh + sprite, resolve animation promise — implements U2 rejection

**Acceptance Criteria:**
- Given the page loads, when the scene initialises, then the same base graph as GIF 1 is visible (5 green Bs central, 15 red As radiating)
- Given the base graph is visible, when the animation plays, then two purple spheres appear with small icons at outer positions and travel inward simultaneously
- Given both Us have arrived at proximity, when U1 snaps, then its colour immediately changes to red, new edges appear, and the network subtly shifts; U1's icon sprite remains at its position
- Given U1 has snapped, when U2 wobbles, then it visibly oscillates back and forth near its target A node for several cycles with gradually decreasing amplitude; a faint, flickering edge connects U2 to its target throughout the wobble
- Given U2 has wobbled, when it detaches, then it drifts outward while smoothly fading to invisible; at opacity=0 it is removed from the scene
- Given the animation has completed, when the user clicks "Download GIF", then an 800×600 GIF file named `gif3-u-filtering.gif` downloads and plays back correctly

## Design Notes

- **U2 edge during wobble:** Create a faint, flickering edge from U2 to its target A when the wobble phase begins. Use `makeEdgeLine` with `weight: 0.15` (very low opacity). On each `wobbleStep` frame, randomise the edge opacity: `u2Edge.line.material.opacity = 0.05 + Math.random() * 0.20` — this gives an unstable, crackling appearance that visually communicates a weak/failing bond. When the fade phase begins, remove this edge immediately (`disposeEdge(u2Edge.line)`) before the drift starts
- **U1 emissive flash:** Apply the same emissive flash effect as GIF 1's new A spawns — `emissiveIntensity: 2.0` at snap, fading to 0 over 14 frames — gives the snap a satisfying visual pop
- **Icon sprite opacity:** During the fade phase, `u2IconSprite.material.opacity` tracks `u2Mesh.material.opacity` exactly — they fade together
- **U2 mesh material:** Set `transparent: true` on U2's material **at creation time**, even though opacity starts at 1 — this avoids a rendering-order flicker when transparency is enabled later
- **Approach path target:** Us approach to within 18 world units of their target A's current position (not the sim node position — use `nodes[targetIdx].mesh.position`)
- **Clamping:** Only clamp during the snap spring update (same all-nodes clamp as GIF 1). Do NOT clamp during wobble or fade — the drift off-screen is intentional for U2

## Verification

**Manual checks:**
- Open `gif3.html` in Chrome — base graph renders within 2s with same look as GIF 1
- Two purple spheres with small white icons appear and travel inward
- U1 snaps red with a visible pop; network shifts; icon stays
- U2 wobbles visibly (5 oscillations), then drifts away fading to nothing
- `index.html` still works correctly (GIF 1 unaffected)
- Clicking "Download GIF" produces a valid GIF that loops correctly

## Spec Change Log

*(none yet)*
