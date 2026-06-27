---
name: pptx
description: Provides guidance for creating, reading, editing, and manipulating PowerPoint presentations (.pptx files). Use any time a .pptx file is involved — creating slide decks, pitch decks, reading/extracting text, editing existing presentations, combining or splitting files, working with templates, layouts, speaker notes, or comments. Trigger whenever the user mentions "deck," "slides," "presentation," or references a .pptx filename.
version: 1.0.0
author: Orchestra Research
license: MIT
tags: [PPTX, PowerPoint, Presentations, pptxgenjs, Design]
dependencies: [pptxgenjs>=3.0.0]
---

# PPTX

Use this skill any time a `.pptx` file is involved in any way — as input, output, or both.

## Quick Reference

| Task | Approach |
|------|----------|
| Read/analyze content | `python -m markitdown presentation.pptx` |
| Edit existing presentation | Unpack → edit XML → repack |
| Create from scratch | pptxgenjs |

## Reading Content

```bash
# Text extraction
python -m markitdown presentation.pptx

# Visual overview
python scripts/thumbnail.py presentation.pptx

# Raw XML
python scripts/office/unpack.py presentation.pptx unpacked/
```

## Creating from Scratch (pptxgenjs)

```bash
npm install -g pptxgenjs
```

```javascript
const pptx = require('pptxgenjs');
const pres = new pptx();

pres.addSlide().addText("Hello World", {
  x: 1, y: 1, w: 8, h: 1,
  fontSize: 36, bold: true, color: "FFFFFF"
});

pres.writeFile({ fileName: "presentation.pptx" });
```

## Design Principles

**Don't create boring slides.** Plain bullets on a white background won't impress anyone.

### Color Strategy

- **Pick a bold, content-informed palette** — if swapping your colors into a completely different presentation would still "work," you haven't made specific enough choices
- **Dominance over equality**: One color should dominate (60-70% visual weight), with 1-2 supporting tones and one sharp accent
- **Dark/light contrast**: Dark backgrounds for title + conclusion slides, light for content ("sandwich" structure), or commit to dark throughout for a premium feel

**Example palettes**:

| Theme | Primary | Secondary | Accent |
|-------|---------|-----------|--------|
| Midnight Executive | `#1E2761` (navy) | `#CADCFC` (ice blue) | `#FFFFFF` |
| Forest & Moss | `#2C5F2D` (forest) | `#97BC62` (moss) | `#F5F5F5` |
| Coral Energy | `#F96167` (coral) | `#F9E795` (gold) | `#2F3C7E` |
| Charcoal Minimal | `#36454F` (charcoal) | `#F2F2F2` (off-white) | `#212121` |

### Slide Layout Options

- Two-column (text left, illustration right)
- Icon + text rows (icon in colored circle, bold header, description below)
- 2×2 or 2×3 grid with image on one side
- Half-bleed image (full left or right side) with content overlay

**Every slide needs a visual element** — image, chart, icon, or shape. Text-only slides are forgettable.

### Typography

| Element | Size |
|---------|------|
| Slide title | 36–44pt bold |
| Section header | 20–24pt bold |
| Body text | 14–16pt |
| Captions | 10–12pt muted |

Recommended pairs: Georgia + Calibri, Arial Black + Arial, Cambria + Calibri.

### Common Mistakes to Avoid

- Don't repeat the same layout — vary across slides
- Don't center body text — left-align paragraphs and lists
- Don't default to blue — pick colors that reflect the specific topic
- Don't create text-only slides — add images, icons, charts, or visual elements
- **NEVER use accent lines under titles** — hallmark of AI-generated slides; use whitespace or background color instead
- Don't use low-contrast elements — both icons AND text need strong contrast

## QA (Required)

**Assume there are problems. Your job is to find them.**

```bash
# Content QA
python -m markitdown output.pptx

# Check for leftover placeholder text
python -m markitdown output.pptx | grep -iE "xxxx|lorem|ipsum"
```

### Visual QA Workflow

Use **subagents** for visual inspection — you've been staring at the code and will see what you expect, not what's there.

```bash
# Convert to images
python scripts/office/soffice.py --headless --convert-to pdf output.pptx
pdftoppm -jpeg -r 150 output.pdf slide

# Re-render specific slides after fixes
pdftoppm -jpeg -r 150 -f N -l N output.pdf slide-fixed
```

Inspect for: overlapping elements, text overflow, low-contrast text/icons, insufficient margins (< 0.5"), uneven gaps, leftover placeholder content.

**Verification loop**: Generate → Convert to images → Inspect → List issues → Fix → Re-verify affected slides → Repeat until a full pass finds no new issues.

## Common Issues

**Text box padding misalignment**: When aligning lines or shapes with text edges, set `margin: 0` on the text box or offset the shape to account for padding.

**Decorative lines positioned wrong**: Accent lines positioned for single-line text will misalign when titles wrap to two lines — remove or use whitespace instead.

**Leftover placeholders**: Always grep for `xxxx`, `lorem`, `ipsum` before declaring success.

## Dependencies

- `pip install "markitdown[pptx]"` — text extraction
- `pip install Pillow` — thumbnail grids
- `npm install -g pptxgenjs` — creating from scratch
- LibreOffice (`soffice`) — PDF conversion
- Poppler (`pdftoppm`) — PDF to images

## References

**pptxgenjs API and XML editing patterns**: See [references/README.md](references/README.md)
