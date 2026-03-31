---
stepsCompleted: ['step-01-init', 'step-02-discovery', 'step-02b-vision', 'step-02c-executive-summary', 'step-03-success', 'step-04-journeys', 'step-05-domain', 'step-06-innovation', 'step-07-project-type', 'step-08-scoping', 'step-09-functional', 'step-10-nonfunctional', 'step-11-polish', 'step-12-complete']
inputDocuments:
  - '_bmad-output/brainstorming/brainstorming-session-2026-03-30-2100.md'
  - 'HANDOVER.md'
classification:
  projectType: web_app
  domain: general
  complexity: low-medium
  projectContext: brownfield
workflowType: 'prd'
---

# Product Requirements Document — A/B/U Interactive Book

**Author:** Alex
**Date:** 2026-03-31

## Executive Summary

A short interactive book teaching the A/B/U epistemological framework through a *metacognition-foregrounding medium*. Readers self-assess their epistemic state (A/B/U) at each section; the interface routes them forward on A/B, descends to simpler scaffolding on U, and surfaces a "stuck" notification when no scaffold exists. Authored by Alex — a recent A/B/U learner — exploiting the "curse of knowledge" inversion: a guide two steps ahead is more effective than an expert.

Target readers: (a) total newcomers to epistemology/metacognition; (b) ORI-adjacent people who've heard A/B/U but haven't internalised it. Branching onboarding sets the initialisation state based on prior familiarity (MGS-style).

Business model: free to read, pay-what-you-got at the end. Value-based pricing is enforced by the interface — a reader who clicked only U has a materially different reading path than one who clicked A and B. Signal-boosted by Defender (ORI head); profit-sharing with ORI. MVP audience: Defender and inner-circle collaborators (password-protected).

### What Makes This Special

1. **The medium is the lesson.** Practising A/B/U self-labelling while reading *about* A/B/U is the pedagogy — not an add-on.
2. **Spatial, animated epistemology.** Nine embedded 3D GIFs (Three.js, already built) make abstract belief mechanics visceral: crystallisation, rejection, and maladaptation are watched, not just described.
3. **Self-improving via reader analytics.** PostHog event logging maps A/B/U clicks per section — U-spikes identify missing scaffolding. Confused readers become content research.
4. **Adaptive entry paths.** Reader's prior knowledge state sets their starting point. The book reads your B-state before teaching you about B-states.
5. **Right-distance authorship.** Alex learned A/B/U recently; this is the advantage, not a liability.
6. **LLM scaffold layer.** An embedded LLM (RAG on book content, permitted novel metaphors) answers questions on U clicks — scalable tier-1 support that replaces Alex as first responder.

Analogous precedent: Quantum Country / Orbit (Nielsen & Matuschak) — mnemonic medium for quantum computing. This is the metacognition equivalent, with adaptive routing, LLM scaffolding, and interface-enforced value-based pricing that Quantum Country lacks.

### Project Classification

- **Project type:** Web app (static, browser-rendered, CDN-only — consistent with existing GIF architecture)
- **Domain:** General (educational/publishing; no regulatory compliance)
- **Complexity:** Low-Medium (one Netlify serverless function for LLM proxy; otherwise static)
- **Project context:** Brownfield — 9 standalone HTML GIF files are the core visual assets

## Success Criteria

### User Success

- Reader correctly applies A/B/U labels to their own epistemic states after engaging
- Reader achieves at least one A or B per session (self-reported via button)
- Reader submits text feedback and receives a reply — returns to the site as a result
- Reader returns across multiple sessions over months/years as value compounds

### Business Success

- **MVP:** Defender reads the draft site and says "amazing, this is going to be really successful, this is exactly what I wanted"
- **6–12 months:** First voluntary payments received; any payment = proof of felt value
- **3–5 years:** Repeat payments at increasing amounts as compounding value accrues; occasional large payments ($10k+) are within scope of the model
- Revenue instrument supports repeat payment — readers return to pay more as value is realised over time
- U-spikes in PostHog are wins (content research signals), not failures

### Technical Success

