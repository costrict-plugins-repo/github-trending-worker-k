---
name: bank-reconciliation
description: Generate monthly bank reconciliation statements comparing bank statement balance to cash book balance. Use when the user asks for bank reconciliation, bank recon, cash book reconciliation, or needs to reconcile bank and book balances.
user-invocable: true
argument-hint: "[month/period] [bank-statement-file] [cash-book-file]"
---

# Monthly Bank Reconciliation

Skill for generating professional monthly bank reconciliation statements in an accounting/audit context.

## Dependencies

```bash
pip install openpyxl pandas
```

## Usage

```
/bank-reconciliation [month/period] [bank-statement-filepath] [cash-book-filepath]
```

The user provides:
1. **Period** — the month/year for the reconciliation (e.g., "March 2026", "31/03/2026")
2. **Bank statement** — uploaded bank statement file (.xlsx, .xls, .csv, or .pdf)
3. **Cash book / GL** — uploaded cash book or general ledger extract (.xlsx, .xls, .csv)
4. **Optionally**: prior month reconciliation for comparison

If the user provides only one file, ask which source it represents. If no files are provided, ask the user for the required data or offer to create a blank template.

## Workflow

### Step 1: Read and Parse Input Files

```python
import pandas as pd

def read_source(filepath):
    if filepath.endswith('.csv'):
        df = pd.read_csv(filepath)
    else:
        df = pd.read_excel(filepath)
    return df
```

For PDF bank statements, extract transaction data first using PDF processing tools.

### Step 2: Identify Key Columns

**Bank Statement columns:**
- Date, Description/Narration, Debit/Withdrawal, Credit/Deposit, Balance

**Cash Book columns:**
- Date, Description/Particulars, Reference/Cheque No., Debit (DR), Credit (CR), Balance

Ask the user to confirm column mappings if ambiguous.

### Step 3: Match Transactions

```python
def match_transactions(bank_df, book_df, date_col, amount_col, tolerance=0.01):
    matched = []
    unmatched_bank = []
    book_remaining = book_df.copy()

    for _, bank_row in bank_df.iterrows():
        bank_amt = abs(bank_row[amount_col])
        match_mask = (abs(abs(book_remaining[amount_col]) - bank_amt) < tolerance)
        matches = book_remaining[match_mask]

        if len(matches) > 0:
            best_match = matches.iloc[0]
            matched.append((bank_row, best_match))
            book_remaining = book_remaining.drop(best_match.name)
        else:
            unmatched_bank.append(bank_row)

    unmatched_book = book_remaining.to_dict('records')
    return matched, unmatched_bank, unmatched_book
```

### Step 4: Classify Reconciling Items

**Add to Bank Statement Balance:**
- Outstanding deposits / deposits in transit
- Bank errors (overcharges, wrong debits)

**Deduct from Bank Statement Balance:**
- Outstanding/unpresented cheques

**Add to Cash Book Balance:**
- Direct deposits / bank credits (interest, transfers in)

**Deduct from Cash Book Balance:**
- Bank charges / service fees
- Direct debits / standing orders
- Returned/dishonoured cheques

### Step 5: Generate the Reconciliation Statement

Create a professionally formatted Excel workbook with:

- **Title section**: Entity name, "BANK RECONCILIATION STATEMENT", period, bank details
- **Section A**: Balance per bank statement + deposits in transit - outstanding cheques = adjusted bank balance
- **Section B**: Balance per cash book + bank credits not in book - bank debits not in book = adjusted book balance
- **Reconciliation check**: Green "BALANCED" or Red "DIFFERENCE"

Formatting:
- Title: Font(bold=True, size=14, color="1F4E79")
- Headers: Font(bold=True, size=11, color="FFFFFF"), Fill(#1F4E79)
- Sections: Font(bold=True, size=11, color="1F4E79"), Fill(#D6E4F0)
- Totals: Font(bold=True), Fill(#E2EFDA), double bottom border
- Currency: '#,##0.00'

## Reconciliation Statement Format

```
SECTION A: BALANCE PER BANK STATEMENT
  Balance per bank statement                          XX,XXX.XX
  Add: Deposits in transit                             X,XXX.XX
  Less: Outstanding/unpresented cheques              (X,XXX.XX)
  Adjusted balance per bank statement                 XX,XXX.XX
                                                      =========

SECTION B: BALANCE PER CASH BOOK
  Balance per cash book                               XX,XXX.XX
  Add: Bank credits not in cash book                   X,XXX.XX
  Less: Bank debits not in cash book                 (X,XXX.XX)
  Adjusted balance per cash book                      XX,XXX.XX
                                                      =========

  Adjusted Bank Balance MUST EQUAL Adjusted Book Balance
```

## Modes of Operation

### Mode 1: From Source Files (Automated Matching)
User provides bank statement and cash book files. Auto-match transactions, identify reconciling items, confirm with user, generate statement.

### Mode 2: From User Input (Manual Entry)
User provides balances and reconciling items directly via conversation.

### Mode 3: Blank Template
Generate a formatted blank reconciliation template with placeholder rows.

## Professional Formatting Standards

1. **Header row**: Dark blue (#1F4E79) background, white bold text
2. **Section headers**: Light blue (#D6E4F0) background
3. **Total rows**: Light green (#E2EFDA) background, double underline border
4. **Monetary amounts**: #,##0.00 format
5. **Dates**: DD/MM/YYYY format
6. **Reconciliation check**: Green if balanced, Red if difference exists
7. **Print-ready**: Fit to page width, portrait orientation
