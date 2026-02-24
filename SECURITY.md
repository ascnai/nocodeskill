# Security Guidance

## Maturity

Skills in this repository are intended for controlled environments unless explicitly marked production-ready.

## Baseline Requirements

- Do not include plaintext credentials in skill examples or payload templates.
- Reference secrets through runtime secret stores.
- Define explicit stop conditions for dependency or auth failures.
- Require explicit user confirmation for destructive operations.

## Disclosure

If you find unsafe guidance:

1. Open a private security report with reproduction steps.
2. Include impacted files/sections and expected safe behavior.
3. Avoid public disclosure until remediation.