- All 9 GIFs embed and play correctly within the reading flow
- A/B/U buttons fire PostHog events with section ID and value
- Text feedback submits with section context included
- localStorage persists reader path and state across sessions
- Site renders correctly on mobile
- Password gate functional for MVP inner-circle access

### Measurable Outcomes

- Defender approval at MVP launch
- At least one text feedback exchange per 10 readers within 30 days of inner-circle launch
- PostHog dashboard shows interpretable A/B/U distribution per section within first week
- At least one section rewritten or lemma added based on U-spike data within 60 days

## User Journeys

### Journey 1: Maya — Curious Newcomer (Primary User, Success Path)

Maya is 28, works in UX research, reads metacognition books but finds most hand-wavy. She follows Defender on Substack and sees him share a link: "someone I trust wrote something short and honest about how minds actually work." She clicks.

Password gate, gets in. Onboarding: "Have you come across A/B/U before?" No. Newcomer path.

First section is short. A 3D animation — nodes crystallising, a belief snapping into place. Something clicks. She clicks **A**. The interface says "nice — keep going." She's charmed.

Three sections in, she hits something denser. Reads it twice. Clicks **U**. Descends to scaffold. Still murky. Opens LLM chat: "Can you explain this using something from everyday life?" The LLM offers the social trust circles metaphor. It clicks. She clicks **A** and continues.

She reaches end of available content. Pay-what-you-want prompt: "If this shifted something for you, pay what it's worth. No obligation. Come back and pay more later if it compounds." She pays $15.

Eight months later she's in a hard conversation with her manager and suddenly sees it — a U she's been bouncing. She returns and pays $200.

**Capabilities revealed:** password gate, onboarding branch, A/B/U routing, scaffold descent, LLM chat, text feedback escalation, end-of-content page, repeat payment, returning user state.

### Journey 2: Tom — ORI-Adjacent Reader (Primary User, Hits the Frontier)

Tom has been in ORI Discord for a year. Defender drops the link. Onboarding: "Have you encountered A/B/U before?" Yes. ORI-adjacent path — skips primer.

He moves fast. Lots of Bs. Section 5 is interesting — maladaptive B dynamics he hadn't considered. He clicks **A** three times. Leaves a text note: "The mal3 gif is the most useful thing I've seen about epistemic change in years."

Section 7 doesn't exist yet. Stub: *"We haven't scaffolded this level yet — content stops here for now. Thanks for getting this far."* Not frustrated — impressed by the honesty. Clicks "notify me." Pays $75 immediately.

**Capabilities revealed:** alternate onboarding path, stub pages, "notify me" email capture, early payment before content is finished.

### Journey 3: Alex — Author Responding to U-Spike (Admin User)

Two weeks post inner-circle launch. Alex opens PostHog. Section 4: 68% U-rate (everything else under 30%). Text feedback for that section: seven messages, all variations of "I don't understand what 'frontier' means."

He writes "What is a frontier?" — 200-word explainer, A-frontier GIF embedded. Drops Markdown file into repo. Updates `content.json` branching to route U clicks from section 4 to this lemma. Push to Netlify — live in 30 seconds.

Replies to seven feedback emails personally. Three return within a week. Section 4 U-rate drops to 22%.

**Capabilities revealed:** PostHog per-section analytics, per-section feedback inbox, Markdown authoring, branching config editable without touching core code, fast deploy.

### Journey 4: Defender — Inner-Circle Reviewer (Collaborator, MVP)

Alex shares password. Defender reads draft over an evening. Mostly A and B — a few Us where Alex over-assumed prior knowledge. Tries LLM chat on one U: it offers a reframe. Leaves three text notes.

Tells Alex: "This is exactly what I wanted. Ship it." MVP success criterion met.

A week later Defender posts on Substack. Password gate becomes waitlist form — soft public launch.

**Capabilities revealed:** password gate → waitlist transition, shareable access, author feedback notification, Defender signal-boost as distribution milestone.

### Journey Requirements Summary

