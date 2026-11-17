# Go + AWS composed application repository

Use this prompt to **design**, **implement**, or **align** an application that composes independently maintained features, such as Attestra. Start by reading [common Go + AWS requirements](go-aws-common.md); they define task modes, discovery, evidence, migration, deployment and delivery. Give the agent the application repository, requested mode, feature repositories and existing architecture location when known.

## Composition and ownership

- Use verified public Composer and feature APIs. Declare a feature/dependency inventory with pinned versions, required capabilities, configuration, outputs and compatibility evidence. Build a dependency graph, detect cycles/missing capabilities and validate bindings before deployment. Do not copy feature implementation into the application or rely on another module's internal packages.
- The application owns composition, host policy, routing, authentication/user lifecycle and integration adapters as appropriate. Features retain ownership of their implementations, contracts and native inventories. Document exceptions explicitly; do not relabel feature resources as application-owned in aggregate reports.
- Classify every resource as application-owned, feature-owned or externally supplied. Assign shared API, identity, DNS, email identity, storage and observability responsibilities explicitly. Create shared resources once and pass typed references to dependents; never infer safe reuse or ownership from names alone.
- Specify cross-feature trust, authentication/authorization propagation, event/API schemas, correlation, retries, timeouts and failure handling. Preserve feature isolation while defining the host's end-to-end invariants. Document version compatibility and coordinated upgrades without requiring all features to share a release number.

## Application deployment

Apply all applicable common deployment requirements. Model shared/admin/index and environment-specific backends as separate targets when they exist. Offer **Admin/index**, **Backend**, and **All** with explicit selected environments in both local tooling and Actions. If no admin/index exists, omit it and explain applicability; do not manufacture a dashboard just to satisfy a menu.

All deploys shared resources once before selected dependents; it never silently expands to production or every environment. Backend-only verifies required existing shared outputs. Admin-only avoids irrelevant backend prompts. Bind each operation to its own protected environment, state identity and saved plan. Coordinate shared locks and serialize competing writes while allowing bounded independent builds.

Validate shared outputs for identity, version and readiness before binding them. A partial deployment must report each target's outcome and prevent dependent work from consuming failed or stale outputs. Document compatible mixed-version states, rollout order, recovery, and any deliberate resource adoption/migration. Treat teardown as a dependency-aware, deliberately confirmed operation; preserve externally owned resources and retention obligations.

## Reporting and acceptance

Aggregate native feature inventories through Composer's report model; retain exact creating owners, hierarchy, evidence age and unknown/partial states. Add application-owned integrations and shared scope. Publish an application UI/report only when part of the product; keep private deployment evidence out of public assets. Feature repositories must not acquire dashboard dependencies to contribute inventory.

| Layer | Required acceptance |
| --- | --- |
| Composition | Real supported feature APIs compile; compatible versions and typed bindings verified; missing capabilities/cycles rejected. |
| Ownership and isolation | Shared resources instantiated once; external resources untouched; selected environments and component scopes do not cross-contaminate state/configuration. |
| Deployment orchestration | Local/CI routing, saved-plan binding, dependency ordering, protection/concurrency and interruption recovery verified without unauthorized live mutation. |
| Application behavior | End-to-end scenarios cover cross-feature success, authorization failure, retries/duplicates, partial failures and recovery. Separate mocked evidence from authorized live acceptance. |
| Operations | Aggregated reports preserve ownership and evidence; health/readiness distinguish provisioned, configured and accepted for each target. |

## Design and documentation deliverables

Keep the README concise and link focused composition, contracts, security, deployment/recovery and acceptance documents. Reuse the established parent architecture location and link reciprocally to feature documentation; do not duplicate feature API manuals. Apply the [system-integrated documentation workflow](../system-integrated-library-documentation.md) when working on the documentation site, scoped to actual modules and application content.

Provide a feature/version/capability table, an AWS artifact table grouped by category with a short purpose paragraph and lifecycle owner, and an environment/component deployment matrix. Include compact, validated Mermaid diagrams for composition/ownership, dependency deployment order, and one important cross-feature request/event flow with failure boundaries. Distinguish current behavior, approved design and proposals.

Finish with the common requirement matrix, staged milestones for design work, and independent provisioned/configured/accepted evidence for implementation. Successful feature unit tests alone do not prove the composed application works.
