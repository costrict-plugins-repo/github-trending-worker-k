---
name: word-processing
description: Create, read, edit, and format Word documents (.docx). Generate letters, reports, memos, proposals, engagement letters, and professional documents with tables, headers, footers, and styles. Use when working with Word files, .docx documents, letters, reports, memos, proposals, or document formatting.
user-invocable: true
argument-hint: "[action] [filepath]"
---

# Word Document Processing

Skill for comprehensive Word document handling in an accounting and professional services context (audit, tax, advisory).

## Dependencies

```bash
pip install python-docx docx2txt
```

## Available Actions

### 1. Read Word Document (`/word-processing read <filepath>`)

Extract text and structure from .docx files:

```python
from docx import Document

def read_docx(filepath):
    doc = Document(filepath)
    content = {"paragraphs": [], "tables": []}
    for para in doc.paragraphs:
        content["paragraphs"].append({
            "text": para.text, "style": para.style.name,
            "bold": any(run.bold for run in para.runs)
        })
    for table in doc.tables:
        table_data = []
        for row in table.rows:
            table_data.append([cell.text for cell in row.cells])
        content["tables"].append(table_data)
    return content
```

Quick text extraction: `docx2txt.process(filepath)`

### 2. Create Word Document (`/word-processing create <output>`)

Generate professional documents with proper formatting:

```python
from docx import Document
from docx.shared import Pt, Cm, RGBColor
from docx.enum.text import WD_ALIGN_PARAGRAPH

def create_document(title, sections, output_path, letterhead=None):
    doc = Document()
    section = doc.sections[0]
    section.top_margin = Cm(2.54)
    section.bottom_margin = Cm(2.54)
    section.left_margin = Cm(2.54)
    section.right_margin = Cm(2.54)

    if letterhead:
        header = section.header
        hp = header.paragraphs[0]
        hp.text = letterhead["firm_name"]
        hp.alignment = WD_ALIGN_PARAGRAPH.CENTER

    title_para = doc.add_heading(title, level=0)
    title_para.alignment = WD_ALIGN_PARAGRAPH.CENTER

    for sec in sections:
        if sec.get("type") == "heading":
            doc.add_heading(sec["text"], level=sec.get("level", 1))
        elif sec.get("type") == "paragraph":
            p = doc.add_paragraph(sec["text"])
            if sec.get("bold"):
                for run in p.runs:
                    run.bold = True
        elif sec.get("type") == "table":
            add_formatted_table(doc, sec["headers"], sec["rows"])
        elif sec.get("type") == "page_break":
            doc.add_page_break()

    doc.save(output_path)
```

### 3. Add Formatted Table

```python
from docx.shared import Pt, RGBColor
from docx.oxml.ns import qn

def add_formatted_table(doc, headers, rows):
    table = doc.add_table(rows=1 + len(rows), cols=len(headers))
    table.style = 'Table Grid'
    for i, header in enumerate(headers):
        cell = table.rows[0].cells[i]
        cell.text = header
        for paragraph in cell.paragraphs:
            for run in paragraph.runs:
                run.font.bold = True
                run.font.color.rgb = RGBColor(255, 255, 255)
                run.font.size = Pt(10)
        shading = cell._element.get_or_add_tcPr()
        shading_elm = shading.makeelement(qn('w:shd'), {
            qn('w:fill'): '1F4E79', qn('w:val'): 'clear'
        })
        shading.append(shading_elm)
    for row_idx, row_data in enumerate(rows):
        for col_idx, value in enumerate(row_data):
            table.rows[row_idx + 1].cells[col_idx].text = str(value or "")
    return table
```

### 4. Edit Word Document (`/word-processing edit <filepath>`)

Modify existing documents — find and replace text across paragraphs and tables.

### 5. Mail Merge (`/word-processing mail-merge <template> <data>`)

Generate multiple documents from a template and data source (e.g., bulk confirmation letters):

```python
from docx import Document

def mail_merge(template_path, records, output_dir):
    outputs = []
    for record in records:
        doc = Document(template_path)
        replacements = {f"{{{{{k}}}}}": str(v) for k, v in record.items()}
        for para in doc.paragraphs:
            for placeholder, value in replacements.items():
                if placeholder in para.text:
                    for run in para.runs:
                        run.text = run.text.replace(placeholder, value)
        for table in doc.tables:
            for row in table.rows:
                for cell in row.cells:
                    for para in cell.paragraphs:
                        for placeholder, value in replacements.items():
                            if placeholder in para.text:
                                for run in para.runs:
                                    run.text = run.text.replace(placeholder, value)
        filename = f"{output_dir}/{record.get('name', 'doc')}_{record.get('ref', '')}.docx"
        doc.save(filename)
        outputs.append(filename)
    return outputs
```

### 6. Convert to PDF (`/word-processing to-pdf <filepath>`)

Convert .docx to PDF using LibreOffice headless:
```bash
soffice --headless --convert-to pdf --outdir output_dir input.docx
```

## Document Templates

### Letter Template
For: engagement letters, confirmation letters, management representation letters, advisory letters.

### Memo Template
For: internal memos, file notes, advisory notes.

### Report Template
For: tax computation reports, advisory reports, due diligence reports.

## Use Cases

| Use Case | Action | Example |
|----------|--------|---------|
| Generate engagement letter | `create` (letter) | Audit engagement letter |
| Bulk confirmation letters | `mail-merge` | Bank/debtor/creditor confirmations |
| Edit draft report | `edit` | Replace placeholders in advisory report |
| Extract from received docs | `read` | Parse client-submitted Word documents |
| Convert to PDF for filing | `to-pdf` | Convert signed letters to PDF |
| Generate memo | `create` (memo) | Internal file note on tax position |
