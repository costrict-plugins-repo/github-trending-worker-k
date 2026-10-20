# Worker K

**AI workforce skills for Claude Code** — Excel, Word, PDF, research, bank reconciliation, and more. Built for accounting and professional service firms.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

## What is Worker K?

Worker K is a collection of Claude Code skills that turn Claude into a capable office assistant. Originally built for Malaysian accounting and audit firms, its skills are useful for any professional services context.

With Worker K installed, you can ask Claude to:
- Read and create Excel spreadsheets with professional formatting
- Generate Word documents, letters, and reports
- Extract data from PDFs, merge/split files, add watermarks
- Conduct structured research on tax, audit, and regulatory topics
- Reconcile bank statements against cash books
- Extract structured data from claim forms
- Browse websites and scrape data with Playwright

## Features

### Office Documents
- **Excel** — Read, create, edit, analyze, reconcile, chart, pivot, convert (.xlsx/.xls/.csv)
- **Word** — Read, create, edit, mail merge, convert to PDF (.docx)
- **PDF** — Extract text/tables, merge, split, watermark, fill forms, generate reports

### Professional Services
- **Research** — Structured research across accounting, tax, audit, and regulatory domains
- **Bank Reconciliation** — Automated bank-to-book matching with professional Excel output
- **Claim Extraction** — Parse expense/insurance/medical claims from PDF/Excel into structured data
- **Management Rep Letter** — Template-based letter generation for audit engagements

### Web Automation
- **Web Browse** — Browse, screenshot, scrape tables, fill forms, download files via Playwright

## Installation

```bash
claude plugin install WeiKhjan/worker-k
```

## Configuration

### Python Dependencies

Most skills require Python libraries. Install them before first use:

```bash
pip install openpyxl xlrd pandas xlsxwriter python-docx docx2txt pdfplumber PyPDF2 reportlab pikepdf fpdf2 playwright beautifulsoup4 tabulate
```

For web browsing, also install the Playwright browser:

```bash
playwright install chromium
```

### No Authentication Required

Worker K skills process everything locally on your machine. No API keys, OAuth setup, or external accounts needed.

## Usage Examples

### Example 1: Analyze an Excel File

> "Read Sales_Q1.xlsx and create a pivot table summarizing revenue by product category and month. Add a bar chart."

Worker K will use the `excel-processing` skill to:
1. Parse the Excel file with openpyxl
2. Analyze data with pandas pivot_table
3. Generate a formatted workbook with the pivot table and chart

### Example 2: Research Tax Rules

> "Research the latest withholding tax rates for royalty payments to non-residents in Malaysia. Include DTA treaty rates for Singapore and Japan."

Worker K will use the `research` skill to:
1. Reference Section 109 of the Income Tax Act 1967
2. Search for current DTA treaty rates
3. Output a structured research memo with citations

### Example 3: Bank Reconciliation

> "Generate a bank reconciliation for March 2026. Here's the bank statement and cash book."

Worker K will use the `bank-reconciliation` skill to:
1. Parse both source files
2. Auto-match transactions by amount and date
3. Classify reconciling items (outstanding cheques, deposits in transit, bank charges)
4. Generate a professionally formatted Excel reconciliation statement

### Example 4: Extract Claim Data

> "Extract all the claim details from these 5 expense claim PDFs and give me a consolidated summary."

Worker K will use the `claim-form-extraction` skill to:
1. Parse each PDF for claimant info, line items, and amounts
2. Validate totals against line item sums
3. Output a consolidated Excel workbook with summary and detail sheets

## Agent Personas

Worker K powers multiple AI agent personas, each specialized for different roles:

| Persona | Specialization |
|---------|---------------|
| **Executive Assistant** | International operations, email, travel, scheduling |
| **Audit Agent** | Malaysian external audit, ISA compliance, working papers |
| **Tax Agent** | Malaysian tax computation, SST, withholding tax, PCB/MTD |
| **Report Compilation Agent** | MPERS/MFRS financial reporting, director's reports |

Learn more at [workerk.aitomate.it.com](https://workerk.aitomate.it.com)

## Skills Reference

| Skill | Command | Description |
|-------|---------|-------------|
| Excel Processing | `/excel-processing` | Read, create, edit, analyze, reconcile, chart, pivot |
| Word Processing | `/word-processing` | Read, create, edit, mail merge, convert to PDF |
| PDF Processing | `/pdf-processing` | Extract, merge, split, watermark, fill forms, report |
| Research | `/research` | Structured research on professional topics |
| Web Browse | `/web-browse` | Browse, screenshot, scrape, fill forms, download |
| Bank Reconciliation | `/bank-reconciliation` | Bank-to-book reconciliation with Excel output |
| Claim Extraction | `/claim-form-extraction` | Parse claim forms into structured Excel data |
| Management Rep Letter | `/management-rep-letter` | Template-based audit letter generation |

## Privacy Policy

Worker K processes all data locally on your machine. No telemetry, no data collection, no external servers.

See [PRIVACY.md](PRIVACY.md) for the full privacy policy, or visit [workerk.aitomate.it.com/privacy](https://workerk.aitomate.it.com/privacy).

## Security

See [SECURITY.md](SECURITY.md) for our vulnerability reporting policy.

## Support

- **Issues**: [github.com/WeiKhjan/worker-k/issues](https://github.com/WeiKhjan/worker-k/issues)
- **Website**: [workerk.aitomate.it.com](https://workerk.aitomate.it.com)
- **Email**: support@aitomate.it.com

## License

[MIT](LICENSE)
