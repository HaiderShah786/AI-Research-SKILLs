---
name: frontend-design
description: Provides guidance for distinctive, intentional visual design when building new UI or reshaping an existing one. Use when working on aesthetic direction, typography, color palettes, and making deliberate choices that avoid templated defaults in web interfaces and artifacts.
version: 1.0.0
author: Orchestra Research
license: MIT
tags: [Frontend, UI Design, CSS, Typography, Design Systems, Web Artifacts]
---

# Frontend Design

Approach this as the design lead at a small studio known for giving every client a visual identity that could not be mistaken for anyone else's. This client has already rejected proposals that felt templated, and is paying for a distinctive point of view: make deliberate, opinionated choices about palette, typography, and layout that are specific to this brief, and take one real aesthetic risk you can justify.

## When to Use

Use this skill when:
- Building a new web UI, landing page, or interactive artifact from scratch
- Reshaping an existing design that feels generic or templated
- Choosing a color palette, typeface pair, or layout direction
- Adding motion, micro-interactions, or signature visual elements
- Writing UI copy and labels

## Ground It in the Subject

If the brief does not pin down what the product or subject is, pin it yourself before designing: name one concrete subject, its audience, and the page's single job, and state your choice. If there's any information in your memory about the human's preferences, context about what they're building, or designs you've made before — use that as a hint.

The subject's own world — its materials, instruments, artifacts, and vernacular — is where distinctive choices come from. Build with the brief's real content and subject matter throughout.

## Design Principles

### Hero as Thesis

Open with the most characteristic thing in the subject's world, in whatever form makes sense — a heading, an image, an animation, a live demo, an interactive moment.

A big number with a small label, supporting stats, and a gradient accent is the template answer; only use if that's truly the best option.

### Typography Carries Personality

Pair the display and body faces deliberately, not the same families you would reach for on any other project, and set a clear type scale with intentional weights, widths, and spacing. Make the type treatment itself a memorable part of the design, not a neutral delivery vehicle for the content.

**Recommended type scale (1.25 modular ratio)**:
```css
--text-xs:   0.64rem;  /* caption, label */
--text-sm:   0.8rem;
--text-base: 1rem;     /* body */
--text-lg:   1.25rem;  /* lead */
--text-xl:   1.563rem;
--text-2xl:  1.953rem; /* h3 */
--text-3xl:  2.441rem; /* h2 */
--text-4xl:  3.052rem; /* h1 */
--text-5xl:  3.815rem; /* display */
```

### Structure Is Information

Structural devices — numbering, eyebrows, dividers, labels — should encode something true about the content, not decorate it. Many generic designs use numbered markers (01 / 02 / 03), but that's only appropriate if the content is actually a sequence with order the reader needs.

### Motion Deliberately

Think about where and if animation can serve the subject: a page-load sequence, a scroll-triggered reveal, hover micro-interactions, ambient atmosphere. An orchestrated moment usually lands harder than scattered effects.

Sometimes less is more — extra animation contributes to the feeling that the design is AI-generated.

```css
/* Always wrap in prefers-reduced-motion */
@media (prefers-reduced-motion: no-preference) {
  .hero-entrance {
    animation: fadeSlideUp 0.6s cubic-bezier(0.16, 1, 0.3, 1) both;
  }
}

@keyframes fadeSlideUp {
  from { opacity: 0; transform: translateY(1.5rem); }
  to   { opacity: 1; transform: translateY(0); }
}
```

### Match Complexity to Vision

Maximalist directions need elaborate execution; minimal directions need precision in spacing, type, and detail. Elegance is executing the chosen vision well.

## Process: Brainstorm → Plan → Critique → Build → Critique Again

AI-generated design clusters around three defaults:
1. A warm cream background (~`#F4F1EA`) with a high-contrast serif display and a terracotta accent
2. A near-black background with a single bright acid-green or vermillion accent
3. A broadsheet-style layout with hairline rulers, zero border-radius, and dense newspaper-like columns

All three are legitimate for some briefs, but they appear regardless of subject. Where the brief pins down a visual direction, follow it exactly — the brief's own words always win. Where it leaves an axis free, don't spend that freedom on one of these defaults.

