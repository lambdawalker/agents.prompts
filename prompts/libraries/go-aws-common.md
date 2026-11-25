# Common Go + AWS requirements

Read this file from either the [feature entry point](go-aws-feature.md) or [composed application entry point](go-aws-application.md). This is the shared execution, Go, AWS and deployment contract; role-specific requirements live in those files. Read each linked dependency once. Apply [shared library rules](shared.md) for cross-language documentation, deployment interaction, records, reporting and delivery. For application-only repositories, module publication/version navigation rules apply only to actual published modules; do not invent a library release.

## Task mode and discovery

1. Identify the requested mode: **design** produces a concrete specification and acceptance plan; **implement** builds the requested design; **align** audits and fixes an existing repository. An explicit audit-only or documentation-only request limits work to that scope. If no mode is stated, infer it from the request and state the assumption. Do not turn a design request into runtime implementation or stop an alignment request at an audit.
2. Read README, applicable repository guidance, linked architecture/decisions, source, dependency versions, deployment programs, workflows, tests, and relevant open branches/PRs. Establish the actual current behavior before proposing changes. Inspect existing deployments only through authorized access. Missing access is unknown, not evidence of absence.
3. Record approved requirements, proposed defaults and unresolved decisions separately. Resolve routine choices autonomously; ask only for decisions that block correct work. Keep assumptions visible. Example repositories provide implementation ideas, not proof of compliance. Current task instructions and applicable repository constraints govern; surface genuine conflicts rather than silently weakening requirements.
4. Create a concise plan and requirement matrix before edits, then update them with final evidence. Preserve existing useful behavior and active work. Do not copy an example wizard and declare alignment without checking every applicable requirement here and in the role prompt.

## Completion and evidence

Store a concise matrix in the target's documentation, with one row per independently verifiable applicable requirement. Split multi-part requirements when their status differs. Use these columns:

| Requirement ID and source section | Role/target | Status | Implementation or design reference | Validation evidence | Remaining work |
| --- | --- | --- | --- | --- | --- |
| Stable local ID + linked requirement | Feature, test host, application or shared tooling | Implemented / Partial / Missing / Not applicable | File and symbol or design section | Command/result or acceptance scenario, with scope/revision | Concrete gap or N/A rationale |

For design-only work, label the matrix **design coverage** and use **Specified / Partial / Missing / Not applicable**; specified is never implemented. A requirement is implemented only with inspectable code/configuration and appropriate verification. A manual workaround is partial when automation is required. No evidence means unverified, recorded in validation and remaining work. Do not hide unfinished requirements under N/A or mark all deployment requirements complete because compilation passed.

Implementation delivery includes behavior changes, tested commands/results, unresolved gaps, migration/recovery guidance, and PR links. A design delivery includes boundaries, interfaces, AWS inventory, diagrams, decisions, milestones and executable acceptance scenarios. Follow the shared branch/PR delivery policy. Implementation authorization does not itself authorize cloud deployment, IAM/DNS changes, GitHub account configuration, message/email sending, publication or merging. Implement and test tooling without exercising those mutations unless authorized.

## Composer compatibility and shared tooling

