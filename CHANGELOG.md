# Changelog

All notable changes to this repository are documented here.

## [1.3.1] - 2026-02-21

### Added

- Explicit node-reference syntax guidance with concrete examples:
  - `={{ $json.field }}`
  - `={{ $node['id'].json.field }}`
  - `={{ $secrets.secret_name }}`
- Contract-level `data_reference_policy` in `contracts/skill-contract.yaml`.
- Quick reference examples in `references/workflow-construction.md`.

### Changed

- `SKILL.md` now has a dedicated required section for node-reference syntax and anti-patterns.

## [1.3.0] - 2026-02-21

### Added

- Required post-export testability flow:
  - smoke invoke exported tool
  - inspect latest runs with `control.runs.list`
- Runtime failure output requirements for `run_trace` and latest run diagnostics.
- Audit requirement for `trace_id` in skill contract observability section.

### Changed

- `SKILL.md` now requires `control.runs.list` in tool surface.
- `SKILL.md` export intent flow now includes smoke-test and run-trace verification.
- `contracts/skill-contract.yaml` upgraded to `1.3.0` with new required tool and export verification sequence.
- `README.md` now documents runtime traceability expectations.

## [1.2.1] - 2026-02-21

### Added

- Standardized user-facing decision templates for capability-gap options:
  - compose existing capabilities
  - connect external MCP tool
  - build new reusable integration
- Contract-level `decision_message_templates` under capability-gap recovery.
- Canonical full user-response examples for all 3 decision paths in `references/integration-proposals.md`.

### Changed

- `SKILL.md` now includes reusable decision-response templates.
- Integration proposal schema version bumped to `1.2.1`.

## [1.2.0] - 2026-02-21

### Added

- Capability-gap policy with reuse-first strategy and user decision gate.
- `contracts/integration-proposal-schema.yaml` for reusable Integration Proposal Card.
- Scenario `contracts/scenarios/missing-capability-propose-integration.yaml`.
- Reference `references/integration-proposals.md`.

### Changed

- `SKILL.md` now requires capability-gap classification and integration proposal output.
- `contracts/skill-contract.yaml` now includes capability-gap recovery and decision options.
- `contracts/error-taxonomy.yaml` now includes `capability_gap` class and error codes.
- `agents/openai.yaml` now includes capability-gap playbook behavior.

## [1.1.0] - 2026-02-21

### Added

- Formal dependency-failure handling for disconnected MCP gateway:
  - `E_DEPENDENCY_MCP_NOT_CONNECTED`
  - `E_DEPENDENCY_REQUIRED_TOOL_MISSING`
- User-facing MCP connection runbook and message template in skill and troubleshooting docs.
- Contract-level dependency handshake and recovery schema.
- Scenario `contracts/scenarios/dependency-mcp-not-connected.yaml`.

### Changed

- `SKILL.md` now requires dependency readiness checks before lifecycle mutations.
- `agents/openai.yaml` now includes explicit dependency-failure behavior and playbook reference.

## [1.0.0] - 2026-02-21

### Added

- Contract-first governance package:
  - `contracts/skill-contract.yaml`
  - `contracts/error-taxonomy.yaml`
  - `contracts/scenarios/*.yaml`
- Standards and governance docs:
  - `README.md`, `CONTRIBUTING.md`, `SECURITY.md`, `VERSION`
- Strengthened `SKILL.md` with deterministic execution policy, retry model, and output contract.

### Changed

- Updated agent profile in `agents/openai.yaml` to align with strict invocation and safety expectations.
- Expanded references for construction and troubleshooting to align with contract taxonomy.