### Two-Pass Workflow

**Pass 1 — Brainstorm a compact token system**:

```
Color:     4–6 named hex values with roles
Type:      Display face (characterful, used with restraint)
           Body face (complementary)
           Utility face for captions/data (optional)
Layout:    One-sentence prose + ASCII wireframe
Signature: The single element this page will be remembered by
```

**Pass 2 — Critique before building**:
Work through a similar prompt and see if you arrive somewhere similar. If any part reads like the generic default — revise it, state what you changed and why. Only after confirming relative uniqueness should you write the code.

### Token System Example

```css
:root {
  /* Color — specific to the brief */
  --color-bg:      #0D1117;
  --color-surface: #161B22;
  --color-accent:  #F78166;
  --color-text:    #E6EDF3;
  --color-muted:   #8B949E;

  /* Type — paired deliberately */
  --font-display: "Playfair Display", Georgia, serif;
  --font-body:    "Inter", system-ui, sans-serif;
  --font-mono:    "JetBrains Mono", monospace;

  /* Spacing — consistent unit */
  --space-unit: 0.5rem;
}
```

### Signature Element Ideas

- Full-bleed generative SVG or canvas element as hero background
- A live interactive demo instead of a screenshot
- A single oversized typographic element that consumes most of the viewport
- A scroll-driven narrative where content appears as the reader moves
- A grid that breaks its own rules once, intentionally

## Restraint and Self-Critique

Spend your boldness in one place. Let the signature element be the one memorable thing; keep everything around it quiet and disciplined; cut any decoration that does not serve the brief.

Build to a quality floor without announcing it:
- Responsive down to mobile (test at 375px)
- Visible keyboard focus (`outline: 2px solid currentColor` on `:focus-visible`)
- Reduced motion respected
- WCAG AA contrast minimum: 4.5:1 for body text, 3:1 for large text

Consider Chanel's advice: before leaving the house, take a look in the mirror and remove one accessory. If removing a decoration doesn't break the design, remove it.

## Writing in Design

Words appear in a design for one reason: to make it easier to understand, and therefore easier to use. Bring the same intentionality to copy that you would bring to spacing and color.

**Voice rules**:
- Write from the end user's side of the screen
- Name things by what people control and recognize, never by how the system is built (a person manages notifications, not webhook config)
- Use active voice: "Save changes," not "Submit"
- Keep the register conversational — plain verbs, sentence case, no filler
- Treat failure and emptiness as moments for direction, not mood

**Consistency rule**: An action keeps the same name through the whole flow — the button that says "Publish" produces a toast that says "Published."

**Empty states**: An empty screen is an invitation to act. Explain what's next, don't just display nothing.

## Common Issues

**CSS specificity conflicts**: Be careful when generating CSS classes that cancel each other out — especially with type-based selectors (`.section`) combined with element-based selectors (`.cta`). This commonly causes padding/margin conflicts between sections.

```css
/* Dangerous: .section p and .cta both match <p class="cta"> inside .section */
.section p { padding: 1rem; }
.cta { padding: 0; }

/* Better: use a single class with BEM-style naming */
.section__body { padding: 1rem; }
.section__cta { padding: 0; }
```

**Templated hero**: If the hero feels interchangeable with another project, replace the signature element. Test by mentally swapping your palette into a completely different brief — if it still works, the choices weren't specific enough.

**Animation overload**: If every element has a micro-interaction, the page reads as AI-generated. Pick one choreographed moment; cut the rest.

**Copy defaults**: Generic copy ("Welcome to our platform", "Lorem ipsum") makes a design feel as templated as the layout itself. Write copy specific to the subject.

## Accessibility Checklist

- [ ] Color contrast AA (4.5:1 body, 3:1 large text) — test with browser DevTools
- [ ] All interactive elements keyboard-accessible
- [ ] Focus styles visible on `:focus-visible`
- [ ] `aria-label` on icon-only buttons
- [ ] Images have descriptive `alt` attributes
- [ ] `prefers-reduced-motion` media query wraps all animations
- [ ] Responsive from 375px to 1440px+

## References

**Design system architecture, token hierarchy, font loading, animation patterns**: See [references/README.md](references/README.md)
