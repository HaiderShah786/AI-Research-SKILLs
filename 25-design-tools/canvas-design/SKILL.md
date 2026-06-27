---
name: canvas-design
description: Provides guidance for creating beautiful visual art as .png and .pdf outputs using a two-pass design philosophy process. Use when the user asks to create a poster, piece of art, design, illustration, or other static visual piece — outputs only .md, .pdf, and .png files. Never copies existing artists' work.
version: 1.0.0
author: Orchestra Research
license: MIT
tags: [Canvas, Visual Art, Design Philosophy, PDF, PNG, Typography, Generative Art]
---

# Canvas Design

Create original visual designs through a two-pass process: first establish a design philosophy, then express it as a visual artifact on canvas. Never copy existing artists' work to avoid copyright violations.

## When to Use

- User asks for a poster, piece of art, illustration, or static visual
- Output will be a `.png`, `.pdf`, or `.md` file
- Task is visual-first (90% design, 10% text)
- User wants something that looks like museum or magazine quality work

## Two-Pass Process

### Pass 1 — Design Philosophy (.md file)

Create a **visual philosophy** — an aesthetic movement — not a layout or template. The philosophy will be interpreted through form, space, color, and composition. It is NOT a description of what the piece will look like; it is a manifesto for an art movement.

**Name the movement** (1–2 words): e.g., "Brutalist Joy", "Chromatic Silence", "Metabolist Dreams"

**Write 4–6 concise paragraphs** that collectively cover:
- Space and form — how emptiness and mass relate
- Color and material — palette character and surface quality
- Scale and rhythm — how sizes and repetition interact
- Composition and balance — visual weight distribution
- Visual hierarchy — where the eye travels and why

**Critical philosophy guidelines**:

- Each design aspect mentioned once — no redundancy, no repeating the same principle in different words
- Emphasize craftsmanship repeatedly throughout: "meticulously crafted," "painstaking attention," "master-level execution," "the product of countless hours by someone at the top of their field" — this framing is essential and must appear multiple times
- Leave creative space — be specific about aesthetic direction, but concise enough that the next pass can make high-quality interpretive choices
- Philosophy should be generic enough to use for any piece in this aesthetic, not narrowly tied to this one brief
- Text is always sparse and essential-only — the philosophy must say that information lives in design, not paragraphs

**Philosophy examples**:

*"Concrete Poetry"* — Communication through monumental form and bold geometry. Visual expression: massive color blocks, sculptural typography (huge single words, tiny labels), Brutalist spatial divisions, Polish poster energy meets Le Corbusier. Ideas expressed through visual weight and spatial tension, not explanation. Text as a rare, powerful gesture. Every element placed with the precision of a master craftsman who has labored over every millimeter.

*"Chromatic Language"* — Color as the primary information system. Geometric precision where color zones create meaning. Typography minimal — small sans-serif labels letting chromatic fields communicate. Think Josef Albers' interaction meets data visualization. Information encoded spatially and chromatically. Words only to anchor what color already shows. The result of painstaking chromatic calibration by an expert colorist.

*"Analog Meditation"* — Quiet visual contemplation through texture and breathing room. Paper grain, ink bleeds, vast negative space. Photography and illustration dominate. Typography whispered (small, restrained, serving the visual). Japanese photobook aesthetic. Images breathe across the composition. Each element balanced with the care of a meditation practice and countless hours of refinement.

*"Organic Systems"* — Natural clustering and modular growth patterns. Rounded forms, organic arrangements, color from nature through architecture. Information shown through visual diagrams, spatial relationships, iconography. Text only for key labels floating in space. The composition tells the story through expert spatial orchestration.

Output the philosophy as a `.md` file before proceeding.

### Intermediate — Deduce the Subtle Reference

**Critical step before canvas creation**: Identify the subtle conceptual thread from the original request.

The topic is a **subtle, niche reference embedded within the art itself** — not always literal, always sophisticated. Someone familiar with the subject should feel it intuitively while others simply experience a masterful abstract composition. The design philosophy provides the aesthetic language; the deduced topic provides the soul — the quiet conceptual DNA woven invisibly into form, color, and composition.

Think like a jazz musician quoting another song: only those who know will catch it, but everyone appreciates the music. This reference must be refined so it enhances depth without announcing itself.

### Pass 2 — Canvas Creation (.pdf or .png)

With philosophy and conceptual framework established, express it on canvas. Take a moment to gather thoughts and clear the mind before beginning.

**Approach**:
- One single page, highly visual, design-forward output (unless more pages requested)
- Use repeating patterns and precise shapes — generally lean on geometric precision
- Treat the abstract philosophy like a scientific bible: dense accumulation of marks, repeated elements, layered patterns that build meaning through patient repetition and reward sustained viewing
- Add sparse, clinical typography — as if this could be a diagram from an imaginary discipline, treating the invisible subject with the reverence of documenting observable phenomena
- Anchor with simple phrase(s) positioned subtly; never explanatory paragraphs
- Limited color palette (4–6 colors max) that feels intentional and cohesive
- Embrace the paradox: analytical visual language expressing ideas about human experience

