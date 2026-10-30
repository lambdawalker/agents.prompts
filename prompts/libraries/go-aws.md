# Go + AWS libraries

Use this section with the selected primary documentation workflow and [shared library rules](shared.md). Apply Go-specific rules to ordinary Go modules and AWS-specific rules only where the module actually integrates with AWS or infrastructure.

## Standalone or part of a larger project

- Standalone: keep Go package/API guidance, module installation, complete examples, tests, configuration, and operational prerequisites with the module.
- Within a larger project: retain the library's contracts and link to host services, architecture, deployment ownership, and environment flows. Distinguish local workspace/replace directives from released module versions. If a library also owns deployable infrastructure, make that role explicit rather than describing every Go package as a service.

## Go contracts and installation

Document supported Go versions, module import paths, semantic import version suffixes where applicable, exported APIs, context cancellation/deadlines, goroutine ownership, concurrency guarantees, errors, retries, resource cleanup, and testable interfaces. Show complete examples and exact build/test commands. Keep package documentation and human/agent guides consistent. Derive release selection from the project's verified distribution policy; a Go library may use confirmed VCS-backed module distribution rather than a package upload workflow.

## AWS boundaries and operations

Inventory AWS services actually used and describe their safe public configuration: service purpose, region/environment behavior, resource relationships, IAM role/policy intent, request/event schemas, limits, retries, idempotency, queues/DLQs, retention, logs, and recovery where relevant. Separate deploy-time inputs from runtime configuration and consumer responsibilities.

Use placeholders for secrets, credentials, account-specific identifiers, private endpoints, and sensitive operational values. Explain supported credential providers/roles and least-privilege requirements without publishing credentials. Document mock/local testing versus integration tests against real AWS. Do not provision, reset, delete resources, send messages, or incur cloud charges merely to verify documentation; use the user's explicitly authorized infrastructure scope.

If infrastructure/deployment tooling exists, document environment selection, state/secrets backend, preview/plan versus apply, DNS/email verification where present, outputs, locks, partial failures, and recovery. Preserve confirmed release metadata independently of deployment status. Do not introduce a deployment wizard or mandate an IaC product for an ordinary library.

## Documentation and visuals

Show exact API and infrastructure diagrams where they improve understanding; use real CLI/admin/demo screenshots only where available and useful, with sensitive data removed and provenance recorded. Headless packages need no artificial screenshot suite. Land on the latest confirmed stable module release, keep Development selectable, and retain coherent links between package docs, shared project operations, and parent architecture.
