# Privacy Policy

**Effective Date:** April 10, 2026
**Last Updated:** April 10, 2026

## Overview

Worker K is a collection of Claude Code skills that process data entirely on your local machine. We do not operate any servers that receive, store, or process your data.

## Data Collection

**Worker K does not collect any data.** Specifically:

- No telemetry or usage analytics
- No crash reports or error logging to external servers
- No user identification or tracking
- No data transmission to AiTraining2U or any third party

## Data Processing

All data processing happens locally on your machine:

- **Excel, Word, PDF skills** use Python libraries (openpyxl, python-docx, pdfplumber, etc.) that run entirely on your local system
- **Web browsing skills** use Playwright running locally on your machine; no browsing data is transmitted externally
- **Research skills** use Claude's built-in web search capabilities; these are governed by Anthropic's privacy policy
- **File outputs** are saved to your local filesystem only

## Third-Party Services

Worker K skills may interact with third-party services when you explicitly request it:

- **Web browsing**: Visits URLs you specify using your local Playwright installation
- **Claude Code**: The skills run within Claude Code, governed by [Anthropic's Privacy Policy](https://www.anthropic.com/privacy)

Worker K does not independently connect to any external services without your explicit instruction.

## Data Storage

Worker K does not maintain any database, cache, or persistent storage. All processing is ephemeral within your Claude Code session.

## Your Rights

Since Worker K does not collect or store any personal data, there is no personal data to access, correct, or delete. You have full control over all files processed by Worker K skills, as they reside entirely on your machine.

## Security

- Skills contain no obfuscated or encoded instructions
- All skill definitions are human-readable markdown files
- No credentials are stored or transmitted by the plugin
- Skills never enter payment information or access financial accounts

## Changes to This Policy

We will update this policy if our data practices change. Updates will be reflected in the CHANGELOG.md and the "Last Updated" date above.

## Contact

For privacy-related questions or concerns:

- GitHub Issues: https://github.com/WeiKhjan/worker-k/issues
- Email: privacy@aitomate.it.com
- Website: https://workerk.aitomate.it.com/privacy