**Typography rules**:
- Text is always minimal and visual-first
- Let context guide scale — a punk venue poster may have aggressive type; a ceramics studio identity whispers
- Most fonts should be thin
- **Search the `./canvas-fonts` directory** for available fonts — do not use system defaults alone
- Use different fonts when writing text; typography must feel like a design choice
- Typography must be part of the art itself — not typeset digitally and laid on top
- Nothing falls off the page; nothing overlaps; every element has breathing room with proper margins — non-negotiable for professional execution
- Check that all text, graphics, and visual elements are contained within canvas boundaries

**Font usage**:
```bash
# Check available canvas fonts
ls ./canvas-fonts/
```

**Quality standard**: The work must look like it took countless hours by someone at the absolute top of their field. Every detail — composition, spacing, color choices, typography — must demonstrate expert craftsmanship. If shown to people to prove expertise, it must rank as undeniably impressive and not AI-generated.

## Refinement Pass (Always Required)

After initial creation, treat this as already received from the user:

> "It isn't perfect enough. It must be pristine, a masterpiece of craftsmanship, as if it were about to be displayed in a museum."

**How to refine**:
- Avoid adding more graphics — refine and tighten what exists
- Ask: "How can I make what's already here more of a piece of art?"
- If the instinct is to draw a new shape, STOP — instead refine spacing, color, and composition
- Focus on crispness, coherence with the philosophy, and minimalism
- Remove any element that doesn't directly serve the philosophy

## Multi-Page Option

When additional pages are requested:
- Create distinctly different pages that share the design philosophy
- Bundle in the same `.pdf` or as multiple `.pngs`
- Treat the first page as a single page in a coffee table book waiting to be filled
- Subsequent pages: unique twists and memories of the original, almost telling a story
- Exercise full creative freedom — distinct yet connected through the philosophy

## Common Issues

**Looks AI-generated**: Reduce scattered effects to one orchestrated moment. Remove any decoration not serving the brief. Tighten spacing and alignment until every placement reads as intentional. Ask whether each element is a choice or a default.

**Text overlapping or off-canvas**: Calculate all text bounds explicitly before finalizing. Use explicit coordinate calculations — never estimate. Add margins of at least 5% on each edge. Test at multiple canvas sizes.

**Generic color palette**: Replace the palette before finalizing. Test by mentally swapping it into an unrelated brief — if it still fits, the choices aren't specific enough. Each color should be defensible as belonging to this brief specifically.

**Philosophy too prescriptive**: A good philosophy leaves room for interpretation. If it sounds like a layout spec ("put a large image on the left, text on the right"), revise it to be about visual character instead.

**Work feels decorated, not designed**: Strip the piece down to its essential geometry and ask whether it communicates the philosophy. Add elements back only if they strengthen the core idea.

## Output Files

- Design philosophy: one `.md` file
- Final artwork: single `.pdf` or `.png` (multiple files for multi-page requests)
- Both files delivered together

## Canvas Rendering Reference

### Python (Pillow / PIL)

```python
from PIL import Image, ImageDraw, ImageFont
import os

# Create canvas
WIDTH, HEIGHT = 2480, 3508  # A4 at 300 DPI
canvas = Image.new("RGB", (WIDTH, HEIGHT), "#faf9f5")
draw   = ImageDraw.Draw(canvas)

# Load a canvas font
font_path = os.path.join("canvas-fonts", "chosen-font.ttf")
font_display = ImageFont.truetype(font_path, size=180)
font_caption = ImageFont.truetype(font_path, size=40)

# Draw text
draw.text((240, 320), "TITLE", fill="#141413", font=font_display)

# Draw shape
draw.rectangle([240, 600, WIDTH - 240, 610], fill="#d97757")  # thin accent line

# Save
canvas.save("output.png", dpi=(300, 300))
```

### Python (cairo / pycairo) for PDF

```python
import cairo
import math

# A4 at 72 DPI
WIDTH_PT, HEIGHT_PT = 595, 842
surface = cairo.PDFSurface("output.pdf", WIDTH_PT, HEIGHT_PT)
ctx     = cairo.Context(surface)

# Background
ctx.set_source_rgb(0.98, 0.976, 0.961)  # #faf9f5
ctx.paint()

# Draw a circle (signature element)
ctx.arc(WIDTH_PT / 2, HEIGHT_PT / 2, 120, 0, 2 * math.pi)
ctx.set_source_rgb(0.851, 0.467, 0.341)  # #d97757
ctx.fill()

surface.finish()
```

### Checking Canvas Font Availability

```bash
ls ./canvas-fonts/
# Lists available .ttf / .otf files for use in Pillow or cairo
```

If `canvas-fonts/` is empty or absent, select an appropriate free font from a known source and document the choice in the design philosophy file.

## Color System for Art

When building the 4–6 color palette for a piece, think in roles — not just values:

| Role | Character | Example |
|------|-----------|---------|
| **Ground** | Large silent areas; sets the mood | `#0d1117` deep night |
| **Field** | Main visual mass; carries the philosophy | `#1e3a5f` dark ocean |
| **Figure** | The dominant active element | `#e8c547` warm gold |
| **Accent** | Sparse; the single risk | `#f05a5b` hot red |
| **Text** | Essential labels only | `#f0ede6` off-white |

Never give all five roles equal visual weight. Ground + Field should cover 80% of the canvas; Figure 15%; Accent and Text 5% combined.

## References

**Typography sources, canvas rendering patterns, and color theory**: See [references/README.md](references/README.md)
