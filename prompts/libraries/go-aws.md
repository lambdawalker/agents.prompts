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

## Deployment wizard requirements for deployable Go + AWS projects

Apply this section when the requested project owns AWS infrastructure or deployment orchestration. Use Pulumi with an S3 state backend for new deployment tooling in this scope. An ordinary Go library needs no deployment wizard. A documentation-only task must describe the implemented behavior and report gaps; it does not silently authorize changing infrastructure tooling or running deployments.

### Portable entrypoints and environments

- Provide repository-root `deploy.sh` and `deploy.bat` launchers for the same Go deployment program. Resolve the repository root independently of the caller's working directory, preserve quoted/empty arguments and exit status, and keep platform-specific behavior in the thin launchers.
- Support first setup, build, preview, deploy, status/health, reconfiguration, and recovery. Build the actual deployable artifacts before preview/apply. Check required tool versions and give actionable prerequisite errors.
- Ask for the environment first. Offer discovered/saved environments, including dev, qa and prod where configured, and allow validated custom names. Keep configuration, state, outputs, credentials scope and recovery receipts isolated by project/environment. Show account, region, environment, stack, backend and hostnames before any mutation.
- When the architecture has an admin/index side, explicitly ask what to deploy: **Admin/index**, **Backend**, or **All**. For Backend or All, select the backend environment (or an explicit list of environments) and display the complete target list. All means admin/index plus the selected backend environments; never silently expand to every environment or production. For admin/index-only operations, use its shared configuration without asking irrelevant backend questions. Remember the scope/environment choices as defaults while keeping the selected targets visible. Deploy shared resources once, before dependent backends, and validate shared outputs before wiring them.
- Where parallel deployments use the same base domain, support a saved configurable prefix with a visible default. Prefix deployment identity/state and application subdomains consistently. An explicitly empty prefix selects the final identity; it must not silently adopt or rename legacy resources. Keep email sender/verification domains unprefixed when reusing an existing SES identity.

### AWS authentication

Use AWS IAM Identity Center (SSO) for interactive deployments. Select and remember the existing AWS CLI profile, check the session, and invoke `aws sso login --profile <profile>` when it expires or is missing. Verify STS account/role and region against the selected target before continuing. Do not repeatedly ask for credentials when the session is valid, solicit long-lived access keys as the normal path, or copy SSO cache credentials into the project's vault. In CI, use OIDC/assumed roles and explicit inputs; never start interactive SSO or rely on a workstation session.

### S3 state and passphrases

- Require an S3-backed Pulumi state location (`s3://bucket/dedicated-prefix`), with distinct project/environment stacks and collision-safe state paths. Explain that Pulumi maintains state objects in the bucket; the operator does not manually edit a single state file.
- Select an existing bucket or provide a separately identified, explicitly authorized bootstrap flow for a new one. Verify ownership/account, region, versioning, encryption at rest, public-access blocking and required access. Never treat a missing stack as permission to initialize over existing infrastructure.
- On first setup, capture the passphrase used to encrypt Pulumi configuration secrets with hidden input and confirmation when creating a new one. For an existing stack, unlock with its existing passphrase; do not replace it. Configure Pulumi's passphrase secrets provider and pass secrets through protected environment/file/stdin mechanisms, never command-line values or logs.
- Distinguish S3 server-side encryption, Pulumi secret encryption and the local credential vault. The Pulumi passphrase does not authenticate to S3 or encrypt every field in state. Protect state access with AWS IAM; do not describe S3 state as containing only public values.
- Restore authoritative encrypted stack configuration and secrets-provider metadata on a fresh checkout before changing settings. Preserve existing secret values and generated proof/signing keys across reruns. Missing local YAML is not permission to regenerate secrets or rotate keys.

### Encrypted local store and remembered answers

Create an encrypted local secret store on first setup, under a private operator directory such as `~/.attestra/<project>/<environment>/` or the platform configuration directory. Use established authenticated encryption and a password-based KDF with random salt/nonces and a versioned format. Capture/confirm its unlock passphrase without echo; never store that unlock passphrase alongside the vault. Scope vaults explicitly and provide deliberate credential update/rotation and recovery instructions. Do not invent a custom cipher.

Persist deployment secrets such as DNS tokens and, when selected, the Pulumi passphrase only inside this encrypted store. Unlock once per run and reuse in memory; a vault unlock can still be required on subsequent runs. An incorrect passphrase or corrupt vault must fail without overwriting it. Keep plaintext secrets out of Git, nonsecret settings, receipts, exports, logs and artifact uploads. Use private permissions/ACLs and atomic writes.

Save nonsecret answers separately with a versioned schema. After first setup, load them before prompting and skip questions with valid saved answers; ask only for missing/invalid values, explicit reconfiguration, changed targets or necessary secret unlocking. Use precedence: explicit flags, current project/environment settings, compatible imported values, then defaults. Report which configuration source was used. Changing environment must load that environment's values before prompting.

Support an allowlisted export/import workflow when migrating from an existing deployment. Reuse portable values without transferring legacy ownership or leaking secrets. Do not rename encrypted vault files to bypass repository/environment scope; any secret migration needs an explicit compatible decrypt/re-encrypt workflow.

### Preview and five-second deployment confirmation

