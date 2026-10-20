---
name: claim-form-extraction
description: Extract and process data from claim forms (expense claims, insurance claims, medical claims, staff reimbursements, petty cash claims). Reads PDF/Excel/image claim forms, extracts claimant details, line items, amounts, and outputs structured Excel summaries. Use when the user mentions claim forms, expense claims, reimbursement claims, claim extraction, or needs to process/digitize claim documents.
user-invocable: true
argument-hint: "[filepath or action]"
---

# Claim Form Extraction

Skill for extracting and processing claim forms in an accounting context. Handles expense claims, insurance claims, medical claims, staff reimbursements, petty cash claims, and mileage claims.

## Dependencies

```bash
pip install pdfplumber openpyxl python-docx tabulate
```

## Workflow Overview

1. **Read** the claim form (PDF, Excel, Word, or image)
2. **Extract** structured data: claimant info, line items, amounts, dates, approvals
3. **Validate** totals and required fields
4. **Output** structured Excel summary with professional formatting

## Available Actions

### 1. Extract from PDF Claim Form (`/claim-form-extraction <filepath.pdf>`)

```python
import pdfplumber
import re

def extract_claim_pdf(filepath):
    with pdfplumber.open(filepath) as pdf:
        full_text = ""
        all_tables = []
        for page in pdf.pages:
            text = page.extract_text()
            if text:
                full_text += text + "\n"
            tables = page.extract_tables()
            all_tables.extend(tables)

    claim_data = {
        "claimant_name": extract_field(full_text, r"(?:Name|Claimant|Employee)\s*[:\-]?\s*(.+)"),
        "employee_id": extract_field(full_text, r"(?:Employee\s*(?:ID|No|Number)|Staff\s*(?:ID|No))\s*[:\-]?\s*(\S+)"),
        "department": extract_field(full_text, r"(?:Department|Dept|Division)\s*[:\-]?\s*(.+)"),
        "claim_date": extract_field(full_text, r"(?:Date|Claim\s*Date|Submission\s*Date)\s*[:\-]?\s*([\d/\-\.]+)"),
        "claim_number": extract_field(full_text, r"(?:Claim\s*(?:No|Number|Ref)|Reference)\s*[:\-]?\s*(\S+)"),
        "claim_type": extract_field(full_text, r"(?:Type|Category|Claim\s*Type)\s*[:\-]?\s*(.+)"),
        "claim_period": extract_field(full_text, r"(?:Period|Claim\s*Period|For\s*(?:the\s*)?(?:Month|Period))\s*[:\-]?\s*(.+)"),
    }

    line_items = extract_line_items(all_tables, full_text)

    total_pattern = r"(?:Total|Grand\s*Total|Amount\s*Claimed|Total\s*Claim)\s*[:\-]?\s*(?:RM\s*)?([\d,]+\.?\d*)"
    total_match = re.search(total_pattern, full_text, re.IGNORECASE)
    claim_data["total_claimed"] = float(total_match.group(1).replace(",", "")) if total_match else None

    claim_data["approved_by"] = extract_field(full_text, r"(?:Approved\s*[Bb]y|Authorised\s*[Bb]y|HOD)\s*[:\-]?\s*(.+)")
    claim_data["approval_date"] = extract_field(full_text, r"(?:Approval\s*Date|Date\s*Approved)\s*[:\-]?\s*([\d/\-\.]+)")

    return {"header": claim_data, "line_items": line_items}
```

### 2. Extract from Excel Claim Form (`/claim-form-extraction <filepath.xlsx>`)

Extract claim details from Excel-based claim forms using openpyxl with pattern-matching for label-value pairs and tabular line items.

### 3. Batch Extract Multiple Claims (`/claim-form-extraction batch <folder>`)

Process multiple claim forms from a folder and consolidate into a single summary.

### 4. Generate Claim Summary Excel (`/claim-form-extraction summary`)

Output extracted claims as a professionally formatted Excel workbook with:
- **Claims Summary** sheet: One row per claim with claimant, department, date, type, total
- **Line Items Detail** sheet: All individual line items across all claims
- Grand total, auto-filter, freeze panes, professional formatting

## Common Claim Form Types

| Claim Type | Typical Fields | Notes |
|------------|---------------|-------|
| **Expense Claim** | Date, description, category, amount, receipt no. | Most common; meals, transport, supplies |
| **Mileage Claim** | Date, from/to, distance (km), rate, amount | Rate varies by company |
| **Petty Cash Claim** | Date, description, voucher no., amount | Usually small amounts with cash receipts |
| **Medical Claim** | Date, clinic/hospital, treatment, amount | May have annual limits |
| **Insurance Claim** | Policy no., incident date, description, amount | Often multi-page with supporting docs |
| **Overtime Claim** | Date, hours, rate, description | May need manager approval |

## Extraction Strategy

1. **Identify format** — PDF (digital or scanned), Excel template, Word form
2. **Locate header fields** — Scan for label-value pairs (Name:, Date:, Dept:, etc.)
3. **Find line items table** — Look for tabular data with Date/Description/Amount columns
4. **Extract amounts** — Parse currency values, handle comma separators, validate totals
5. **Cross-check** — Compare sum of line items against stated total; flag discrepancies
6. **Handle variations** — Different companies use different layouts; adapt field matching

## Output Standards

1. **Header row**: Dark blue (#1F4E79) background, white bold text
2. **Monetary amounts**: #,##0.00 format
3. **Dates**: DD/MM/YYYY format
4. **Auto-filter**: Enabled on header rows
5. **Freeze panes**: Headers frozen for scrolling
6. **Column widths**: Auto-fit with padding
