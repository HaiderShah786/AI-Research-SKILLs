---
name: web-design-guidelines
description: Reviews UI code for Web Interface Guidelines compliance. Use when asked to "review my UI", "check accessibility", "audit design", "review UX", "check my site against best practices", or any time UI code needs a quality pass. Covers accessibility, focus states, forms, animation, typography, performance, dark mode, i18n, and a full list of anti-patterns to flag. Pass a file path or glob pattern as the argument.
version: 1.0.0
author: Orchestra Research
license: MIT
tags: [Web Design, Accessibility, UX, Code Review, WCAG, Frontend, React, Performance]
---

# Web Design Guidelines

Review UI files for compliance with Web Interface Guidelines. Produces terse `file:line` findings for every violation found.

## Usage

```
Argument: <file-or-pattern>   e.g.  src/components/Button.tsx  or  src/**/*.tsx
```

1. Fetch the latest live guidelines (see below)
2. Read the specified files (or ask the user which files to review if none provided)
3. Check every rule from the fetched guidelines
4. Output findings in `file:line — rule description` format

## Guidelines Source

Always fetch fresh guidelines before each review — rules evolve:

```
https://raw.githubusercontent.com/vercel-labs/web-interface-guidelines/main/command.md
```

Use `WebFetch` to retrieve the latest rules. The fetched content is authoritative and supersedes the summary below.

## Current Rules (Canonical Reference)

### Accessibility

- Icon-only buttons need `aria-label`
- Form controls need `<label>` or `aria-label`
- Interactive elements need keyboard handlers (`onKeyDown`/`onKeyUp`)
- `<button>` for actions, `<a>`/`<Link>` for navigation — never `<div onClick>`
- Images need `alt` (or `alt=""` if decorative)
- Decorative icons need `aria-hidden="true"`
- Async updates (toasts, validation) need `aria-live="polite"`
- Use semantic HTML before ARIA
- Headings hierarchical `<h1>`–`<h6>`; include skip link
- `scroll-margin-top` on heading anchors

### Focus States

- Interactive elements need visible focus: `focus-visible:ring-*`
- Never `outline-none` without a focus replacement
- Use `:focus-visible` over `:focus`
- Group focus with `:focus-within`

### Forms

- Inputs need `autocomplete` and meaningful `name`
- Use correct `type` and `inputmode`
- Never block paste (`onPaste` with `preventDefault`)
- Labels must be clickable
- Disable spellcheck on emails/codes/usernames
- Checkboxes/radios: no dead zones around the control
- Submit button enabled until request starts
- Errors inline; focus first error field
- Placeholders end with `…` not `...`
- `autocomplete="off"` on non-auth fields
- Warn before navigation with unsaved changes

### Animation

- Honor `prefers-reduced-motion`
- Animate `transform`/`opacity` only
- Never `transition: all`
- Set correct `transform-origin`
- Animations must be interruptible

### Typography

- `…` not `...` (ellipsis character, not three dots)
- Curly quotes and non-breaking spaces
- Loading states: `"Loading…"` (not `"Loading..."`)
- `font-variant-numeric: tabular-nums` for numbers that change
- `text-wrap: balance` on headings

### Content Handling

- Text containers handle long content without overflow
- Flex children need `min-w-0` to prevent overflow
- Handle empty states explicitly — never show nothing
- Anticipate varying input lengths in all layouts

### Images

- `<img>` needs explicit `width` and `height`
- Below-fold images: `loading="lazy"`
- Critical above-fold images: `priority`

### Performance

- Lists > 50 items: virtualize
- No layout reads in render (avoid `getBoundingClientRect` in render)
- Batch DOM reads/writes
- Prefer uncontrolled inputs where possible
- Preconnect to CDNs
- Critical fonts: preload with `font-display: swap`

### Navigation & State

- URL reflects current state (deep-linkable)
- Use `<a>`/`<Link>` for navigation — never `onClick` for page changes
- Deep-link stateful UI
- Destructive actions need confirmation dialogs