For an interactive invocation that explicitly selects deploy, complete prerequisite/identity checks, build and a successful Pulumi preview first. Display the exact target and preview summary, then ask:

> Deploy the previewed changes? [Y/n] Continuing in 5 seconds; press n or Ctrl+C to cancel.

Show a visible five-second countdown. Yes or Enter proceeds immediately; no, Escape or Ctrl+C cancels. If the user gives no input for five seconds, proceed with that previewed deployment automatically. Do not restart the timer for each resource or repeat configuration questions. Treat EOF, a closed terminal or an input error as cancellation, not timer expiry. Avoid leaked reader goroutines or buffered input accidentally confirming a later step.

The timer must never turn preview/status/build into deploy, apply an unsuccessful/stale preview, or authorize a changed account/environment/plan. Noninteractive execution requires an explicit deployment flag/approval and complete inputs; it must not use silence or EOF as approval. Teardown, reset, state removal and destructive replacement/deletion require a separate explicit confirmation with no automatic timeout. Document these behaviors, including the default-to-proceed countdown.

### GitHub deployment setup

Include GitHub deployment configuration in the deployment wizard, so the same project can deploy locally or through GitHub Actions. Ask whether to configure/update GitHub deployment support for the selected targets. When admin/index exists, offer its GitHub Action and GitHub environment setup independently, alongside backend environment setup or all selected components. Allow setup-only/reconfigure runs without deploying AWS resources, and allow local deployment when GitHub setup is skipped. Remember completed setup and reconcile only missing/changed configuration on later runs.

- Resolve and verify the target repository and authenticated GitHub identity before writing configuration. Explain required permissions; use the existing GitHub CLI/session or supported API integration. Handle unavailable environment administration permissions with exact manual steps and an incomplete-setup report, rather than claiming success.
- Create or reconcile GitHub environments for admin/index and each selected backend environment, with explicit component/environment mappings. Keep shared settings separate from backend-specific settings; preserve existing secrets, protection rules, required reviewers and branch restrictions. Do not overwrite unrelated configuration or grant broader access.
- Configure environment variables for nonsecret inputs such as AWS account/region, role ARN, S3 state URL, deployment prefix, component scope, domains and reviewed settings. Transfer required deployment secrets (for example Pulumi passphrase and DNS token) from the unlocked local store through protected GitHub secret APIs/CLI stdin. Never commit plaintext secrets, upload the vault or print secret values. Because stored GitHub secrets cannot be read back, track names/setup metadata without inventing value verification; support explicit replacement/rotation.
- Configure AWS OIDC trust and a least-privilege deployment role scoped to the actual repository and GitHub environment. Reuse a compatible existing provider/role where appropriate. Show IAM/trust changes before applying authorized setup. Do not put long-lived AWS access keys or local SSO tokens in GitHub secrets.
- Add a manual `workflow_dispatch` deployment Action with scope choices **Admin/index**, **Backend**, and **All**, plus backend environment selection. Use separate workflows/jobs if that makes component ownership clearer; the operator must still have equivalent choices. Validate inputs and route jobs to the matching protected GitHub environments. An All run deploys shared once and orders selected backends after it; backend-only can reuse verified existing shared outputs.
- Have Actions call the same Go deployment program and configuration schema as local launchers, using explicit noninteractive flags and approval. Use OIDC, minimal workflow permissions, appropriate concurrency groups for shared/identity locks, and environment protection rules. The local five-second interactive countdown must not run in CI. Fail early on absent required variables/secrets; do not substitute first-run defaults for omitted saved settings.
- Provide first-run and rerun instructions, the workflow/environment links, required variables/secret names, and a setup summary listing completed/skipped/incomplete components. Verify workflow syntax, scope/environment routing, shared-first ordering, concurrency and credential handling without triggering an actual deployment merely to validate setup. Configuring Actions does not itself authorize dispatching a deployment.

### Recovery, DNS and verification

Use Pulumi's state locking and additional operation coordination where shared registry/DNS work requires it. Fail clearly on concurrent/stale locks; never automatically delete locks or steal an active deployment. Preserve durable checkpoints for partial AWS, certificate, DNS, verification and publication work so retries reconcile ownership and resume unfinished work without duplicating resources or rotating secrets.

Where DNS/SES are involved, remember the DNS provider and zone, use least-privilege scoped tokens, detect conflicting records and distinguish externally owned identities/certificates from managed resources. Explain staged deployment and readiness waits. Do not publish an endpoint as ready before required DNS/TLS and bootstrap work completes. Preserve shared email identity/DNS ownership before retiring legacy infrastructure.

Export useful nonsecret URLs/client configuration and provide health checks plus a live acceptance checklist. Document backup/recovery of state, vault and passphrases, and the exact rerun command after interruption. Verify launcher behavior, environment isolation, saved-answer precedence, secret handling, expired SSO, state restoration, partial retries, and confirmation yes/no/timeout/EOF/cancellation with fake clocks and mocked cloud calls. Do not run a real deployment merely to test the wizard.

## Documentation and visuals

Show exact API and infrastructure diagrams where they improve understanding; use real CLI/admin/demo screenshots only where available and useful, with sensitive data removed and provenance recorded. Headless packages need no artificial screenshot suite. Land on the latest confirmed stable module release, keep Development selectable, and retain coherent links between package docs, shared project operations, and parent architecture.