| Capability | Journeys | Phase |
|---|---|---|
| Password gate + waitlist fallback | J1, J4 | MVP |
| Branching onboarding (newcomer / ORI-adjacent) | J1, J2 | MVP |
| A/B/U buttons with routing (advance / descend / stub) | J1, J2 | MVP |
| LLM chat (RAG on book content + poetic license) | J1, J4 | MVP |
| Text feedback form with section context (→ email) | J1, J3 | MVP |
| PostHog per-section event analytics | J3 | MVP |
| Graceful stubs + "notify me" email capture | J2 | MVP |
| Pay-what-you-want, repeat-payment capable | J1, J2 | MVP |
| Returning user state (localStorage) | J1 | MVP |
| Markdown authoring + fast deploy (no build step) | J3 | MVP |
| In-site reply interface (author replies visible) | J3 | Growth |
| Reading history across sessions | J1 | Growth |

## Innovation & Novel Patterns

### Detected Innovation Areas

**1. Metacognition-Foregrounding Medium (new category)**
Quantum Country / Orbit (Nielsen & Matuschak, 2019) embedded spaced repetition into a physics textbook. This product goes further: instead of *testing* recall, readers *label their own epistemic state* in real time. The labelling act is the pedagogical intervention — not the content. No published interactive book has used reader self-assessment as both navigation logic and learning mechanism simultaneously.

**2. Interface-Enforced Value-Based Pricing**
"Pay what you want" is not new (Radiohead, Humble Bundle). What is new: the *interface detects* what you got. A reader who clicked only U is routed to simpler scaffolding throughout — their path is materially different from one who clicked A and B. The payment prompt reflects a lived experience. The product makes honesty in payment the path of least resistance.

**3. LLM as Scaffold Layer, Not General Assistant**
Most LLM educational integrations are open-ended chat. This bounds the LLM by RAG on book content, triggers it specifically on U clicks, and permits novel metaphors within the A/B/U vocabulary. The LLM is a scaffold rung, not a search engine.

**4. U-Spike Analytics as Content Research**
PostHog A/B/U logging per section gives the author a content-gap heatmap. The feedback loop (U-spike → write lemma → U-rate drops) is a self-improving publishing model with no direct precedent.

### Market Context & Competitive Landscape

| Competitor | Gap |
|---|---|
| Quantum Country / Orbit | No adaptive routing, no LLM, no value-based pricing, no author feedback loop |
| Roam Research courses | Some interactivity; no epistemic state tracking |
| Standard pay-what-you-want | No interface enforcement of amount |
| Khan Academy / Khanmigo | General LLM tutoring; not bounded to a specific knowledge base or self-assessment model |

### Validation Approach

- **MVP:** Defender approves. PostHog data interpretable within one week of inner-circle launch.
- **Medium-term:** First voluntary payments validate felt value. Alex reviews first 20 LLM conversations for framework faithfulness.
- **Innovation-specific:** U-rate delta before/after lemma additions. LLM chat rate vs text-feedback escalation rate (proxy for scaffold effectiveness).

### Risk Mitigation

| Risk | Mitigation |
|---|---|
| LLM hallucinates A/B/U concepts | RAG on book content; system prompt forbids general-knowledge answers; Alex reviews early LLM conversations |
| Readers game U-button to avoid paying | Gaming only routes them to simpler content — acceptable |
| Content too sparse for LLM to answer well | LLM says "this section hasn't been written yet" honestly; stubs surface same message |
| Alex overwhelmed by feedback | LLM handles tier-1; text feedback is tier-2 only; Alex's time protected by default |

## Web App Technical Requirements

### Architecture Overview

Static single-page application — one HTML shell, JavaScript-driven content switching, no server-side rendering, no build step. Content is Markdown files rendered client-side. One Netlify serverless function proxies the Claude API. Architecture is intentionally consistent with the existing GIF files (CDN-only, standalone HTML).

**Browser support:** Chrome, Firefox, Safari, Edge (last 2 major versions). Three.js WebGL already excludes legacy browsers.

**Real-time:** None required. LLM chat is async fetch. PostHog and Formspree are fire-and-forget.

### Content Architecture

