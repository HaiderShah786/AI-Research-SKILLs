# Frontend Design — Extended Reference

## Design System Architecture

### Token Hierarchy
```
Primitive tokens → Semantic tokens → Component tokens
#0D1117          → color.background → card.background
```

### Responsive Breakpoints
| Breakpoint | Width | Use |
|---|---|---|
| xs | < 480px | Mobile portrait |
| sm | 480–767px | Mobile landscape |
| md | 768–1023px | Tablet |
| lg | 1024–1279px | Desktop |
| xl | 1280px+ | Wide desktop |

## Accessibility Standards

- WCAG AA minimum: 4.5:1 contrast ratio for body text, 3:1 for large text
- Keyboard focus visible at all times (`outline: 2px solid currentColor`)
- `prefers-reduced-motion`: wrap animations in `@media (prefers-reduced-motion: no-preference)`
- `prefers-color-scheme`: provide dark/light variants

## Typography Scale (Modular — 1.25 ratio)

| Step | Size | Use |
|---|---|---|
| -1 | 0.8rem | Caption, label |
| 0 | 1rem | Body |
| 1 | 1.25rem | Lead |
| 2 | 1.563rem | H4 |
| 3 | 1.953rem | H3 |
| 4 | 2.441rem | H2 |
| 5 | 3.052rem | H1 |
| 6 | 3.815rem | Display |

## CSS Architecture Patterns

### Custom Properties for Theming
```css
:root {
  --color-bg: #0d1117;
  --color-surface: #161b22;
  --color-accent: #f78166;
  --color-text: #e6edf3;
  --font-display: "Playfair Display", Georgia, serif;
  --font-body: "Inter", system-ui, sans-serif;
  --space-unit: 0.5rem;
}
```

### Avoiding Specificity Conflicts
- Prefer class selectors over type selectors for component styles
- Use BEM or CSS Modules to scope styles
- Avoid `.section p` patterns when `.section` and `.cta` both apply to `<p>` elements

## Web Font Loading

```html
<!-- Preconnect to font origin -->
<link rel="preconnect" href="https://fonts.googleapis.com">

<!-- Load with font-display: swap to avoid FOUT -->
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap" rel="stylesheet">
```

## Animation Guidelines

```css
/* Respect reduced motion preference */
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
