---
name: excel-processing
description: Create, read, edit, and analyze Excel spreadsheets (.xlsx/.xls/.csv). Generate pivot tables, charts, financial schedules, reconciliations, and formatted reports. Use when working with Excel files, spreadsheets, tabular data, .xlsx files, CSV data, financial schedules, or data analysis tasks.
user-invocable: true
argument-hint: "[action] [filepath]"
---

# Excel Processing

Skill for comprehensive Excel/spreadsheet handling in an accounting and professional services context (audit, tax, advisory).

## Dependencies

```bash
pip install openpyxl xlrd pandas xlsxwriter
```

## Available Actions

### 1. Read Excel (`/excel-processing read <filepath>`)

Read and parse Excel data:

```python
import openpyxl

def read_excel(filepath, sheet_name=None):
    wb = openpyxl.load_workbook(filepath, data_only=True)
    ws = wb[sheet_name] if sheet_name else wb.active
    data = []
    for row in ws.iter_rows(values_only=True):
        data.append(list(row))
    return {"headers": data[0] if data else [], "rows": data[1:], "sheets": wb.sheetnames}
```

For `.xls` (legacy): use `xlrd`. For `.csv`: use `csv.reader`.

### 2. Create Excel (`/excel-processing create <output>`)

Create formatted Excel workbooks with professional styling:

```python
import openpyxl
from openpyxl.styles import Font, PatternFill, Alignment, Border, Side

def create_workbook(title, headers, rows, output_path):
    wb = openpyxl.Workbook()
    ws = wb.active
    ws.title = title

    header_font = Font(bold=True, color="FFFFFF", size=11)
    header_fill = PatternFill(start_color="1F4E79", end_color="1F4E79", fill_type="solid")
    thin_border = Border(
        left=Side(style='thin'), right=Side(style='thin'),
        top=Side(style='thin'), bottom=Side(style='thin')
    )

    for col, header in enumerate(headers, 1):
        cell = ws.cell(row=1, column=col, value=header)
        cell.font = header_font
        cell.fill = header_fill
        cell.alignment = Alignment(horizontal='center')
        cell.border = thin_border

    for row_idx, row_data in enumerate(rows, 2):
        for col_idx, value in enumerate(row_data, 1):
            cell = ws.cell(row=row_idx, column=col_idx, value=value)
            cell.border = thin_border
            if isinstance(value, (int, float)):
                cell.number_format = '#,##0.00'

    for col in ws.columns:
        max_length = max(len(str(cell.value or "")) for cell in col)
        ws.column_dimensions[col[0].column_letter].width = min(max_length + 4, 50)

    ws.auto_filter.ref = ws.dimensions
    wb.save(output_path)
```

### 3. Edit Excel (`/excel-processing edit <filepath>`)

Modify existing workbooks — update cells, add rows, change formatting.

### 4. Data Analysis (`/excel-processing analyze <filepath>`)

Analyze spreadsheet data using pandas:

```python
import pandas as pd

def analyze_excel(filepath, sheet_name=None):
    df = pd.read_excel(filepath, sheet_name=sheet_name)
    analysis = {
        "shape": df.shape,
        "columns": list(df.columns),
        "summary": df.describe().to_dict(),
        "nulls": df.isnull().sum().to_dict(),
    }
    numeric_cols = df.select_dtypes(include=['number']).columns
    if len(numeric_cols) > 0:
        analysis["totals"] = df[numeric_cols].sum().to_dict()
    return analysis
```

### 5. Financial Reconciliation (`/excel-processing reconcile <file1> <file2>`)

Compare two data sources (bank statement vs GL, TB vs FS):

```python
import pandas as pd

def reconcile(source1_path, source2_path, key_column, amount_column, output_path):
    df1 = pd.read_excel(source1_path)
    df2 = pd.read_excel(source2_path)
    merged = pd.merge(df1, df2, on=key_column, how='outer', suffixes=('_s1', '_s2'), indicator=True)
    amt1 = f"{amount_column}_s1"
    amt2 = f"{amount_column}_s2"
    merged['difference'] = merged[amt1].fillna(0) - merged[amt2].fillna(0)
    merged['status'] = merged.apply(lambda r:
        'Match' if abs(r['difference']) < 0.01
        else 'Source 1 Only' if r['_merge'] == 'left_only'
        else 'Source 2 Only' if r['_merge'] == 'right_only'
        else 'Difference', axis=1)
    merged.to_excel(output_path, index=False)
    return merged
```

### 6. Generate Chart (`/excel-processing chart <filepath> <type>`)

Add charts (bar, pie, line) to Excel workbooks using `openpyxl.chart`.

### 7. Pivot Table (`/excel-processing pivot <filepath>`)

Create pivot-table-style summaries using `pandas.pivot_table`.

### 8. Convert Formats (`/excel-processing convert <filepath> <format>`)

Convert between Excel, CSV, and JSON using pandas.

## Use Cases

| Use Case | Action | Example |
|----------|--------|---------|
| Parse trial balance | `read` | Read TB.xlsx for audit engagement |
| Generate PBC checklist | `create` | PBC checklist with professional formatting |
| Bank reconciliation | `reconcile` | Compare bank statement vs GL |
| Tax computation schedule | `create` | Tax computation with formulas |
| Aged debtors analysis | `analyze` + `pivot` | Aging buckets from AR ledger |
| Revenue trend chart | `chart` | Monthly revenue bar chart |
| TB-to-FS mapping | `create` | Map TB accounts to FS captions |
| Convert CSV to Excel | `convert` | Format raw CSV data into .xlsx |

## Professional Formatting Standards

1. **Header row**: Dark blue (#1F4E79) background, white bold text
2. **Monetary amounts**: `#,##0` or `#,##0.00` format
3. **Dates**: `DD/MM/YYYY` format
4. **Auto-filter**: Always enable on header row
5. **Column widths**: Auto-fit with 4-char padding, max 50 chars
6. **Freeze panes**: Freeze row 1 (headers)
