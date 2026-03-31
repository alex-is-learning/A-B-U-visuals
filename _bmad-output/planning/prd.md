---
stepsCompleted: ['step-01-init', 'step-02-discovery', 'step-02b-vision', 'step-02c-executive-summary', 'step-03-success', 'step-04-journeys', 'step-05-domain', 'step-06-innovation', 'step-07-project-type', 'step-08-scoping', 'step-09-functional', 'step-10-nonfunctional', 'step-11-polish', 'step-12-complete']
classification:
  projectType: web_app
  domain: general
  complexity: low
  projectContext: brownfield
inputDocuments:
  - '_bmad-output/brainstorming/brainstorming-session-2026-03-30-2100.md'
  - 'HANDOVER.md'
  - '_bmad-output/implementation-artifacts/spec-abu-base-gif1.md'
  - '_bmad-output/implementation-artifacts/spec-abu-gif3-u-filtering.md'
  - '_bmad-output/implementation-artifacts/deferred-work.md'
workflowType: 'prd'
---

# Product Requirements Document - ABU 3D Visual: Maladaptive B

**Author:** Alex
**Date:** 2026-03-31

## Executive Summary

Three completed Three.js animations (A-frontier growth, A→B maturation, U-node filtering) visualize the ABU epistemological framework as a healthy, functioning system. This PRD covers four new animations depicting the shadow side: what happens when a consolidated belief (B) becomes maladaptive.

The series is grounded in Robert Kegan's *Immunity to Change*: consolidated beliefs drive harmful behaviour without internal awareness; corrective feedback frequently fails to penetrate an entrenched B (landing as a rejected U); change becomes possible only when feedback is scaffolded to meet the system's A frontier.

Target audience: readers of Alex's forthcoming book on the ABU framework. Animations are embedded in book and/or presentation materials alongside the existing GIFs.

**The four new animations:**

1. **mal1 — Maladaptive B fires:** A B acting normally emits a pulse that burns an external person. The B has no internal signal of harm; damage is visible only at the target.
2. **mal2 — Feedback rejected:** The burnt person sends signal back. It approaches and is rejected as a U. The B remains unchanged — Kegan's immunity in action.
3. **mal3 — Feedback integrated, equal:** Scaffolded feedback lands at the A frontier, matures into a new B, and forms an equal binary orbit with the original maladaptive B.
4. **mal4 — Feedback integrated, dominant:** Same integration path, but the new B grows dominant and the original shrinks to a moon — both remaining in orbit.

### What Makes This Special

The maladaptiveness is invisible from inside the system. The B behaves normally — it acts from consolidated truth. Harm exists only at the external consequence. Visual thesis: maladaptive beliefs feel like functioning correctly. The animation mechanics carry the philosophical argument without labels.

Together with the existing GIFs, the full set maps the complete ABU lifecycle — healthy growth and the failure modes that make change hard.

## Project Classification

- **Project Type:** Standalone browser-rendered animation (HTML/JS, CDN-only, no build step)
- **Domain:** Educational visualization — epistemology/philosophy book content
- **Complexity:** Low — no backend, no auth, no compliance. Constraints are visual and aesthetic.
- **Project Context:** Brownfield — existing spring layout, render pipeline, node types, and GIF export infrastructure are proven and reusable.

## Success Criteria

### User Success

A reader encountering any animation understands what is happening from the visual alone. Explainer text accompanies each GIF in the book — the animation reinforces the text, not replaces it. Fire, movement, and spatial relationships carry the meaning.

### Business Success

Four standalone HTML files delivered, matching the quality and aesthetic of the five existing GIFs. The set is complete for the book.

### Technical Success

- Each file renders correctly in a modern browser with no build step
- GIF export produces a clean, looping file suitable for web/print embedding
- Visual language (node colours, sizes, spring layout, scene rotation, bezier edges) consistent with existing files
- No regressions to existing files

### Measurable Outcomes

The existing five GIFs are the quality benchmark. Each new animation is indistinguishable in style and technical quality from those files.

## Product Scope

### MVP

Four standalone HTML files:
1. `mal1-maladaptive-b-fires.html` — B pulses outward, external person is burned
2. `mal2-feedback-rejected.html` — Burnt person sends signal back; rejected as a U
3. `mal3-feedback-integrated-equal.html` — Feedback lands as A, matures to B, forms equal binary orbit with original
4. `mal4-feedback-integrated-dominant.html` — Same integration; new B grows dominant, old B shrinks to moon

