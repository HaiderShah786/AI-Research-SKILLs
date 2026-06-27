---
name: theme-factory
description: Provides a toolkit for styling artifacts with professional font and color themes. Use when applying consistent visual styling to slides, docs, reports, HTML landing pages, or any artifact — offers 10 preset themes or generates a custom theme on-the-fly. Trigger when the user asks to style, theme, rebrand, apply a color scheme, or apply a visual identity to any artifact, even if they don't use the word "theme."
version: 1.0.0
author: Orchestra Research
license: MIT
tags: [Themes, Design Systems, Typography, Color Palettes, Slides, CSS, Branding]
---

# Theme Factory

Apply consistent, professional styling to any artifact — presentations, documents, reports, HTML pages. Offers 10 pre-built themes or generates custom themes on demand.

## Workflow

1. **Show the theme showcase**: Display `theme-showcase.pdf` for the user to see all available themes visually. Do not modify it — show for viewing only.
2. **Ask for choice**: Ask which theme to apply to the artifact.
3. **Wait for selection**: Get explicit confirmation before making any changes.
4. **Apply the theme**: Read the corresponding file from `themes/` and apply colors + fonts consistently throughout.

## 10 Available Themes

| # | Name | Character | Best for |
|---|------|-----------|---------|
| 1 | **Ocean Depths** | Professional, calming maritime | Corporate, finance |
| 2 | **Sunset Boulevard** | Warm, vibrant sunset | Creative, marketing |
| 3 | **Forest Canopy** | Natural, grounded earth tones | Sustainability, wellness |
| 4 | **Modern Minimalist** | Clean, contemporary grayscale | Tech, SaaS |
| 5 | **Golden Hour** | Rich, warm autumnal | Premium, hospitality |
| 6 | **Arctic Frost** | Cool, crisp winter | Healthcare, science |
| 7 | **Desert Rose** | Soft, sophisticated dusty | Fashion, beauty |
| 8 | **Tech Innovation** | Bold, modern tech | Startups, AI |
| 9 | **Botanical Garden** | Fresh, organic garden | Education, nonprofits |
| 10 | **Midnight Galaxy** | Dramatic, cosmic deep | Events, entertainment |

Each theme is fully defined in `themes/` with hex color values, font pairings, and visual identity guidance.

## Applying a Theme

After selection:

1. Read the corresponding theme file from `themes/<theme-name>.md` (or similar)
2. Extract the full token set: background, surface, primary, accent, text, muted colors + heading and body fonts
3. Apply the specified colors and fonts **consistently throughout the artifact** — every element, not just the prominent ones
4. Ensure proper contrast and readability at all text sizes
5. Maintain the theme's visual identity across all components

**Application checklist**:
- [ ] Primary background and surface colors applied everywhere
- [ ] Heading font and weight set on all headings
- [ ] Body font set on all body text, captions, labels
- [ ] Accent color applied to interactive elements, highlights, CTAs
- [ ] Muted color applied to secondary text and captions
- [ ] All text passes contrast requirements (4.5:1 for body, 3:1 for large text)
- [ ] No leftover default styles (e.g., Times New Roman, pure black `#000000` on pure white `#ffffff`)
- [ ] Table headers, footnotes, tooltips, and metadata text also themed

## Applying to Common Artifact Types

### HTML / CSS

```css
:root {
  /* Replace with actual values from the selected theme file */
  --color-bg:      #F8F9FA;
  --color-surface: #FFFFFF;
  --color-primary: #1A3A5C;
  --color-accent:  #2E86AB;
  --color-text:    #212529;
  --color-muted:   #6C757D;
  --font-heading:  "Georgia", serif;
  --font-body:     "Inter", system-ui, sans-serif;
}

body        { background: var(--color-bg); color: var(--color-text); font-family: var(--font-body); }
h1, h2, h3  { font-family: var(--font-heading); color: var(--color-primary); }
.accent     { color: var(--color-accent); }
.muted      { color: var(--color-muted); }
```

### PPTX (python-pptx)

```python
from pptx.dml.color import RGBColor
from pptx.util import Pt

def hex_to_rgb(hex_str):
    h = hex_str.lstrip('#')
    return RGBColor(int(h[0:2], 16), int(h[2:4], 16), int(h[4:6], 16))

# Apply to all runs in a presentation
for slide in pres.slides:
    for shape in slide.shapes:
        if shape.has_text_frame:
            for para in shape.text_frame.paragraphs:
                for run in para.runs:
                    is_heading = run.font.size and run.font.size >= Pt(20)
                    run.font.name  = theme["heading_font"] if is_heading else theme["body_font"]
                    run.font.color.rgb = hex_to_rgb(theme["text"])
```

### DOCX (python-docx)