- Markdown files (one per section) rendered via `marked.js` (CDN)
- `content.json` maps each section ID to: title, GIF reference, A/B destination, U destination, stub flag
- `netlify/functions/chat.js` receives `{message, sectionId, conversationHistory}`, prepends book content as RAG context, calls Claude API, returns response; API key in Netlify environment variable
- `localStorage` stores current section ID, A/B/U history, and LLM conversation per session
- Push to GitHub → Netlify auto-deploys in ~30 seconds; no build command

### Responsive Design

- Mobile-first layout
- GIFs scale down with `max-width: 100%` from native 800×600
- Touch targets minimum 44×44px
- LLM chat drawer slides up from bottom on mobile

### Accessibility

- A/B/U buttons: `aria-label` attributes with plain-language descriptions
- GIFs: text caption/description beneath each (WebGL fallback + screen reader context)
- LLM chat: keyboard-accessible input
- Buttons use labels + icons, not colour alone

### SEO

- MVP: none (password-protected)
- Post-MVP: `<meta>` tags per section, Open Graph tags for Substack link previews

## Project Scoping & Phased Development

### MVP Strategy

**Approach:** Experience MVP — deliver the full coherent reading experience (branching onboarding + GIFs + A/B/U routing + LLM chat) to a small inner circle. Goal is qualitative validation from Defender, not revenue or scale.

**Resource:** Alex as sole developer/author, AI-assisted. Content authoring (5–6 sections) is the primary time constraint, not engineering. Each new section: ~30 minutes engineering, N hours writing.

### Phase 1: MVP Feature Set

| Capability | Rationale |
|---|---|
| Password gate | Inner-circle only until Defender approves |
| Branching onboarding (newcomer / ORI-adjacent) | Sets correct entry point; core A/B/U metaphor |
| 5–6 Markdown sections with embedded GIFs | The content is the product |
| A/B/U buttons with routing | Core mechanic — without this it's a static site |
| LLM chat (Netlify function, RAG) | Tier-1 scaffolding; replaces Alex as first responder |
| Text feedback form (Formspree, section context) | Tier-2 escalation; author research signal |
| PostHog A/B/U event analytics | Content-gap heatmap; self-improvement loop |
| Graceful stubs + "notify me" email | Honest frontier acknowledgement |
| Pay-what-you-want link (repeat-capable) | Gumroad or Stripe; essential for value-based model |
| localStorage session state | Persists reading position and A/B/U history |

### Phase 2: Growth (Post-Defender Approval)

- True multi-level adaptive branching (multiple scaffold depths per section)
- In-site reply interface (author replies visible on site)
- Reading history across sessions
- Public launch (password gate → waitlist → open)
- Authoring UI to add scaffold levels without editing JSON

### Phase 3: Expansion (6–18 months)

- Spaced repetition / Orbit-style revisit prompts
- Community layer (anonymised A/B/U patterns from other readers)
- Translated versions
- Embeddable widget for other A/B/U content

### Risks

| Risk | Mitigation |
|---|---|
| LLM quality with sparse early content | LLM honest when it can't answer; sparse = honest "not yet written" |
| Branching config complexity | Start flat linear + one scaffold level per section; add depth from U-spike data |
| Time-delayed value signal at launch | MVP success = Defender says "ship it"; payment is 6–12 month signal |
| Content scope creep | Ship 5 sections; LLM and stubs handle the frontier; write more from data |

## Functional Requirements

### Access & Authentication

- **FR1:** Reader can access the site using a shared password
- **FR2:** Unauthenticated visitors can join a waitlist when the site transitions to soft public launch
- **FR3:** Author can share access credentials with collaborators

### Reader Onboarding

- **FR4:** Reader can indicate their prior familiarity with A/B/U at the start of their reading session
- **FR5:** The system routes readers to different entry sections based on their onboarding response
- **FR6:** Reader can restart their onboarding path at any time

### Reading & Section Navigation

- **FR7:** Reader can view a section's text content and associated GIF animation
- **FR8:** Reader can advance to the next section by registering an A or B response
- **FR9:** Reader can register a U response to indicate a section did not land
- **FR10:** Reader can see their current position within the reading path
- **FR11:** Reader can return to a previously visited section
- **FR12:** The system preserves a reader's position and response history across browser sessions

