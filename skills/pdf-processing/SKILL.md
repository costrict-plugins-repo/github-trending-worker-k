---
name: pdf-processing
description: Extract text and tables from PDF files, fill forms, merge/split PDFs, convert PDFs, add watermarks, and generate PDF reports. Use when working with PDF files, when the user mentions PDFs, forms, document extraction, PDF merging, splitting, watermarking, or PDF report generation.
user-invocable: true
argument-hint: "[action] [filepath]"
---

# PDF Processing

Skill for comprehensive PDF handling in an accounting and professional services context (audit, tax, advisory).

## Quick Start

```python
import pdfplumber

with pdfplumber.open("document.pdf") as pdf:
    for page in pdf.pages:
        text = page.extract_text()
        tables = page.extract_tables()
```

## Dependencies

```bash
pip install pdfplumber PyPDF2 reportlab pikepdf fpdf2
```

## Available Actions

### 1. Extract Text (`/pdf-processing extract <filepath>`)

```python
import pdfplumber

def extract_text(filepath):
    text_content = []
    with pdfplumber.open(filepath) as pdf:
        for i, page in enumerate(pdf.pages):
            text = page.extract_text()
            if text:
                text_content.append(f"--- Page {i+1} ---\n{text}")
    return "\n\n".join(text_content)
```

### 2. Extract Tables (`/pdf-processing tables <filepath>`)

Extract tabular data from PDFs (financial statements, tax returns, trial balances):

```python
import pdfplumber

def extract_tables(filepath):
    all_tables = []
    with pdfplumber.open(filepath) as pdf:
        for i, page in enumerate(pdf.pages):
            tables = page.extract_tables()
            for j, table in enumerate(tables):
                all_tables.append({
                    "page": i + 1,
                    "table_index": j + 1,
                    "headers": table[0] if table else [],
                    "rows": table[1:] if len(table) > 1 else []
                })
    return all_tables
```

### 3. Merge PDFs (`/pdf-processing merge <file1> <file2> ... <output>`)

```python
from PyPDF2 import PdfMerger

def merge_pdfs(input_files, output_path):
    merger = PdfMerger()
    for pdf_file in input_files:
        merger.append(pdf_file)
    merger.write(output_path)
    merger.close()
```

### 4. Split PDF (`/pdf-processing split <filepath> <pages>`)

```python
from PyPDF2 import PdfReader, PdfWriter

def split_pdf(filepath, page_ranges, output_prefix):
    reader = PdfReader(filepath)
    for i, (start, end) in enumerate(page_ranges):
        writer = PdfWriter()
        for page_num in range(start - 1, min(end, len(reader.pages))):
            writer.add_page(reader.pages[page_num])
        output_path = f"{output_prefix}_part{i+1}.pdf"
        with open(output_path, "wb") as f:
            writer.write(f)
```

### 5. Add Watermark (`/pdf-processing watermark <filepath> <text>`)

Add watermark text (e.g., "DRAFT", "CONFIDENTIAL"):

```python
from reportlab.pdfgen import canvas
from reportlab.lib.pagesizes import A4
from PyPDF2 import PdfReader, PdfWriter
import io

def add_watermark(filepath, watermark_text, output_path):
    packet = io.BytesIO()
    c = canvas.Canvas(packet, pagesize=A4)
    c.setFont("Helvetica", 60)
    c.setFillAlpha(0.15)
    c.saveState()
    c.translate(A4[0]/2, A4[1]/2)
    c.rotate(45)
    c.drawCentredString(0, 0, watermark_text)
    c.restoreState()
    c.save()
    packet.seek(0)

    watermark_reader = PdfReader(packet)
    reader = PdfReader(filepath)
    writer = PdfWriter()
    for page in reader.pages:
        page.merge_page(watermark_reader.pages[0])
        writer.add_page(page)
    with open(output_path, "wb") as f:
        writer.write(f)
```

### 6. Generate PDF Report (`/pdf-processing report <data>`)

```python
from fpdf import FPDF

def generate_report(title, sections, output_path):
    pdf = FPDF()
    pdf.set_auto_page_break(auto=True, margin=15)
    pdf.add_page()
    pdf.set_font("Helvetica", "B", 16)
    pdf.cell(0, 10, title, ln=True, align="C")
    pdf.ln(10)
    for section in sections:
        pdf.set_font("Helvetica", "B", 12)
        pdf.cell(0, 8, section["heading"], ln=True)
        pdf.set_font("Helvetica", "", 10)
        pdf.multi_cell(0, 6, section["content"])
        pdf.ln(5)
    pdf.output(output_path)
```

### 7. Fill PDF Form (`/pdf-processing fill-form <template> <data>`)

```python
from PyPDF2 import PdfReader, PdfWriter

def fill_form(template_path, field_data, output_path):
    reader = PdfReader(template_path)
    writer = PdfWriter()
    writer.append(reader)
    writer.update_page_form_field_values(writer.pages[0], field_data)
    with open(output_path, "wb") as f:
        writer.write(f)
```

## Use Cases

| Use Case | Action | Example |
|----------|--------|---------|
| Extract TB from scanned PDF | `extract` + `tables` | Tax return schedules, bank statements |
| Merge audit working papers | `merge` | Combine sections into single PDF |
| Add DRAFT watermark | `watermark` | Draft audit reports, tax computations |
| Generate client report | `report` | Tax computation summaries, advisory reports |
| Split large bank statement | `split` | Separate monthly statements |
| Fill statutory forms | `fill-form` | Government forms, regulatory filings |