```python
from docx.shared import RGBColor, Pt

def apply_theme_to_doc(doc, theme):
    for style_name in ["Heading 1", "Heading 2", "Heading 3"]:
        style = doc.styles[style_name]
        style.font.name = theme["heading_font"]
        style.font.color.rgb = RGBColor.from_string(theme["primary"].lstrip("#"))

    body_style = doc.styles["Normal"]
    body_style.font.name = theme["body_font"]
    body_style.font.color.rgb = RGBColor.from_string(theme["text"].lstrip("#"))
```

## Creating a Custom Theme

When none of the 10 presets fit:

1. Ask the user for a brief description (mood, industry, audience, any color preferences)
2. Generate a theme following this structure:

```yaml
name: Theme Name
description: One-sentence character description
palette:
  background: "#HEX"   # page/slide background
  surface:    "#HEX"   # cards, panels
  primary:    "#HEX"   # dominant color — headers, nav
  accent:     "#HEX"   # CTAs, highlights, links
  text:       "#HEX"   # primary body text
  muted:      "#HEX"   # secondary text, captions, placeholders
typography:
  heading: "Font Name, fallback, generic"
  body:    "Font Name, fallback, generic"
  mono:    "Font Name, monospace"     # optional — for code/data
  scale:
    h1: "2.5rem bold"
    h2: "2rem semibold"
    h3: "1.5rem semibold"
    body: "1rem regular"
    small: "0.875rem regular"
```

3. Show the generated token values for review before applying
4. After user confirmation, apply as with a preset theme

**Naming guide**: Name custom themes after their visual character, not the client name: "Coastal Startup", "Legal Precision", "Warm Clinical", "Dark Academic".

## Common Issues

**Theme looks inconsistent**: Check that ALL text elements have been updated — captions, table headers, footnotes, and tooltips are commonly missed.

**Poor contrast after theming**: Check accent on primary background first — it's the most common failure. Shift lightness (not hue) until passing. Tools: browser DevTools color contrast checker, or Python's `wcag-contrast-ratio` library.

**Font not available in HTML**: Always specify a web-safe fallback in the font stack. For HTML artifacts offline, embed fonts as base64 data URIs.

**Slides look unthemed in new slides added after theming**: In `.pptx`, formatting must be applied to the slide master AND individual slide layouts — applying only to slides leaves the master unstyled, causing resets when new slides are added.

**Custom theme colors clash**: Run the full palette through a contrast matrix (all text colors against all background colors) before confirming. Minimum 4.5:1 for body, 3:1 for large text/UI elements.

## Contrast Verification

Always verify contrast before delivering. Minimum ratios:
- Body text: **4.5:1** (WCAG AA)
- Large text (18pt+, or bold 14pt+): **3:1** (WCAG AA)
- UI components and icons: **3:1**

Quick Python check:
```python
def contrast_ratio(hex1, hex2):
    def luminance(hex_color):
        r, g, b = (int(hex_color[i:i+2], 16) / 255 for i in (1, 3, 5))
        def linearize(c):
            return c / 12.92 if c <= 0.04045 else ((c + 0.055) / 1.055) ** 2.4
        return 0.2126 * linearize(r) + 0.7152 * linearize(g) + 0.0722 * linearize(b)
    l1, l2 = sorted([luminance(hex1), luminance(hex2)], reverse=True)
    return (l1 + 0.05) / (l2 + 0.05)

# Example
print(contrast_ratio("#1A3A5C", "#F8F9FA"))  # Should be ≥ 4.5 for body text
```

## Font Loading for HTML Artifacts

When applying a theme to an HTML artifact that may run offline:

```html
<!-- Option 1: Google Fonts (requires internet) -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Georgia&family=Inter:wght@400;600&display=swap" rel="stylesheet">

<!-- Option 2: System font stack (always available) -->
<style>
:root {
  --font-heading: "Georgia", "Times New Roman", serif;
  --font-body:    "Arial", "Helvetica Neue", system-ui, sans-serif;
}
</style>
```

For fully offline artifacts, embed fonts as base64 data URIs in the CSS.

## Theme Application for PPTX Slide Masters

To ensure new slides inherit the theme (not just existing slides):

```python
from pptx import Presentation
from pptx.dml.color import RGBColor

prs = Presentation("input.pptx")
theme_primary = RGBColor(0x1A, 0x3A, 0x5C)  # Replace with selected theme value

# Apply to slide master (propagates to new slides)
for layout in prs.slide_master.slide_layouts:
    for ph in layout.placeholders:
        if ph.has_text_frame:
            for para in ph.text_frame.paragraphs:
                for run in para.runs:
                    run.font.color.rgb = theme_primary
```

## References

**Full color token specifications for all 10 preset themes**: See [references/README.md](references/README.md)