### Adaptive Scaffolding

- **FR13:** The system presents a simpler scaffold section when a reader registers U
- **FR14:** Reader is shown a stub message when no scaffold exists below their current level
- **FR15:** Reader can request notification when stub content becomes available
- **FR16:** Reader can navigate back up to the main path from a scaffold section after registering A or B

### LLM Chat

- **FR17:** Reader can open a chat interface to ask questions about the content of their current section
- **FR18:** The system responds to reader questions drawing primarily from the book's content
- **FR19:** The system may use novel metaphors and analogies consistent with the A/B/U framework
- **FR20:** The system acknowledges when a question falls outside the available book content
- **FR21:** Reader can view their conversation history within a session
- **FR22:** Reader can escalate from LLM chat to direct author contact

### Reader Feedback

- **FR23:** Reader can submit freeform text feedback on any section
- **FR24:** Feedback submissions include the section identifier and the reader's A/B/U response history
- **FR25:** Author receives feedback submissions with full section context
- **FR26:** Reader can indicate they are persistently stuck and wish to speak with the author directly

### Analytics & Monitoring

- **FR27:** The system records each A/B/U button interaction with the associated section identifier
- **FR28:** The system records LLM chat session events by section
- **FR29:** The system records when readers reach stub pages
- **FR30:** Author can view A/B/U click distributions per section in an analytics dashboard

### Payment

- **FR31:** Reader can make a voluntary payment upon completing their reading path
- **FR32:** Reader can return to make additional payments at a later date
- **FR33:** Reader can pay any amount they choose, including zero
- **FR34:** The payment prompt is contextualised by the reader's reading experience

### Content Authoring

- **FR35:** Author can add a new section by creating a Markdown file and updating the section configuration
- **FR36:** Author can specify branching targets (A/B destination, U destination, stub flag) for each section in a configuration file
- **FR37:** Author can embed a GIF animation in any section
- **FR38:** The system renders Markdown content without a build step
- **FR39:** Author can publish new or updated content by pushing to the repository
- **FR40:** Author can create a scaffold section and associate it with a parent section

### Accessibility & Platform

- **FR41:** The system functions correctly on modern mobile and desktop browsers
- **FR42:** GIF animations include a text description for readers who cannot render them
- **FR43:** A/B/U interactive controls are operable via keyboard

## Non-Functional Requirements

### Performance

- **NFR1:** Each section (Markdown + GIF) loads and is readable within 3 seconds on a standard broadband connection
- **NFR2:** LLM chat responses begin streaming or appear within 5 seconds of submission
- **NFR3:** A/B/U button interactions complete without perceptible delay (<200ms)
- **NFR4:** GIF animations do not block or delay the display of section text

### Security

- **NFR5:** The Claude API key is stored exclusively in Netlify environment variables and never exposed to the browser
- **NFR6:** Password gate prevents access to all content for unauthenticated users during MVP
- **NFR7:** Payment processing is delegated entirely to a third-party provider (Gumroad or Stripe) — no payment data handled by the site
- **NFR8:** LLM chat input is sanitised before transmission to prevent prompt injection

### Scalability

- **NFR9:** The site sustains a traffic spike of up to 500 concurrent readers without degradation — Netlify CDN serves static assets; serverless LLM function scales automatically
- **NFR10:** Content architecture supports at least 50 sections and 3 scaffold levels per section without refactoring

### Accessibility

- **NFR11:** All interactive elements meet WCAG 2.1 Level AA for keyboard operability and screen reader compatibility
- **NFR12:** GIF animations comply with WCAG 2.3.1 (no content that flashes more than 3 times per second)
- **NFR13:** All text meets WCAG 2.1 AA colour contrast ratio (minimum 4.5:1)

### Integration

- **NFR14:** PostHog analytics events fire within 1 second of the triggering action; dropped events in offline scenarios are acceptable
- **NFR15:** Formspree feedback submissions deliver to the author's email within 5 minutes under normal conditions
- **NFR16:** The LLM serverless function returns a user-visible error message on Claude API downtime rather than hanging
- **NFR17:** Payment links open in a new tab, preserving the reader's session state in the original tab
