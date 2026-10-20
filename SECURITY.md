# Security Policy

## Reporting Vulnerabilities

If you discover a security vulnerability in Worker K, please report it responsibly:

1. **GitHub Security Advisories** (preferred): https://github.com/WeiKhjan/worker-k/security/advisories/new
2. **Email**: security@aitomate.it.com

Please include:
- Description of the vulnerability
- Steps to reproduce
- Potential impact
- Suggested fix (if any)

## Response Timeline

- **Acknowledgment**: Within 48 hours
- **Assessment**: Within 7 days
- **Fix**: Within 30 days for confirmed vulnerabilities

## Scope

Worker K is a set of Claude Code skill definitions (markdown files). The security scope includes:

- Skill instructions that could lead to unintended data exposure
- Instructions that could bypass Claude Code's safety mechanisms
- Hardcoded credentials, tokens, or sensitive information

## Security Design Principles

- **Local-only processing**: All data stays on the user's machine
- **No credential storage**: Skills never store passwords, tokens, or API keys
- **No payment access**: Skills never interact with payment systems
- **No obfuscated code**: All instructions are human-readable markdown
- **Minimal dependencies**: Skills use standard Python libraries only
- **Explicit user control**: Skills only act on user-provided instructions

## Supported Versions

| Version | Supported |
|---------|-----------|
| 1.0.x   | Yes       |