### Touch & Interaction

- `touch-action: manipulation` on interactive elements
- Set `-webkit-tap-highlight-color` on custom interactives
- `overscroll-behavior: contain` in modals/drawers
- Disable text selection during drag operations
- Limit `autoFocus` — use intentionally, not by default

### Safe Areas & Layout

- Full-bleed elements: use `env(safe-area-inset-*)`
- Manage scrollbar gutter to avoid layout shift
- Prefer flex/grid over JS-based layout

### Dark Mode

- `color-scheme: dark` on `<html>` when dark mode active
- `<meta name="theme-color">` updated for dark mode
- Native `<select>`: provide explicit colors (not inherited)

### Locale & i18n

- Use `Intl.DateTimeFormat` and `Intl.NumberFormat` — never hardcode formats
- Detect language via header or API
- Wrap identifiers (brand names, codes) with `translate="no"`

### Hydration Safety

- Inputs with `value` need `onChange` (controlled pattern)
- Guard date/time rendering against SSR/client mismatch
- Minimize `suppressHydrationWarning` usage

### Hover & Interactive States

- All buttons and links need `hover:` states
- Increase contrast on hover/active interaction

### Content & Copy

- Active voice
- Title Case for page headings
- Numerals for counts (7, not "seven")
- Specific button labels ("Save changes", not "Submit")
- Error messages include a solution, not just a description
- Second person ("your", not "the user's")
- `&` only in space-constrained UI, not in prose

## Anti-Patterns to Flag

Flag these immediately — always violations:

| Anti-pattern | Why |
|---|---|
| `viewport` with `user-scalable=no` or `maximum-scale=1` | Blocks zoom — accessibility violation |
| `onPaste` with `e.preventDefault()` | Blocks paste — never acceptable |
| `transition: all` | Catches unintended properties, causes jank |
| `outline-none` or `outline: 0` without replacement | Removes focus visibility for keyboard users |
| Inline `onClick` navigation (not `<a>`/`<Link>`) | Breaks browser history, right-click, cmd+click |
| `<div onClick>` or `<span onClick>` for interactive elements | Not keyboard accessible, no semantics |
| `<img>` without `width`/`height` | Causes layout shift (CLS) |
| Unvirtualized list rendering > 50 items | Performance — blocks main thread |
| Form inputs without `<label>` or `aria-label` | Accessibility violation |
| Icon-only buttons without `aria-label` | Screen readers get no context |
| Hardcoded date/number formats | Breaks i18n |
| `autoFocus` without justification | Hijacks keyboard context unexpectedly |

## Output Format

```
src/components/Button.tsx:14 — Icon-only button missing aria-label
src/components/Form.tsx:32 — outline-none without focus replacement
src/pages/index.tsx:88 — transition: all detected; animate only transform/opacity
src/components/List.tsx:12 — 200-item list not virtualized
```

One finding per line: `file:line — brief description of violation`. Group by file. No preamble, no summary paragraph — just findings. If no violations, output: `✓ No violations found.`

## Workflow

```
1. WebFetch the guidelines source URL (fresh rules)
2. Read target files
3. Walk through every rule category
4. Emit findings in file:line format
5. Done — no follow-up prose
```

## Common Issues

**No files specified**: Ask the user: "Which files or pattern should I review? (e.g., `src/components/**/*.tsx`)"

**Large codebase**: Focus on files most likely to contain violations — interactive components, forms, lists, and layout wrappers. Note any files skipped due to size.

**Fetching guidelines fails**: Fall back to the canonical rule list in this SKILL.md. Note at the top of findings: "Using cached guidelines — live fetch failed."

## References

**Latest live rules**: `https://raw.githubusercontent.com/vercel-labs/web-interface-guidelines/main/command.md`

**Extended rule rationale and examples**: See [references/README.md](references/README.md)
