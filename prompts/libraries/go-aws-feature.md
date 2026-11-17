# Go + AWS feature repository

Use this prompt to **design**, **implement**, or **align** a reusable feature such as email verification, signature verification or card capture. Start by reading [common Go + AWS requirements](go-aws-common.md); they define task modes, discovery, evidence, migration, deployment and delivery. Give the agent the target repository, requested mode and any host repositories. Existing repositories default to alignment only when the request asks to fix/align them.

## Boundaries and public contracts

- Keep domain/runtime behavior independent of a particular host. Publish explicit inputs, outputs, errors, cancellation, concurrency, retry/idempotency and security contracts. Identify which behavior the feature owns and which decisions belong to its consumer.
- Implement the infrastructure module against verified public Composer contracts. Declare required capabilities and typed inputs/outputs; validate missing or incompatible bindings before resource creation. State which AWS resources the feature creates and which it receives from the host. Avoid imports from application implementation packages, host-specific environment names, fixed account IDs or hidden global configuration.
- Separate runtime, infrastructure integration and standalone test host. Host-specific handlers/wiring belong in `internal` or equivalent nonpublic packages. A test host must consume the same supported feature interfaces as a real composed app; it must not bypass the public API to make tests pass.
- Declare default ownership and configurable reuse for resources such as API routes, authentication, tables, queues, keys, DNS and SES identities. Reuse is explicit and must not transfer lifecycle ownership. The host owns account/user lifecycle and application policy unless explicitly part of the feature contract.
- Pin compatible dependencies and provide a small consumer integration example that builds against the supported public API. Record semantic-version and migration implications of contract changes. Distinguish released module versions from local workspace/replace experiments.

## Standalone deployment and reporting

A deployable feature test app uses the common deployment tooling, isolated scope/state and mock or synthetic fixtures. It needs no application dashboard, admin/index component or cross-feature registry merely to exercise a library. Expose only applicable component choices in the wizard and Actions; explain N/A entries in the matrix.

Inventory feature-owned resources and test-host-owned adapters separately, preserving their actual creating repository. Host-provided resources remain external. Supply Composer-compatible inventory for aggregation and standalone JSON/Markdown/HTML reports without importing the host. Document the test host's authentication, allowed test identities, health checks, live acceptance scenario, cleanup and retained data. Test tooling must not send real emails, process real cards or provision AWS merely as part of local verification.

## Acceptance by layer

| Layer | Required acceptance |
| --- | --- |
| Runtime/library | Public consumer example compiles; domain success/failure, authorization boundaries, cancellation, retries and idempotency tested as applicable. |
| Infrastructure module | Supported Composer integration builds; required bindings validated; owned/external resource boundaries, IAM intent and safe reporting verified with appropriate mocks or preview fixtures. |
| Isolated test host | Uses the public contract; isolated configuration/state; portable build and deployment workflow verified without live mutation; documented end-to-end scenario with honest live status. |
| Composition | A minimal host consumes the supported outputs/capabilities without host imports in the feature or duplicated shared infrastructure. Record supported dependency versions. |

## Design and documentation deliverables

Keep a short README entry and linked focused documents; split architecture, contracts, security, deployment/recovery and acceptance when their size warrants it. Choose the [standalone documentation workflow](../standalone-library-documentation.md) or [system-integrated workflow](../system-integrated-library-documentation.md) for documentation work; link to established parent architecture without moving the feature's API ownership there.

Include an AWS artifact table grouped by category, with resource, owner, lifecycle, dependencies and a short purpose paragraph. Include compact Mermaid diagrams for feature/host boundaries, the main request or event sequence, and meaningful lifecycle/failure states. Show trust boundaries and distinguish planned resources from implemented ones. Validate Mermaid syntax/rendering and relative links; do not treat a fenced block as proof it renders. Keep labels simple and avoid raw semicolons in sequence messages that may be parsed as statement separators.

Record feature-specific security decisions: trust/authorization, token or proof lifecycle, replay/attempt limits, sensitive-data handling, retention/deletion and any legal-hold behavior that is actually required. Do not impose email-verification or legal-hold semantics on unrelated features. Mark unresolved product requirements explicitly.

Finish with the common requirement matrix and separate readiness evidence for runtime, module and test host. For design-only work, deliver the specification and implementation milestones rather than claiming the feature runs.
