---
name: research
description: Conduct thorough research and analysis on accounting, tax, audit, advisory, regulatory, and business topics. Use when the user asks to research a topic, analyze regulations, summarize standards, investigate a company, perform due diligence, or compile reference material on any professional subject.
user-invocable: true
argument-hint: "[topic-or-question]"
---

# Research & Analysis

Skill for conducting thorough research across accounting, tax, audit, advisory, and regulatory domains. Leverages web search, web browsing, document analysis, and structured output.

## Research Workflow

### Step 1: Understand the Research Question

Parse the user's request to identify:
- **Topic**: What are we researching?
- **Scope**: How deep should the research go?
- **Output format**: Summary, memo, report, comparison table?
- **Jurisdiction**: Malaysia-specific, international, or both?

### Step 2: Gather Information

Use available tools in order of reliability:
1. **Internal knowledge** — accounting standards, tax law, audit methodology
2. **Web search** — current rates, recent regulatory changes, company info
3. **Web browsing** (via `/web-browse`) — detailed page content, tables, specific data
4. **Document analysis** — uploaded client files, prior research

### Step 3: Analyze & Synthesize

- Cross-reference multiple sources
- Flag conflicting information
- Distinguish authoritative sources (MASB, LHDN, MIA) from secondary
- Note effective dates for regulatory/tax changes

### Step 4: Deliver Structured Output

Format output based on the use case (see templates below).

## Research Domains

### Tax Research

Key sources: Income Tax Act 1967, LHDN Public Rulings, Tax Orders, Budget announcements.

| Topic | Key References |
|-------|---------------|
| Capital allowance rates | Schedule 3, ITA 1967 |
| Withholding tax rates | Section 109, ITA; DTA treaties |
| Tax incentives | PITA 1986, various Orders |
| Transfer pricing | Section 140A, TP Rules 2012 |
| SST | Service Tax Act 2018, Sales Tax Act 2018 |
| Stamp duty | Stamp Act 1949 |
| RPGT | RPGT Act 1976 |

### Audit & Assurance Research

Key sources: ISA (Malaysia), MIA By-Laws, Companies Act 2016, MPERS/MFRS.

| Topic | Key References |
|-------|---------------|
| Going concern | ISA 570, MPERS Section 3 |
| Related party transactions | ISA 550, MPERS Section 33 |
| Estimates & fair value | ISA 540, MPERS Sections 11/12 |
| Subsequent events | ISA 560, MPERS Section 32 |
| Revenue recognition | ISA 240, MPERS Section 23 |

### Advisory / Consulting Research

Business valuation, due diligence, M&A, SSM compliance, corporate governance.

### Key Regulatory Bodies

| Body | Domain | Website |
|------|--------|---------|
| LHDN | Tax | www.hasil.gov.my |
| SSM | Companies | www.ssm.com.my |
| MASB | Accounting standards | www.masb.org.my |
| MIA | Audit profession | www.mia.org.my |
| SC | Securities | www.sc.com.my |
| BNM | Banking/FX rates | www.bnm.gov.my |
| Bursa | Listed companies | www.bursamalaysia.com |

## Output Templates

### Research Summary (Quick)

```
## Research: [Topic]
**Question:** [What was asked]
**Answer:** [Concise answer in 2-3 paragraphs]
**Key Points:** [Bullet list]
**Sources:** [References]
**Effective Date:** [If applicable]
**Limitations:** [Caveats]
```

### Research Memo (Detailed)

```
# RESEARCH MEMO
**To:** [Recipient] | **From:** [Preparer] | **Date:** [Date] | **Re:** [Subject]
---
## 1. Background
## 2. Issue(s)
## 3. Analysis
## 4. Conclusion
## 5. References
---
Prepared by: [Name] | Reviewed by: _________
```

### Comparison Table

```
| Criteria | Option A | Option B |
|----------|----------|----------|
**Recommendation:** [Which option and why]
```

## Best Practices

1. **Always cite sources** — reference specific sections of Acts, standards, or rulings
2. **Note effective dates** — tax rates and rules change with budget cycles
3. **Distinguish law vs practice** — what the Act says vs how it is applied in practice
4. **Flag uncertainties** — if an area is ambiguous, say so explicitly
5. **Use authoritative sources first** — legislation > public rulings > textbooks > articles
6. **Cross-check web data** — verify online information against authoritative sources
7. **Date-stamp research** — regulatory environment changes; always note when research was conducted
