# ABU Project — Handover

**Date:** 2026-03-31
**Status:** Phase 1 (GIFs) complete. Phase 2 PRD complete. Ready to plan/build the book website.

---

## What exists

### Phase 1: GIF animations (complete)

| File | What it shows |
|------|--------------|
| `A-frontier.html` | GIF 1 — A frontier growth |
| `A-to-B.html` | GIF 2 — A→B maturation |
| `U-book-and-person.html` | GIF 3 combined — U1 snaps in, U2 fades |
| `U-to-A.html` | GIF 3a — book U only |
| `U-rejected.html` | GIF 3b — person U only |
| `mal1-maladaptive-b-fires.html` | mal1 — B0 fires at person |
| `mal2-feedback-rejected.html` | mal2 — person feedback rejected |
| `mal3-feedback-integrated-equal.html` | mal3 — feedback integrated, equal orbit |
| `mal4-feedback-integrated-dominant.html` | mal4 — feedback integrated, dominant |

All standalone HTML, CDN-only (Three.js 0.160.0, gif.js 0.2.0). No build step.

### Phase 2: PRD (complete)

`_bmad-output/planning/prd-website-book.md` — full product requirements document for the book website. 43 functional requirements, 17 non-functional requirements, 4 user journeys, innovation analysis, scoping.

---

## What we're building (Phase 2)

An **interactive book** teaching the A/B/U epistemological framework — a *metacognition-foregrounding medium*. Key mechanics:

- Readers self-assess with **A/B/U buttons** at each section
- **A/B** → advance to next section; **U** → descend to scaffold
- **LLM chat** (RAG on book content) answers questions on U clicks — tier-1 support
- **Text feedback form** (Formspree) — tier-2 escalation to Alex
- **PostHog analytics** — A/B/U click heatmap per section; U-spikes = content research
- **Pay-what-you-got** at the end — repeat payment capable (Gumroad/Stripe)
- **Password-protected** for MVP inner-circle (Defender + collaborators)

Analogous to Quantum Country / Orbit but for metacognition, with adaptive routing + LLM + value-based pricing.

---

## Architectural decisions made

| Decision | Choice | Rationale |
|---|---|---|
| Hosting | Netlify | Free tier, auto-deploy from GitHub, built-in serverless functions, password protection options |
| Static vs dynamic | Static SPA (single HTML shell, JS routing) | No build step, consistent with GIF architecture, no framework needed |
| Content format | Markdown files, one per section | Author writes in Markdown; `marked.js` CDN renders client-side |
| Branching config | `content.json` — maps section ID → A/B target, U target, stub flag | Editable without touching core JS; new sections = new file + config line |
| LLM integration | Netlify serverless function (`netlify/functions/chat.js`) → Claude API | API key in env var, never browser-exposed; scales automatically |
| LLM approach | RAG on book content + ~1% poetic license for novel metaphors | Bounds hallucination; allows generative explanations within framework |
| Analytics | PostHog (free tier, custom events) | Per-section A/B/U heatmap; `posthog.capture('abu_click', {section, value})` |
| Feedback | Formspree | Serverless form submission → email to Alex with section context |
| Payment | Gumroad or Stripe (TBD) | Must support repeat payments (value is time-delayed, readers return over years) |
| State persistence | localStorage | Session reading position, A/B/U history, LLM conversation |
| Browser support | Modern only (Chrome/Firefox/Safari/Edge last 2 versions) | Three.js WebGL already excludes legacy browsers |
| MVP scope | 5–6 sections, flat branching (one scaffold level per section) | Start lean; add depth based on U-spike data |

---

## Blockers

None. The PRD is complete and implementation-ready.

---

## Exact next steps

The PRD is the foundation. The next phase is to plan and then build. Four options — pick one or discuss which order makes sense:

### Option A — Create Epics & Stories (start building path)
**Skill:** `bmad-create-epics-and-stories` (or tell John "CE")
**What it does:** Breaks the 43 FRs into epics and user stories with acceptance criteria. Output feeds directly into development.
**When to choose:** If you want to start coding soon and are happy with the architecture being designed as you go.

### Option B — Check Implementation Readiness
**Skill:** `bmad-check-implementation-readiness` (or tell John "IR")
**What it does:** Validates the PRD has everything needed before architecture/UX work begins. Identifies gaps.
**When to choose:** If you want confidence the PRD is solid before investing in architecture or UX design.

### Option C — Design Architecture First
**Agent:** Winston (architect) — say "talk to Winston" or use `bmad-agent-architect`
**What it does:** Designs the technical architecture — file structure, data flow, component design, LLM integration pattern, branching config schema.
**When to choose:** If you want a clear technical blueprint before writing a line of code. Recommended given the LLM integration and branching config are novel enough to benefit from upfront design.

### Option D — Design UX First
**Agent:** Sally (UX designer) — say "talk to Sally" or use `bmad-agent-ux-designer`
**What it does:** Designs the reading experience — how sections feel, how A/B/U buttons look and behave, LLM chat UI, payment prompt, mobile layout.
**When to choose:** If you want to nail the reading experience design before implementation. The UX of the A/B/U buttons is load-bearing — it shapes whether readers actually use them.

### Recommended order
**B → C → A** or **C → A** (skip B if you trust the PRD):
1. (Optional) IR check — 30 minutes, catches any gaps
2. Winston for architecture — defines the file structure and config schema Alex will actually work with
3. Epics & stories — now implementation is straightforward

UX (Sally) can run **in parallel with or after architecture** — the reading experience design doesn't block the technical foundation.

---

## Key context for next session

- Alex is sole developer/author, AI-assisted
- Writing the 5–6 sections is the main time investment (not engineering)
- Content pacing note from brainstorming: three timescales — fast (U binding), medium (A growth), slow (A→B maturation) — worth considering when ordering sections
- Defender is ready to review and signal-boost once MVP is ready
- ORI profit-sharing model in place
- Payment validation is a 3–5 year signal — early low payments are not failure
- The book is collaborative with Defender (Open Research Institute: openresearchinstitute.org)

---

## Files

```
/Users/alexanderlarge/claude/2026-03-30 - A B U 3D visual/
├── HANDOVER.md                          ← this file
├── A-frontier.html
├── A-to-B.html
├── U-book-and-person.html
├── U-to-A.html
├── U-rejected.html
├── mal1-maladaptive-b-fires.html
├── mal2-feedback-rejected.html
├── mal3-feedback-integrated-equal.html
├── mal4-feedback-integrated-dominant.html
└── _bmad-output/
    └── planning/
        ├── prd.md                       ← GIF animations PRD (complete, Phase 1)
        └── prd-website-book.md          ← Book website PRD (complete, Phase 2)
```
