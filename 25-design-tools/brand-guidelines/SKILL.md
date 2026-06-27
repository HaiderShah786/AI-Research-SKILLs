---
name: brand-guidelines
description: Applies Anthropic's official brand colors and typography to any artifact — slides, documents, HTML pages, reports, emails. Use when brand colors, style guidelines, visual formatting, or company design standards need to be applied to any output. Trigger when the user mentions Anthropic branding, brand colors, corporate identity, or wants any artifact to match Anthropic's look-and-feel, even if they just say "make it look official."
version: 1.0.0
author: Orchestra Research
license: MIT
tags: [Brand, Anthropic, Design Systems, Typography, Color, Corporate Identity, Style Guide]
---

# Brand Guidelines

Apply Anthropic's official brand identity to any artifact. Covers the complete token set: color palette, typography, shape styling, smart contrast selection, and application patterns for HTML, PPTX, and DOCX.

## Brand Tokens

### Color Palette

```css
/* Main palette */
--color-dark:       #141413;  /* Primary text, dark backgrounds */
--color-light:      #faf9f5;  /* Light backgrounds, text on dark — NOT pure white */
--color-mid-gray:   #b0aea5;  /* Secondary elements, dividers */
--color-light-gray: #e8e6dc;  /* Subtle backgrounds, borders */

/* Accent colors — cycle through in order: orange → blue → green */
--color-orange: #d97757;  /* Primary accent — CTAs, highlights */
--color-blue:   #6a9bcc;  /* Secondary accent */
--color-green:  #788c5d;  /* Tertiary accent */
```

**Important**: The background is `#faf9f5`, not `#ffffff`. Pure white is not on-brand.

### Typography

| Role | Font | Fallback | Apply when |
|------|------|----------|-----------|
| Headings | Poppins | Arial | 24pt and larger |
| Body text | Lora | Georgia | All body, captions, labels |
| Data / captions | Poppins Light | Arial | Optional — numeric data, small labels |

Fonts should be pre-installed in the environment. The skill falls back to Arial/Georgia automatically if custom fonts are unavailable.

## Smart Contrast Selection

Always select text color based on the background:

| Background | Use text color |
|-----------|---------------|
| `--color-light` (#faf9f5) | `--color-dark` (#141413) |
| `--color-light-gray` (#e8e6dc) | `--color-dark` (#141413) |
| `--color-dark` (#141413) | `--color-light` (#faf9f5) |
| `--color-orange` (#d97757) | `--color-dark` (#141413) |
| `--color-blue` (#6a9bcc) | `--color-dark` (#141413) |

**Contrast ratios** (pre-verified):
- `#141413` on `#faf9f5`: ~14:1 (AAA ✓)
- `#141413` on `#e8e6dc`: ~11:1 (AAA ✓)
- `#faf9f5` on `#141413`: ~14:1 (AAA ✓)

**Orange accent on light**: `#d97757` on `#faf9f5` = ~3.2:1 — passes AA only at 18pt+ or bold 14pt+. Use only for decorative and large text elements; never for small body text.

## Applying to HTML / CSS

```css
:root {
  --color-dark:       #141413;
  --color-light:      #faf9f5;
  --color-mid-gray:   #b0aea5;
  --color-light-gray: #e8e6dc;
  --color-orange:     #d97757;
  --color-blue:       #6a9bcc;
  --color-green:      #788c5d;
  --font-heading: "Poppins", Arial, sans-serif;
  --font-body:    "Lora", Georgia, serif;
}

body {
  background-color: var(--color-light);
  color: var(--color-dark);
  font-family: var(--font-body);
}

h1, h2, h3, h4, h5, h6 {
  font-family: var(--font-heading);
  color: var(--color-dark);
}

.accent  { color: var(--color-orange); }
.section-dark {
  background-color: var(--color-dark);
  color: var(--color-light);
}
.section-dark h1, .section-dark h2 {
  color: var(--color-light);
}
```

For web with Google Fonts (if Poppins/Lora not installed locally):
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600&family=Lora:ital,wght@0,400;0,600;1,400&display=swap" rel="stylesheet">
```

## Applying to PPTX (python-pptx)

```python
from pptx.dml.color import RGBColor
from pptx.util import Pt

DARK       = RGBColor(0x14, 0x14, 0x13)
LIGHT      = RGBColor(0xFA, 0xF9, 0xF5)
MID_GRAY   = RGBColor(0xB0, 0xAE, 0xA5)
LIGHT_GRAY = RGBColor(0xE8, 0xE6, 0xDC)
ORANGE     = RGBColor(0xD9, 0x77, 0x57)
BLUE       = RGBColor(0x6A, 0x9B, 0xCC)
GREEN      = RGBColor(0x78, 0x8C, 0x5D)

ACCENTS = [ORANGE, BLUE, GREEN]  # Cycle through in order

def brand_text_run(run, is_heading=False, on_dark_bg=False):
    run.font.name  = "Poppins" if is_heading else "Lora"
    run.font.color.rgb = LIGHT if on_dark_bg else DARK

def brand_shape_fill(shape, accent_index=0):
    shape.fill.solid()
    shape.fill.fore_color.rgb = ACCENTS[accent_index % 3]
```

## Applying to DOCX (python-docx)

```python
from docx.shared import RGBColor, Pt

def apply_brand_to_doc(doc):
    # Heading styles
    for heading_style in ["Heading 1", "Heading 2", "Heading 3"]:
        s = doc.styles[heading_style]
        s.font.name  = "Poppins"
        s.font.color.rgb = RGBColor(0x14, 0x14, 0x13)

    # Body style
    s = doc.styles["Normal"]
    s.font.name  = "Lora"
    s.font.color.rgb = RGBColor(0x14, 0x14, 0x13)

    # Page background (via document settings — optional)
    # doc.settings.element.background.color = "FAF9F5"
```

## Shape and Accent Application

Non-text shapes (icons, dividers, highlight boxes, callouts) use accent colors cycling through **orange → blue → green**:

```python
# Cycling pattern
for i, shape in enumerate(accent_shapes):
    brand_shape_fill(shape, accent_index=i)
```

Maintain visual interest while staying on-brand. Avoid using the same accent color on every shape — the cycle creates rhythm.

## Application Checklist

- [ ] Background set to `#faf9f5` (not `#ffffff`)
- [ ] Dark sections use `#141413` background + `#faf9f5` text
- [ ] Poppins applied to all headings ≥24pt
- [ ] Lora applied to all body text, captions, labels
- [ ] Arial/Georgia fallbacks specified everywhere
- [ ] Text color adapts to background (dark-on-light, light-on-dark)
- [ ] Orange accent only on large text (18pt+) or decorative elements
- [ ] Accent colors cycling orange → blue → green on shapes
- [ ] Contrast verified: all text/background pairs ≥4.5:1 body, ≥3:1 large text

## Common Issues

**Font not rendering**: Poppins and Lora must be pre-installed, or loaded via Google Fonts in HTML. In non-HTML environments, always verify font availability before applying.

**Orange text fails contrast**: Use orange only for large text (18pt+, bold 14pt+) and decorative elements. For small body text accents, use `--color-dark` with a background tinted by the accent.

**Background looks wrong**: Double-check the hex — `#faf9f5` (off-white with a warm tint) is required. Pure white `#ffffff` is not the Anthropic brand.

**Inconsistent look across pages/slides**: Apply brand tokens to the master template or base stylesheet, not just individual elements — changes to a single slide won't propagate to new content.

**Shapes look flat**: Use the accent color cycle (orange → blue → green) rather than a single accent throughout. This creates the visual rhythm characteristic of Anthropic materials.

## Brand in Practice — Layout Patterns

### Light Section (default)

```
Background: #faf9f5
Heading:    Poppins, #141413
Body:       Lora, #141413
Accent:     #d97757 (decorative / large text only)
Border:     #e8e6dc
```

### Dark Section (hero, CTA, footer)

```
Background: #141413
Heading:    Poppins, #faf9f5
Body:       Lora, #faf9f5
Accent:     #d97757 (large text), #6a9bcc (secondary)
Border:     #b0aea5
```

### Accent Card

```
Background: #d97757 or #6a9bcc or #788c5d
Text:       #141413 (on orange/green), #141413 (on blue — check contrast)
Heading:    Poppins bold
```

## Accessibility Checklist

- [ ] Body text (`#141413` on `#faf9f5`): ~14:1 — AAA ✓
- [ ] Reverse body (`#faf9f5` on `#141413`): ~14:1 — AAA ✓
- [ ] Orange accent on light bg: check — passes AA only at 18pt+ or bold 14pt+
- [ ] Blue accent on light bg: `#6a9bcc` on `#faf9f5` ≈ 3.1:1 — use only for large/bold text
- [ ] Green accent on light bg: `#788c5d` on `#faf9f5` ≈ 3.4:1 — use only for large/bold text
- [ ] Focus styles visible — `outline: 2px solid #d97757` on `:focus-visible`
- [ ] `prefers-reduced-motion` respected if any animations present
- [ ] All images have `alt` attributes; decorative images use `alt=""`

## Quick-Reference: Hex Values

| Token | Hex | RGB |
|-------|-----|-----|
| Dark | `#141413` | 20, 20, 19 |
| Light | `#faf9f5` | 250, 249, 245 |
| Mid Gray | `#b0aea5` | 176, 174, 165 |
| Light Gray | `#e8e6dc` | 232, 230, 220 |
| Orange | `#d97757` | 217, 119, 87 |
| Blue | `#6a9bcc` | 106, 155, 204 |
| Green | `#788c5d` | 120, 140, 93 |

## References

**Extended component-level specs, email templates, and accessibility guidance**: See [references/README.md](references/README.md)
