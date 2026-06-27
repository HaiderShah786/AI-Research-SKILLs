---
name: pdf
description: Provides guidance for reading, creating, editing, and manipulating PDF files. Use whenever the user wants to extract text or tables from PDFs, merge or split PDFs, rotate pages, add watermarks, create new PDFs, fill forms, encrypt/decrypt, extract images, or run OCR on scanned PDFs.
version: 1.0.0
author: Orchestra Research
license: MIT
tags: [PDF, Document Processing, Python, pypdf, reportlab]
dependencies: [pypdf>=4.0.0, pdfplumber>=0.10.0, reportlab>=4.0.0]
---

# PDF

Use this skill whenever the user wants to do anything with PDF files — reading, creating, editing, merging, splitting, or processing.

## Quick Reference

| Task | Best Tool | Method |
|------|-----------|--------|
| Merge PDFs | pypdf | `writer.add_page(page)` |
| Split PDFs | pypdf | One page per file |
| Extract text | pdfplumber | `page.extract_text()` |
| Extract tables | pdfplumber | `page.extract_tables()` |
| Create new PDFs | reportlab | Canvas or Platypus |
| OCR scanned PDFs | pytesseract | Convert to image first |
| Command-line merge/split | qpdf | `qpdf --pages` |

## Reading PDFs

```python
from pypdf import PdfReader

reader = PdfReader("document.pdf")
print(f"Pages: {len(reader.pages)}")

# Extract text
text = ""
for page in reader.pages:
    text += page.extract_text()
```

### Extract Tables (pdfplumber)

```python
import pdfplumber

with pdfplumber.open("document.pdf") as pdf:
    for page in pdf.pages:
        # Text with layout preserved
        text = page.extract_text()

        # Tables as lists of rows
        tables = page.extract_tables()
        for table in tables:
            for row in table:
                print(row)
```

```python
# Convert table to pandas DataFrame
import pandas as pd

with pdfplumber.open("document.pdf") as pdf:
    table = pdf.pages[0].extract_tables()[0]
    df = pd.DataFrame(table[1:], columns=table[0])
    df.to_excel("output.xlsx", index=False)
```

## Merging and Splitting

```python
from pypdf import PdfReader, PdfWriter

# Merge multiple PDFs
writer = PdfWriter()
for path in ["file1.pdf", "file2.pdf", "file3.pdf"]:
    reader = PdfReader(path)
    for page in reader.pages:
        writer.add_page(page)

with open("merged.pdf", "wb") as f:
    writer.write(f)

# Split: extract specific pages
writer = PdfWriter()
reader = PdfReader("document.pdf")
for i in [0, 2, 4]:  # Pages 1, 3, 5 (0-indexed)
    writer.add_page(reader.pages[i])

with open("extracted.pdf", "wb") as f:
    writer.write(f)
```

## Creating PDFs (reportlab)

```python
from reportlab.lib.pagesizes import letter
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer
from reportlab.lib.styles import getSampleStyleSheet

doc = SimpleDocTemplate("output.pdf", pagesize=letter)
styles = getSampleStyleSheet()

story = [
    Paragraph("Title", styles["Title"]),
    Spacer(1, 12),
    Paragraph("Body text here.", styles["Normal"]),
]

doc.build(story)
```

### Subscripts and Superscripts

```python
# Use HTML-style tags in Paragraph objects — avoid Unicode characters
Paragraph("H<sub>2</sub>O and E=mc<sup>2</sup>", styles["Normal"])
```

## Other Operations

### Rotate Pages

```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader("document.pdf")
writer = PdfWriter()
for page in reader.pages:
    page.rotate(90)  # 90, 180, or 270
    writer.add_page(page)

with open("rotated.pdf", "wb") as f:
    writer.write(f)
```

### Password Protection

```python
writer = PdfWriter()
# ... add pages ...
writer.encrypt("user_password", "owner_password")
with open("protected.pdf", "wb") as f:
    writer.write(f)
```

### OCR Scanned PDFs

```python
from pdf2image import convert_from_path
import pytesseract

images = convert_from_path("scanned.pdf")
text = ""
for image in images:
    text += pytesseract.image_to_string(image)
```

## Command-Line Tools

```bash
# qpdf: merge, split, decrypt
qpdf --empty --pages file1.pdf file2.pdf -- merged.pdf
qpdf input.pdf --pages . 1-5 -- pages1-5.pdf

# pdftotext: extract with layout preservation
pdftotext -layout document.pdf output.txt
```

## Common Issues

**`data_only=True` loses formulas**: Don't use this parameter when you need to preserve formulas in source data.

**Table extraction fails**: pdfplumber's `extract_tables()` works best on PDFs with grid lines. For borderless tables, use `extract_text()` and parse manually.

**OCR accuracy poor**: Convert to 300 DPI before OCR: `convert_from_path("scan.pdf", dpi=300)`.

**Encrypted PDF can't be read**: Decrypt first with qpdf: `qpdf --decrypt --password=PASS input.pdf output.pdf`.

## Dependencies

- `pip install pypdf` — basic operations (merge, split, rotate, encrypt)
- `pip install pdfplumber` — text and table extraction
- `pip install reportlab` — create PDFs from scratch
- `pip install pytesseract pdf2image` — OCR scanned PDFs
- **Poppler** (system): `pdftoppm`, `pdftotext`, `pdfinfo`
- **qpdf** (system): advanced manipulation and decryption

## References

**Form filling, watermarks, and image extraction**: See [references/README.md](references/README.md)
