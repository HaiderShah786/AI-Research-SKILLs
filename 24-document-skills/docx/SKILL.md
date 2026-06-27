---
name: docx
description: Provides guidance for creating, reading, editing, and manipulating Word documents (.docx files) using the docx JS library or XML editing. Use when a .docx file is involved as input, output, or both — including creating new documents, editing existing ones, adding tracked changes, tables, images, headers, footers, and comments.
version: 1.0.0
author: Orchestra Research
license: MIT
tags: [DOCX, Word, Document Processing, JavaScript, XML]
dependencies: [docx>=8.0.0]
---

# DOCX

A `.docx` file is a ZIP archive containing XML files. Use the `docx` JS library to create new documents and XML editing (unpack → edit → repack) to modify existing ones.

## Quick Reference

| Task | Approach |
|------|----------|
| Read/analyze content | `pandoc` or unpack for raw XML |
| Create new document | Use `docx` JS library |
| Edit existing document | Unpack → edit XML → repack |

## Creating New Documents

```bash
npm install -g docx
```

```javascript
const { Document, Packer, Paragraph, TextRun, Table, TableRow, TableCell,
        AlignmentType, HeadingLevel, BorderStyle, WidthType, ShadingType,
        LevelFormat, PageNumber, PageBreak } = require('docx');

const doc = new Document({
  sections: [{ children: [new Paragraph({ children: [new TextRun("Hello")] })] }]
});
Packer.toBuffer(doc).then(buffer => fs.writeFileSync("doc.docx", buffer));
```

### Page Size (Critical — Always Set Explicitly)

docx defaults to A4. For US Letter:

```javascript
sections: [{
  properties: {
    page: {
      size: { width: 12240, height: 15840 },  // 8.5" × 11" in DXA (1440 DXA = 1 inch)
      margin: { top: 1440, right: 1440, bottom: 1440, left: 1440 }
    }
  },
  children: [/* content */]
}]
```

### Styles and Typography

```javascript
new Document({
  styles: {
    default: { document: { run: { font: "Arial", size: 24 } } },
    paragraphStyles: [{
      id: "Heading1", name: "Heading 1", basedOn: "Normal",
      run: { size: 32, bold: true, font: "Arial" },
      paragraph: { spacing: { before: 240, after: 240 }, outlineLevel: 0 }
    }]
  },
  sections: [{ children: [
    new Paragraph({ heading: HeadingLevel.HEADING_1, children: [new TextRun("Title")] })
  ]}]
})
```

### Lists

```javascript
// Always use LevelFormat.BULLET — never unicode bullets
new Document({
  numbering: {
    config: [{
      reference: "bullets",
      levels: [{ level: 0, format: LevelFormat.BULLET, text: "•",
        alignment: AlignmentType.LEFT,
        style: { paragraph: { indent: { left: 720, hanging: 360 } } } }]
    }]
  },
  sections: [{ children: [
    new Paragraph({ numbering: { reference: "bullets", level: 0 },
      children: [new TextRun("Bullet item")] })
  ]}]
})
```

### Tables (Dual Widths Required)

```javascript
const border = { style: BorderStyle.SINGLE, size: 1, color: "CCCCCC" };
const borders = { top: border, bottom: border, left: border, right: border };

new Table({
  width: { size: 9360, type: WidthType.DXA },
  columnWidths: [4680, 4680],
  rows: [new TableRow({ children: [
    new TableCell({
      borders,
      width: { size: 4680, type: WidthType.DXA },
      shading: { fill: "D5E8F0", type: ShadingType.CLEAR },
      margins: { top: 80, bottom: 80, left: 120, right: 120 },
      children: [new Paragraph({ children: [new TextRun("Cell")] })]
    })
  ]})]
})
```

### Headers/Footers

```javascript
sections: [{
  headers: { default: new Header({ children: [new Paragraph("Header text")] }) },
  footers: { default: new Footer({ children: [new Paragraph({
    children: [new TextRun("Page "), new TextRun({ children: [PageNumber.CURRENT] })]
  })] }) },
  children: [/* content */]
}]
```

## Editing Existing Documents

Follow all 3 steps in order:

```bash
# Step 1: Unpack
python scripts/office/unpack.py document.docx unpacked/

# Step 2: Edit XML in unpacked/word/
# Use smart quotes: &#x2018; &#x2019; &#x201C; &#x201D;

# Step 3: Pack
python scripts/office/pack.py unpacked/ output.docx --original document.docx
```

### Tracked Changes in XML

```xml
<!-- Insertion -->
<w:ins w:id="1" w:author="Claude" w:date="2025-01-01T00:00:00Z">
  <w:r><w:t>inserted text</w:t></w:r>
</w:ins>

<!-- Deletion -->
<w:del w:id="2" w:author="Claude" w:date="2025-01-01T00:00:00Z">
  <w:r><w:delText>deleted text</w:delText></w:r>
</w:del>
```

## Critical Rules

- **Set page size explicitly** — defaults to A4
- **Never use `\n`** — use separate Paragraph elements
- **Never use unicode bullets** — use `LevelFormat.BULLET`
- **PageBreak must be inside a Paragraph**
- **Tables need dual widths** — both `columnWidths` on Table AND `width` on each cell
- **Always use `ShadingType.CLEAR`** — never SOLID
- **Override built-in styles** — use exact IDs and include `outlineLevel` for TOC
- **Always set table width with DXA** — never `WidthType.PERCENTAGE`

## Common Issues

**Formula/reference errors**: Always test 2–3 sample cell references before building full model; confirm column mapping accuracy.

**Image not rendering**: `ImageRun` requires explicit `type` field (`"png"`, `"jpg"`, etc.).

**Table layout broken**: Table `width` must equal sum of `columnWidths`; always add cell margins.

## Dependencies

- **pandoc**: Text extraction
- **docx**: `npm install -g docx` (new documents)
- **LibreOffice**: PDF conversion
- **Poppler**: `pdftoppm` for images

## References

**Full API reference and XML patterns**: See [references/README.md](references/README.md)
