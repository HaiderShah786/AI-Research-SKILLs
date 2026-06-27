---
name: xlsx
description: Provides guidance for creating, reading, editing, and manipulating Excel spreadsheet files (.xlsx, .xlsm, .csv, .tsv). Use when working with spreadsheets, financial models, data analysis, or restructuring messy tabular data. Handles formulas, formatting, charts, and cross-sheet references.
version: 1.0.0
author: Orchestra Research
license: MIT
tags: [XLSX, Excel, Spreadsheets, pandas, openpyxl, Data Processing]
dependencies: [openpyxl>=3.1.0, pandas>=2.0.0]
---

# XLSX

Use this skill whenever a spreadsheet file is involved: `.xlsx`, `.xlsm`, `.csv`, `.tsv`. Covers data analysis, financial models, formatting, formulas, and cross-sheet references.

## Quick Reference

| Task | Best Tool |
|------|-----------|
| Data analysis, bulk operations, simple exports | pandas |
| Complex formatting, formulas, Excel-specific features | openpyxl |
| Reading CSV/TSV | pandas `read_csv()` |

## Core Principle: Use Formulas, Not Hardcoded Values

Always use Excel formulas instead of calculating values in Python and hardcoding them. This ensures spreadsheets remain dynamic and updatable when source data changes.

```python
# Good: formula stays live in Excel
ws['B2'] = '=A2*1.1'

# Bad: hardcoded value breaks when A2 changes
ws['B2'] = 110
```

## Reading and Writing with pandas

```python
import pandas as pd

# Read Excel
df = pd.read_excel("data.xlsx", sheet_name="Sheet1")

# Read CSV
df = pd.read_csv("data.csv")

# Write Excel (simple export)
df.to_excel("output.xlsx", index=False)

# Write multiple sheets
with pd.ExcelWriter("output.xlsx") as writer:
    df1.to_excel(writer, sheet_name="Summary", index=False)
    df2.to_excel(writer, sheet_name="Detail", index=False)
```

## Formatting with openpyxl

```python
from openpyxl import Workbook, load_workbook
from openpyxl.styles import Font, PatternFill, Alignment, numbers

wb = Workbook()
ws = wb.active

# Write value with formula
ws['A1'] = 'Revenue'
ws['B1'] = 1000000
ws['C1'] = '=B1*0.1'  # Formula

# Formatting
ws['A1'].font = Font(bold=True, name='Arial', size=12)
ws['B1'].number_format = '$#,##0'  # Currency format
ws['C1'].number_format = '0.0%'    # Percentage

wb.save("output.xlsx")
```

## Financial Model Conventions

### Color Coding (Industry Standard)

| Color | Meaning |
|-------|---------|
| **Blue text** | Hardcoded inputs and changeable numbers |
| **Black text** | All formulas and calculations |
| **Green text** | Cross-sheet links within same workbook |
| **Red text** | External links to other files |
| **Yellow background** | Key assumptions needing attention |

```python
from openpyxl.styles import Font, PatternFill

# Input cell (blue text)
ws['B2'].font = Font(color="0000FF")

# Formula cell (black text — default)
ws['C2'].font = Font(color="000000")

# Key assumption (yellow background)
ws['D2'].fill = PatternFill(start_color="FFFF00", end_color="FFFF00", fill_type="solid")
```

### Number Formatting Standards

```python
# Years as text strings
ws['A1'] = "2024"

# Currency with units in headers
ws['B2'].number_format = '$#,##0'

# Zeros display as dash
ws['C2'].number_format = '$#,##0;-$#,##0;"-"'

# Percentages
ws['D2'].number_format = '0.0%'

# Negatives in parentheses
ws['E2'].number_format = '#,##0;(#,##0)'
```

## Cross-Sheet References

```python
# Reference another sheet
ws['B5'] = '=Summary!B2*1.05'

# SUM across sheets
ws['C5'] = "=SUM(Jan:Dec!B2)"
```

## Formula Verification Checklist

Before building a full model:
- [ ] Test 2–3 sample references manually to confirm they resolve correctly
- [ ] Confirm column mapping accuracy (Python 0-indexed vs Excel 1-indexed)
- [ ] Remember Excel rows are 1-indexed (DataFrame row 5 = Excel row 6)
- [ ] Check for NaN handling in formulas
- [ ] Verify no `#DIV/0!`, `#REF!`, `#VALUE!`, `#N/A`, `#NAME?` errors

```python
# After writing formulas, recalculate
import subprocess
subprocess.run(["python", "scripts/recalc.py", "output.xlsx"])
```

## Common Issues

**`#REF!` errors**: Invalid cell references, usually from deleted rows/columns or incorrect cross-sheet syntax.

**`#DIV/0!` errors**: Division by zero. Wrap with `=IFERROR(A1/B1, 0)` or `=IF(B1=0, 0, A1/B1)`.

**NaN handling**: pandas NaN becomes empty cell in Excel; wrap calculations with `fillna(0)` where appropriate.

**`data_only=True` loses formulas**: When loading an existing workbook with `data_only=True`, formulas are replaced with their last calculated value. Don't save after loading this way.

**Circular reference**: Excel will refuse to calculate. Restructure so no cell depends on its own value chain.

**Row index offset**: DataFrame index 0 = Excel row 2 (row 1 is the header). Always offset by +2 when mapping.

## Dependencies

- `pip install openpyxl` — formulas, formatting, Excel-specific features
- `pip install pandas openpyxl` — data analysis and export
- `pip install xlrd` — reading legacy `.xls` files (not `.xlsx`)

## References

**Charts, pivot tables, and advanced formula patterns**: See [references/README.md](references/README.md)