Each includes GIF export capability matching the existing pattern.

### Growth Features (Post-MVP)

None identified. Self-contained deliverable.

### Vision (Future)

Combined animation showing all phases in sequence (burn → reject → integrate) as a single narrative arc. Not required for the book.

## Functional Requirements

### Shared Network State

- **FR1:** Viewer can observe all four animations starting from an identical base network (B cluster centrally positioned, A nodes radiating outward in layers)
- **FR2:** Viewer can identify node types by colour: B nodes green, A nodes red, incoming signals purple
- **FR3:** Viewer can observe an external person entity positioned outside the network boundary, visually distinct from network nodes
- **FR4:** Viewer can observe the network undergoing subtle spring-layout motion throughout (consistent with existing GIFs)

### Animation 1 — Maladaptive B Fires (mal1)

- **FR5:** Viewer can observe a specific B node emitting a pulse or beam directed outward toward the external person
- **FR6:** Viewer can observe the B node showing no internal change before, during, or after firing — it continues behaving as a normal B
- **FR7:** Viewer can observe the external person receiving the pulse and showing a visible harm response (fire, burning, or equivalent)
- **FR8:** Viewer can observe the harm effect localised entirely at the external person — no change to the network

### Animation 2 — Feedback Rejected (mal2)

- **FR9:** Viewer can observe the external person (post-burn, showing residual harm — smouldering or smoke) emitting a signal back toward the network
- **FR10:** Viewer can observe the returning signal travelling toward the B network in a charged, direct path aimed at the B cluster
- **FR11:** Viewer can observe the signal rejected at the network boundary using the U-rejection mechanic (wobble/crackle/fade)
- **FR12:** Viewer can observe the B network — including the original maladaptive B — remaining completely unchanged after rejection
- **FR13:** Viewer can observe the rejected signal dissipating, conveying failure to penetrate

### Animation 3 — Feedback Integrated, Equal (mal3)

- **FR14:** Viewer can observe a signal approaching the A frontier (not the B cluster directly) at a measured, smooth pace — visually distinct from the charged, direct approach in mal2. The approach path and character communicate "this is landing differently" before it arrives.
- **FR15:** Viewer can observe the signal absorbed at the A frontier as a new A node
- **FR16:** Viewer can observe the network reorganising as the new A node is integrated — spring adjustment, edges forming
- **FR17:** Viewer can observe the new A node maturing into a B node (colour change red→green, size increase, inward drift toward B cluster)
- **FR18a:** Viewer can observe the new B and the original maladaptive B moving to adjacent positions and entering a slow mutual orbit as equals — same size and brightness, neither dominant

### Animation 4 — Feedback Integrated, Dominant (mal4)

- **FR14b–FR17b:** Same approach, A-landing, integration, and B-maturation sequence as mal3
- **FR18b:** Viewer can observe the new B growing larger/brighter while the original maladaptive B visibly shrinks — new B becomes primary body, old B becomes its moon, both remaining in mutual orbit

### Shared — Binary Orbital Mechanic (mal3 and mal4)

- **FR18c:** Viewer can observe the orbital pair remaining gravitationally integrated into the broader B cluster — not drifting away from it

### GIF Delivery

- **FR19:** Author can export each animation as a looping GIF using the in-browser export function
- **FR20:** Author can open each animation in a modern browser without a build step or local server
- **FR21:** Files use `mal` prefix for alphabetical sorting: `mal1-maladaptive-b-fires.html`, `mal2-feedback-rejected.html`, `mal3-feedback-integrated-equal.html`, `mal4-feedback-integrated-dominant.html`

## Non-Functional Requirements

### Performance

- Each animation renders at a smooth, visually continuous frame rate in a modern browser
- GIF export completes within the existing 90-second safety timeout
- Spring layout settles cleanly before animation begins — no visible jitter at animation start

### Compatibility

- Each file opens and runs in Chrome, Firefox, and Safari without a build step, local server, or installed dependencies
- All dependencies loaded via CDN: Three.js 0.160.0, gif.js 0.2.0 — consistent with existing files