Use [go.infra.composer](https://github.com/lambdawalker/go.infra.composer) as the composition base for new AWS features and composed applications in this family. Inspect its current public contracts, supported versions, examples and reporting adapters before designing an integration. Record the exact dependency revision/version and compatibility evidence. Do not invent an API based on repository names or claim compatibility from a dependency declaration alone.

Keep generic deployment behavior in reusable Go tooling in or alongside Composer: prompts, scoped records/vaults, authentication, state bootstrap, saved-plan execution, GitHub reconciliation, caches, recovery and reports. Repositories should supply typed declarations, resource factories, build targets, configuration schemas and health/acceptance hooks through supported public extension points. Keep root launchers thin and local/CI execution on the same implementation. Pin shared tooling versions and document upgrades.

Inspect existing shared capabilities before adding abstractions. When a capability is missing, document the gap and a minimal extension contract; implement upstream only within authorized repository scope. A temporary local adapter must have an explicit boundary, tests and a removal/migration path; do not silently fork a full deployment wizard or claim an upstream release exists. Ordinary Go libraries without infrastructure do not need Composer or deployment tooling.

## Existing deployment adoption and migration

Before altering deployment tooling, inventory established project/stack identity, backend bucket/prefix, region/account, resource logical and physical IDs, domains, ownership, encrypted configuration, secrets provider and existing record locations. Use safe references rather than copying sensitive state into documentation. Unknown identities block adoption, not unrelated code work.

Preserve these identities and existing secret values by default. Renaming a project, choosing an empty prefix, moving records or changing module wiring must not silently create a replacement stack or duplicate resources. Describe any deliberate migration, prerequisites, rollback/roll-forward limits and exact recovery commands. Rehearse against fixtures/mocks or an authorized isolated target; review preview replacements/deletions explicitly. Keep recovery copies and only mark migration complete after verifying the new records against the same target. Existing physical infrastructure remains owned by its established stack until an explicit supported ownership transfer completes.

## Readiness and failure recovery

Report these dimensions independently for each target:

| Dimension | Required evidence |
| --- | --- |
| Provisioned | Successful scoped apply and resource/output observations; a preview is only planned evidence. |
| Configured | Required secrets/configuration, integrations, DNS/TLS/email identity and bootstrap readiness verified where applicable; unavailable checks remain unknown. |
| Accepted | Authorized live end-to-end scenario succeeds through the actual public interface, including failure behavior and cleanup. Local/mocked tests do not prove this. |

Record revision, target, time and evidence source. Provisioned is not synonymous with usable. Classify checks as local/static, mocked integration, read-only live, or mutating live. Supply a live checklist when execution is not authorized and say **not run**. Avoid logging proof tokens, personal data or secret values.

Define recovery at each boundary: state bucket created, stack initialized, encrypted configuration restored, artifacts built, plan saved, partial apply, partial GitHub environment/OIDC setup, and DNS/verification pending. Persist scoped receipts after confirmed steps, never a blanket success marker after partial completion. Retries reconcile authoritative state, preserve secrets and completed work, and stop dependents on failed prerequisites. Test interruption and retry at these boundaries, including inaccessible state and concurrent writers. Describe whether rollback is possible; do not promise transactional rollback across providers.

## Go contracts and installation

Document supported Go versions, module import paths, semantic import version suffixes where applicable, exported APIs, context cancellation/deadlines, goroutine ownership, concurrency guarantees, errors, retries, resource cleanup, and testable interfaces. Show complete examples and exact build/test commands. Keep package documentation and human/agent guides consistent. Derive release selection from the project's verified distribution policy; a Go library may use confirmed VCS-backed module distribution rather than a package upload workflow.

## AWS boundaries and operations

Inventory AWS services actually used and describe their safe public configuration: service purpose, region/environment behavior, resource relationships, IAM role/policy intent, request/event schemas, limits, retries, idempotency, queues/DLQs, retention, logs, and recovery where relevant. Separate deploy-time inputs from runtime configuration and consumer responsibilities.

Use placeholders for secrets, credentials, account-specific identifiers, private endpoints, and sensitive operational values. Explain supported credential providers/roles and least-privilege requirements without publishing credentials. Document mock/local testing versus integration tests against real AWS. Do not provision, reset, delete resources, send messages, or incur cloud charges merely to verify documentation; use the user's explicitly authorized infrastructure scope.

If infrastructure/deployment tooling exists, document environment selection, state/secrets backend, preview/plan versus apply, DNS/email verification where present, outputs, locks, partial failures, and recovery. Preserve confirmed release metadata independently of deployment status. Do not introduce a deployment wizard or mandate an IaC product for an ordinary library.

## Deployment wizard requirements for deployable Go + AWS projects

Apply this section when the requested project owns AWS infrastructure or deployment orchestration. Use Pulumi with an S3 state backend for new deployment tooling in this scope. An ordinary Go library needs no deployment wizard. A documentation-only task must describe the implemented behavior and report gaps; it does not silently authorize changing infrastructure tooling or running deployments.

Use the pinned [Infra Composer deploy CLI](https://github.com/lambdawalker/go.infra.composer/tree/main/deploycli) for compatible deployments, including standalone feature test hosts. Keep the Pulumi program, declarative `deploy.json`, thin launchers, version pin and GitHub workflow in the consumer. Reuse Composer's terminal and browser workflows, reporting, discovery and validation. Put reusable fixes in Composer, verify them there, then update the consumer pin and verify its normal entrypoints. For multi-stack applications, preserve their existing orchestration and reuse compatible Composer components.

Use the [email-verification test host](https://github.com/lambdawalker/go.apexfission.aws.email-verification/blob/main/deploy.json) as a consumer example for composed settings, artifact purposes and discovery publication. Keep its service names and environment choices specific to that project.

### Interactive defaults and countdowns

Follow the [shared deployment question rules](shared.md#deployment-questions-defaults-and-countdowns). Use **15 seconds** for eligible routine defaults in the terminal and browser. In the browser, use **30 seconds** for a settings form only when the required inputs resolve and validate, the account and existing state are selected, and required provider credentials are available. Leave forms with missing values, failed checks or a state bootstrap choice pending for explicit input.

Terminal mode defaults to Deploy. Browser mode without an explicit action opens the action menu with Preview selected. Its initial timer may select Preview. Returning from an action leaves the menu available for the next selection. Account and apply confirmations in the browser require explicit input. In both interfaces, secret entry and destructive confirmations have no countdown.

### Persistent projects and console presentation

Follow the shared [deployment project identity and persistent records](shared.md#deployment-project-identity-and-persistent-records) and [console wizard presentation](shared.md#console-deployment-wizard-presentation) rules. Ask for project identity first on initial setup; keep central records under `~/.infra-composer/<project_name>/` and the encrypted vault/local configuration under ignored repository-root `.deploy/`. Preserve project/environment/account/region isolation and existing secret values when migrating older layouts.

Use successful remote Pulumi stack discovery to select existing state or automatically initialize genuinely absent state during setup/preview/deploy. A local flag alone is not proof of existence or absence; lookup failures and lost initialized stacks stop for recovery. Keep preview/status/build distinct from apply and retain explicit CI inputs.

Use labeled steps and aligned target summaries. Render the countdown on one line in terminals, with clean completion/cancellation and plain redirected output. Respect `NO_COLOR`, terminal capabilities and Windows compatibility. Offer the terminal/browser choice on supported local desktops, plus `--terminal`, `--server` and `--server --no-open`. Headless and SSH sessions default to the terminal. CI stays noninteractive.

### Browser workspace and settings

- Use Composer's loopback workspace and private session. Keep Actions and Settings available from startup, with editing and submission enabled only during the relevant step. Keep progress and its substeps, Overview, Process output and artifact inspection available as the run advances. Put passphrase, provider sign-in and short confirmation prompts in dialogs where the operator needs to respond.
- Declare field labels, help, validators and reusable compositions in the deploy manifest. Ask for the base ownership domain, then show the resolved service/environment hostname and sender address. For example, `example.com`, `dev` and `verify` can resolve to `verify@dev.components.<service>.example.com`. Keep the sender and test recipient distinct, accept an explicit valid full sender address, and preserve compatible saved values. Validate as the operator edits and recheck on Continue. Return to the form with nonsecret edits intact and actionable field errors when AWS profile, account or state checks fail.
- Keep the action bar visible while scrolling. Show the timer in Continue with a Pause button beside it, and pause on interaction or when leaving the tab. After success, failure or cancellation, keep the result reviewable and let the operator return to Actions in the same session once the previous operation releases its locks. Offer copyable diagnostics with the action, stage, exit status and relevant sanitized failures. Exclude secrets, sign-in transcripts, command arguments and raw state or configuration.

### Portable entrypoints and environments

- Provide repository-root `deploy.sh` and `deploy.bat` launchers for the same Go deployment program. Resolve the repository root independently of the caller's working directory, preserve quoted/empty arguments and exit status, and keep platform-specific behavior in the thin launchers.
- Support first setup, build, preview, deploy, status/health, reconfiguration, and recovery. Build the actual deployable artifacts before preview/apply. Check required tool versions and give actionable prerequisite errors.
- After establishing the project identity on first setup, ask for the environment before other target-specific settings. Offer discovered/saved environments, including dev, qa and prod where configured, and allow validated custom names. Keep configuration, state, outputs, credentials scope and recovery receipts isolated by project/environment. Show account, region, environment, stack, backend and hostnames before any mutation.
- When the architecture has an admin/index side, explicitly ask what to deploy: **Admin/index**, **Backend**, or **All**. For Backend or All, select the backend environment (or an explicit list of environments) and display the complete target list. All means admin/index plus the selected backend environments; never silently expand to every environment or production. For admin/index-only operations, use its shared configuration without asking irrelevant backend questions. Remember the scope/environment choices as defaults while keeping the selected targets visible. Deploy shared resources once, before dependent backends, and validate shared outputs before wiring them.
- Where parallel deployments use the same base domain, support a saved configurable prefix with a visible default. Prefix deployment identity/state and application subdomains consistently. An explicitly empty prefix selects the final identity; it must not silently adopt or rename legacy resources. Keep email sender/verification domains unprefixed when reusing an existing SES identity.

### AWS authentication

Use AWS IAM Identity Center (SSO) for interactive deployments. Select and remember the AWS CLI profile and invoke `aws sso login --profile <profile>` when renewal is needed. Open sign-in on supported desktops and offer an **Open AWS sign-in** link in the browser dialog. Keep the manual instructions available when the local browser cannot be opened. Discover the signed-in account and role through STS. If the session needed renewal, wait for STS verification before reporting success. Display account, role, region, environment and stack. Use the 15-second default-yes confirmation in the terminal and explicit confirmation in the browser. Verify explicit/saved target constraints before confirmation, and let the operator correct mismatches in Settings. Keep SSO credentials in their normal provider cache. In CI, use OIDC/assumed roles and explicit inputs without interactive SSO.

### S3 state and passphrases

- Require an S3-backed Pulumi state location (`s3://bucket/dedicated-prefix`), with distinct project/environment stacks and collision-safe state paths. Explain that Pulumi maintains state objects in the bucket; the operator does not manually edit a single state file.
- Reuse a valid saved state location. On first setup, offer an existing S3 location or explicit creation when supported. Show the dedicated prefix, account and region. When creation is authorized, choose a collision-safe name and enable versioning, encryption, public-access blocking and TLS enforcement. Keep bootstrap distinct from stack deployment and save its identity and progress for recovery. Inaccessible or missing previously configured storage requires recovery. CI requires an explicit state location. Verify ownership/account, region, versioning, encryption at rest, public-access blocking and required access. Never treat a missing stack as permission to initialize over existing infrastructure.
- On first setup, capture the passphrase used to encrypt Pulumi configuration secrets with hidden input and confirmation when creating a new one. For an existing stack, unlock with its existing passphrase; do not replace it. Configure Pulumi's passphrase secrets provider and pass secrets through protected environment/file/stdin mechanisms, never command-line values or logs.
- Distinguish S3 server-side encryption, Pulumi secret encryption and the local credential vault. The Pulumi passphrase does not authenticate to S3 or encrypt every field in state. Protect state access with AWS IAM; do not describe S3 state as containing only public values.
- Restore authoritative encrypted stack configuration and secrets-provider metadata on a fresh checkout before changing settings. Preserve existing secret values and generated proof/signing keys across reruns. Missing local YAML is not permission to regenerate secrets or rotate keys.

### Encrypted local store and remembered answers

Create an encrypted local secret store on first setup, under the repository's ignored `.deploy/` directory, scoped by environment and target. Keep nonsecret central deployment records under `~/.infra-composer/<project_name>/` as specified in the shared rules. Use established authenticated encryption and a password-based KDF with random salt/nonces and a versioned format. Capture/confirm its unlock passphrase without echo; never store that unlock passphrase alongside the vault. Scope vaults explicitly and provide deliberate credential update/rotation and recovery instructions. Do not invent a custom cipher.

Persist local deployment secrets such as DNS tokens and, when selected, the Pulumi passphrase inside this encrypted store. Unlock once per terminal run. In the browser, reuse the unlock across actions and lock the interface behind a blocking passphrase dialog after **20 minutes of inactivity**. Resume from the same step after unlocking. In-progress operations can finish while the interface is locked.

Use a Credentials panel with password fields for applicable provider tokens such as Cloudflare and GitHub. A saved credential displays `*****` as a placeholder, a blank submission keeps the saved value, and an entered replacement is stored encrypted after scope verification. Keep the stored value out of browser form state and responses. An incorrect passphrase or corrupt vault must fail without overwriting it. Keep plaintext secrets out of Git, nonsecret settings, receipts, exports, logs and artifact uploads. Use private permissions/ACLs and atomic writes.

Save nonsecret answers separately with a versioned schema. After first setup, load them before prompting and skip questions with valid saved answers; ask only for missing/invalid values, explicit reconfiguration, changed targets or necessary secret unlocking. Use precedence: explicit flags, current project/environment settings, compatible imported values, then defaults. Report which configuration source was used. Changing environment must load that environment's values before prompting.

Support an allowlisted export/import workflow when migrating from an existing deployment. Reuse portable values without transferring legacy ownership or leaking secrets. Do not rename encrypted vault files to bypass repository/environment scope; any secret migration needs an explicit compatible decrypt/re-encrypt workflow.

### Preview, approval and apply

For Deploy, complete prerequisite and identity checks, build the artifacts and save a successful Pulumi plan for the selected stack. Show the exact target, recorded inventory and planned creates, updates, replacements and deletions. Show an empty change summary with the recorded inventory when the plan has no resource changes. A local build fingerprint or cached snapshot cannot substitute for a fresh plan. Preview ends after showing the result.

For a non-destructive plan, use a 15-second default-to-proceed confirmation in the terminal and an explicit **Apply saved plan** action in the browser. CI requires explicit deploy intent, approval and complete inputs. Teardown, reset, state removal and plans with deletion or replacement require separate deliberate confirmation without a timer. EOF, a closed terminal and input errors cancel.

Bind approval to the target, configuration identity, source and built artifacts, and the saved plan digest. Keep the plan in protected local storage. Recheck these bindings immediately before passing the exact saved plan to Pulumi apply. Require a fresh preview and approval if the target, configuration or artifacts change or the plan is missing, stale or tampered with. Test these cases in both local and CI paths.

### GitHub deployment setup

Include GitHub deployment configuration in the deployment wizard, so the same project can deploy locally or through GitHub Actions. Ask whether to configure/update GitHub deployment support for the selected targets. When admin/index exists, offer its GitHub Action and GitHub environment setup independently, alongside backend environment setup or all selected components. Allow setup-only/reconfigure runs without deploying AWS resources, and allow local deployment when GitHub setup is skipped. Remember completed setup and reconcile only missing/changed configuration on later runs.

- Resolve and verify the target repository and authenticated GitHub identity before writing configuration. Explain required permissions; use the existing GitHub CLI/session or supported API integration. Handle unavailable environment administration permissions with exact manual steps and an incomplete-setup report, rather than claiming success.
- Create or reconcile GitHub environments for admin/index and each selected backend environment, with explicit component/environment mappings. Keep shared settings separate from backend-specific settings; preserve existing secrets, protection rules, required reviewers and branch restrictions. Do not overwrite unrelated configuration or grant broader access.
- Configure environment variables for nonsecret inputs such as AWS account/region, role ARN, S3 state URL, deployment prefix, component scope, domains and reviewed settings. Transfer required deployment secrets (for example Pulumi passphrase and DNS token) from the unlocked local store through protected GitHub secret APIs/CLI stdin. Never commit plaintext secrets, upload the vault or print secret values. Because stored GitHub secrets cannot be read back, track names/setup metadata without inventing value verification; support explicit replacement/rotation.
- Configure AWS OIDC trust and a least-privilege deployment role scoped to the actual repository and GitHub environment. Reuse a compatible existing provider/role where appropriate. Show IAM/trust changes before applying authorized setup. Do not put long-lived AWS access keys or local SSO tokens in GitHub secrets.
- Add a manual `workflow_dispatch` deployment Action with explicit component and environment selection. When admin/index exists, provide scope choices **Admin/index**, **Backend**, and **All**, plus backend environment selection; a feature test host without admin/index exposes only its applicable targets. Use separate workflows/jobs if that makes component ownership clearer; the operator must still have equivalent choices. Validate inputs and route jobs to the matching protected GitHub environments. An All run deploys shared once and orders selected backends after it; backend-only can reuse verified existing shared outputs.
- Have Actions call the same pinned Go deployment CLI and configuration schema as local launchers, using explicit noninteractive flags and approval. Use OIDC, minimal workflow permissions, appropriate concurrency groups for shared/identity locks, and environment protection rules. Fail early on absent required variables/secrets; do not substitute first-run defaults for omitted saved settings.
- Provide first-run and rerun instructions, the workflow/environment links, required variables/secret names, and a setup summary listing completed/skipped/incomplete components. Verify workflow syntax, scope/environment routing, shared-first ordering, concurrency and credential handling without triggering an actual deployment merely to validate setup. Configuring Actions does not itself authorize dispatching a deployment.

### Avoid repeated work with fingerprints and reconciliation

Make repeat runs fast and idempotent. Inspect existing resources before creating them, reconcile only managed differences, reuse verified build artifacts, and parallelize independent work. A fingerprint is a change detector and cache key, not proof of ownership or remote readiness.

#### Fingerprint format and success records

- Use SHA-256 over a versioned canonical manifest: sorted field/file names, explicit field boundaries, stable encoding and normalized representations. Prefer canonical JSON or length-delimited records over naive string concatenation, which can collide structurally. Hash actual content, including uncommitted changes; a Git commit ID or modification time alone is insufficient.
- Scope manifests and completion records by repository/project, component, environment, account, region, deployment identity and target architecture as applicable. Include the fingerprint schema and relevant generator/tool versions so behavior changes invalidate old records.
- Keep separate desired-input, artifact-content and successfully-applied fingerprints. Write completion markers atomically only after the corresponding operation and verification succeed. A failed, cancelled or partial operation must not mark the entire target complete. Missing/corrupt records or artifacts are cache misses; provide explicit force-rebuild, force-reconcile and cache-clean controls.
- Never put secret plaintext or ordinary hashes of secret values into public manifests, GitHub variables, logs or resource tags. Low-entropy secrets can be guessed from a plain hash. Use nonsecret secret-version/rotation identifiers, or a private keyed digest inside the encrypted store where needed. Secret names alone detect inventory changes, not changed values or successful secret delivery.

#### GitHub environment reconciliation

Before creating a GitHub environment, query whether it already exists and inspect the configuration the wizard owns. Compute its desired fingerprint from a canonical manifest of repository/environment identity, component mapping, managed nonsecret variable names and values, required secret names and rotation identifiers, OIDC role/trust references, deployment restrictions/protection settings and applicable workflow content or version. Respect independently managed settings.

Store a versioned marker such as `DEPLOY_CONFIG_SHA256` as an environment variable, excluded from its own hash. If the marker matches and readable managed fields, required secret-name inventory and referenced setup still match, skip environment creation and unnecessary update calls. If the marker differs, compute and apply the managed delta; do not recreate the environment or overwrite unrelated settings. Adopt an existing compatible environment through reconciliation rather than assuming it belongs to the wizard.

A matching marker must not hide deleted variables, changed protections, missing secrets or external drift. Readable fields must still be checked. GitHub secret values cannot be read back: retain honest delivery/rotation receipts, support explicit rotation, and never claim that a secret value was verified merely because its name or marker exists. Permission/API failures are unknown state, not a cache hit. Write the marker last, after all required changes and checks succeed; partial setup remains resumable. Serialize concurrent writers to the same environment and recheck before publishing completion.

#### Lambda and Go build caching

Maintain a per-build-target local cache manifest and reusable artifact, rather than rebuilding every Lambda on each deploy. The build-input fingerprint must cover relevant source and transitive local dependency content, embedded assets, generated inputs, dependency/module metadata and replacements, Go/toolchain version, GOOS/GOARCH, build tags, compiler/linker flags, relevant build environment and packaging recipe. Exclude generated outputs, cache directories and irrelevant timestamps from source discovery. Do not reuse an artifact across incompatible toolchains or architectures.

If the input fingerprint matches and the cached binary/archive exists with its recorded SHA-256, reuse it. Otherwise rebuild that target, package it deterministically, verify it and atomically record the new input/artifact fingerprints. Shared entrypoints with identical build inputs may reuse one compiled binary for multiple deployment names; keep function-specific configuration distinct. Reuse Go's own build/module cache as well.

For remote Lambda updates, compare the actual deployment-package digest using AWS/Pulumi's supported code-hash semantics. Distinguish an input hash from the ZIP-byte hash AWS reports. Let Pulumi own resource changes; supply stable artifact hashes rather than bypassing its state with ad hoc update calls. Unchanged code can skip rebuild/upload while changed configuration, IAM, layers, environment variables or infrastructure still receive their own preview/reconciliation.

Apply the same principle to other resources where useful: compare desired managed configuration with authoritative state, preserve ownership, and reconcile drift through the IaC provider. Never skip the entire Pulumi plan/refresh or required remote verification solely because a local hash, resource tag or previous success marker matches. A resource may have changed or disappeared outside the wizard.

#### Bounded parallel work

Represent deployment work as a dependency graph. Build independent Lambda targets and perform independent read-only prerequisite checks concurrently with a configurable worker limit based on available CPU/memory. Avoid oversubscribing Go compilation, exhausting memory, or exceeding API rate limits; use bounded retries/backoff for throttled calls.

Keep shared-resource prerequisites, dependent backend setup, same-stack Pulumi updates, and writes to the same environment/state/receipt serialized. Use operation locks and atomic per-target cache publication so parallel runs cannot consume half-built artifacts or mark another target complete. On failure/cancellation, stop dependent work, clean temporary outputs and preserve successful independent cache entries. Keep logs attributable to targets and print a deterministic summary of reused, rebuilt, unchanged, updated, failed and skipped work.

Test unchanged reruns, source/dependency/toolchain changes, missing/tampered artifacts, remote drift, changed secret inventory/rotation, interrupted updates, concurrent writers, and serial-versus-parallel result equivalence. Verify that unchanged reruns avoid unnecessary creation, compilation and upload while still detecting real changes.

### Recovery, DNS and verification

Use Pulumi's state locking and additional operation coordination where shared registry/DNS work requires it. Fail clearly on concurrent/stale locks; never automatically delete locks or steal an active deployment. Preserve durable checkpoints for partial AWS, certificate, DNS, verification and publication work so retries reconcile ownership and resume unfinished work without duplicating resources or rotating secrets.

Where DNS/SES are involved, persist the selected DNS provider and zone. Carry them into the Pulumi program for related public hostnames, with a deliberate override for separately hosted zones. Resolve the authoritative public zone before registering dependent resources and surface actionable lookup errors. Use least-privilege scoped tokens, detect conflicting records and distinguish externally owned identities/certificates from managed resources. Explain staged deployment and readiness waits. Preserve shared email identity and DNS ownership when retiring legacy infrastructure. For SES, document sender identity verification and recipient requirements in the sandbox.

### Environment discovery and teardown

When clients need environment discovery, reuse Composer's discovery adapter and a separate shared stack. Provision and verify the index before wiring environment deployments to it. Keep the public hostname independent of a specific environment (for example `index.components.<service>.<base-domain>`). Give each deployment role access to its own write route. Publish only allowlisted public connection metadata after successful apply and an appropriate readiness probe. Preserve the prior entry until a successful replacement or confirmed retirement. Preview and setup leave the index unchanged. Record apply and publication outcomes separately so failed publication can be retried safely.

Expose separate removal actions for an environment, all saved environments plus the declared index, and the selected environment's managed GitHub setup where applicable. Show the exact scope and require typed confirmation. Retire each environment from discovery before stack destruction. Verify the existing shared index state before an all-environments teardown and destroy the index only after all selected environment stacks succeed. Stop on partial failure and allow a retry. Reconcile GitHub removal against saved ownership and verified role trust, preserving external roles and the shared OIDC provider.

Export useful nonsecret URLs/client configuration and provide health checks plus a live acceptance checklist. Document backup/recovery of state, vault and passphrases, and the exact rerun command after interruption. Verify launcher behavior, environment isolation, saved-answer precedence, secret handling, expired SSO, state restoration, partial retries, and confirmation yes/no/timeout/EOF/cancellation with fake clocks and mocked cloud calls. Do not run a real deployment merely to test the wizard.

## Artifact tagging and reporting

Follow the shared [artifact ownership, tagging and report rules](shared.md#deployed-artifact-ownership-tagging-and-reports). An application/aggregator owns cross-feature orchestration and report aggregation; a feature repository owns its native inventory and standalone feature/test-stack reports. Feature code must not import host application modules to obtain reporting or claim host-provided API/identity/runtime resources.

Use the existing Infra Composer reporting model and Pulumi adapters where available: safe state inventory, preview events and read-only refresh completions have different evidence semantics. Keep exact feature repository ownership on taggable resources and authoritative untaggable inventory rows. Use native AWS descriptions plus project/environment/component/owner tags; without a domain, display `<project-name>-<environment>`. Include owned bootstrap resources only with verified scope/ownership receipts; external shared state/DNS/GitHub resources retain host/operator ownership.

Validate generated provider-facing names before resource registration using Composer's shared `awsnames` rules, including Lambda function names and permission statement IDs. Validate each applicable resource kind with its own constraints and identify the field or resource in errors. Keep logical Pulumi names, AWS physical names and display names distinct. Any required rename of an existing resource needs a reviewed migration.

In the browser, load saved scoped inventory before cloud sign-in when available and label it as recorded or unchecked. Keep it visible while preview runs and overlay the fresh plan operations when ready. Use short feature names, shared service icons and exact ownership and identity in artifact details. Supply a short nonsecret purpose for owned resources using native descriptions or `ArtifactDescriptions` keyed by exact Pulumi logical name. Use the same inventory for reports, the searchable tree, details and topology, and synchronize artifact selection across the browser views. Reuse Composer's bundled AWS glass models, basic shapes for unsupported types, and an accessible artifact list when WebGL is unavailable. Keep the visual assets local and preserve their provenance.

Generate equivalent JSON/Markdown/self-contained HTML for preview and deployment attempts, and support feature-scoped report-only/read-only verification. Preserve partial failures, independent evidence/configuration timestamps, private scope-checked caches and safe settings allowlists. Application UI publication belongs to the host; a library needs no application dashboard to report its artifacts.

## Documentation and visuals

Show exact API and infrastructure diagrams where they improve understanding; use real CLI/admin/demo screenshots only where available and useful, with sensitive data removed and provenance recorded. Headless packages need no artificial screenshot suite. Land on the latest confirmed stable module release, keep Development selectable, and retain coherent links between package docs, shared project operations, and parent architecture.
