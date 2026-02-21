# Security Guidance

## Current Maturity

This skill is intended for product validation and controlled environments.

## Operational Safety Requirements

- Operators MUST treat this skill as non-production unless platform authorization controls are enabled upstream.
- Operators MUST not include plaintext credentials in workflow payloads.
- Operators MUST use workspace secrets references (`={{ $secrets.<name> }}`) for sensitive values.
- Operators MUST include mutation intent and affected workflow identifiers in every completion summary.

## Disclosure

If you find unsafe guidance in this repository:

1. Open a private security report with reproduction details.
2. Include impacted sections/files and expected safe behavior.
3. Do not publish exploit details before remediation.
