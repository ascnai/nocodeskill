# Changelog

All notable changes to this repository are documented here.

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
