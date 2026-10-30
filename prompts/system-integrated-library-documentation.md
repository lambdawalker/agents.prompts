# System-integrated library documentation

Act as a senior system architect and documentation engineer. Build or improve documentation for libraries that participate in a larger system spanning multiple repositories, together with the central architecture documentation repository.

## Inputs and scope

- Central architecture/documentation repository: [URL]
- Implementation repositories and their roles: [URLS_AND_ROLES]
- Target library or libraries: [URLS]
- Existing central and library site URLs: [DISCOVER_OR_SUPPLY]
- Target branches: [PER_REPOSITORY]
- Delivery: [local changes / pull requests / commit to target]
- Branch review: [target branches only / review additional named branches / review all design branches]
- Branch consolidation: [review only / consolidate explicitly named branches]
- Release automation: [audit and document / implement integration]
- Canonical language and requested locales: [PER_REPOSITORY; preserve existing choices and follow standalone defaults for new multilingual sites]

Default to target branches only, review-only consolidation, and auditing existing release automation. Missing access to another repository does not authorize inventing its contents or silently duplicating its architecture. Complete accessible work, document blocked reciprocal changes, and provide exact proposed changes for the inaccessible repository. Do not publish packages or deploy applications just to validate documentation.

## Required outcome

Each library has complete human HTML documentation and focused raw Markdown documentation for agents. The central architecture repository also has its own human HTML site and agent Markdown entry point. Humans can move between system design and implementation guides through contextual links. Agents can follow the same relationships entirely through Markdown until they deliberately need source code.

Use the quality, runnable-demo, screenshot, raw-Markdown, versioning, and validation requirements in [Standalone library documentation](standalone-library-documentation.md) for each library, with the ownership rules below replacing the standalone ownership model. Read that companion before implementing; if it cannot be retrieved, report the dependency rather than claiming the complete workflow was applied. Adapt those requirements to the central site: it documents architecture, contracts, flows, decisions, and references, not an invented installable library.

## Ownership and authority

The central repository owns general architecture: system boundaries, component relationships, terminology, product flows, state machines, shared security requirements, cross-repository contracts, architecture decisions, shared UI specifications, and documented design deviations.

Each library owns its implemented API, imports, setup, dependencies, local concepts, configuration, lifecycle, threading, resource ownership, build/test commands, runnable examples, operational guidance, limitations, migration notes, and confirmed installation metadata.

Do not rewrite intended architecture to match an implementation defect. Identify designed, implemented, verified, deprecated, and proposed behavior separately, with evidence and source refs. Record contradictions and their owners. Keep formal shared schemas in their established authoritative repository; do not arbitrarily relocate them.

Avoid duplicated architecture, but provide enough local orientation for readers to understand the library's responsibility. Human guides may show generated excerpts of central contracts; identify their source and ref and verify freshness. Agents should follow links to canonical Markdown.

## Central repository structure and site

Preserve meaningful existing feature paths such as auth/onboarding/email-confirmation/. Add only useful missing structure:
- README.md: system scope, human site, agent entry, repository directory, architecture and feature flow links.
- docs/agents/index.md: concise system-agent entry with task routing, component ownership, non-goals, critical invariants, version/ref scope, and raw Markdown links to feature contracts and library agent entries.
- Focused agent guides for system concepts, integration, contracts, UI states, limitations, troubleshooting, and decisions as needed.
- A repository catalog, preferably structured YAML/JSON with generated human and agent tables.
- A documentation ownership policy and compatibility/deviation records.
- Existing feature specifications, diagrams, decisions, and UI reference directories.
- sites/ or the established site root: the central human website, raw Markdown publishing, llms.txt, build and deploy workflow.

The central site must explain overall architecture, end-to-end journeys, component responsibilities, failure/recovery behavior, shared contracts, security boundaries, decisions, compatibility, and known gaps. Include a component directory with direct links to every known repository, human documentation site, agent entry point, and relevant integration page. Use contextual links from feature flows to the actual participating implementations, not only a generic repository list.

## Reciprocal navigation contract

Maintain these relationships where relevant:

| Origin | Required destination |
| --- | --- |
| Central README and site | Central agent entry, component repository directory, human library sites |
| Central feature guide | Participating library integration guides, API/schema owner, demos and implementation evidence |
| Central agent flow/contract | Library raw Markdown entry or exact recipe; explicit API/schema source |
| Library README and human site | Central human architecture, relevant flow, ownership policy, library agent entry |
| Library agent entry/recipe | Central raw Markdown entry and exact system contract/flow |
| UI reference and specification | Each other, parent feature, owning implementations, and capture evidence |

Record repository URL, role, branch/ref, human site URL, raw agent entry URL, owned contracts/features, and documentation status in the catalog. Missing or unpublished sites must be marked as such; do not invent deployed URLs. Separate public navigation from private/internal sources to avoid publishing confidential content.

Validate links in both directions, including project base paths, heading anchors, raw .md targets, and nested paths. Unavailable private targets should be reported distinctly from known broken links. Generate repeated directory tables from the catalog rather than hand-maintaining them across pages. Do not create circular reading prerequisites: give a brief local orientation, then route to deeper contracts.

## HTML UI references and machine-readable contracts

Reference example:
https://github.com/lambdawalker/design.attestra/blob/main/auth/onboarding/email-confirmation/ui-reference/email_verification_start/code.html

Treat code.html as a visual/interactive design reference. A timer-driven success banner in a prototype is not evidence of a working backend, email delivery, or an implemented authentication guarantee.

For each important UI reference, retain its established path and add or link a nearby Markdown specification, such as spec.md. Include:
- Stable screen and parent flow IDs; purpose and entry/exit conditions.
- Fields, labels, validation rules, actions, accessibility requirements, and visible states.
- Loading, error, retry, success, cancellation, and navigation transitions where applicable.
- Intended service interaction with links to canonical API/contracts; unknown behavior explicitly identified.
- Design status, implementation status, owner repositories, and evidence refs.
- Reference HTML, screenshot, and runnable implementation/demo links.
- Known prototype defects or missing states, without treating them as intended behavior.

The human central site should show the reference image and screen explanation, with an optional isolated prototype preview and a source link. Agents should reach the textual screen contract without parsing HTML or CSS. An agent implementing or inspecting the visual design may deliberately read/render the reference.

Distinguish capture_kind values such as design-prototype, component-preview, and running-application. Label these visibly in galleries and machine-readable manifests. Never present a prototype capture as proof of implemented product behavior.

Use reproducible browser captures for HTML references and the implementation platform's renderer for app captures. Pin or vendor external fonts/scripts/assets where appropriate and permitted, or record dependency failures. Verify scripts and network assets before claiming an interactive prototype works. If previewing arbitrary HTML, isolate it from documentation navigation and credentials; a static capture is an acceptable fallback. Report prototype defects separately unless fixing them is within scope.

## Cross-repository changes and release consistency

Document supported combinations of library versions, service/schema versions, and architecture revisions only when evidence exists. A new central design does not prove all implementations support it. Pin release-specific source and demo links; clearly label development pages.

A contract change must identify affected repositories and their required docs/tests. Update central specifications, local integration guides, navigation, and compatibility status together where authorized. Cross-repository commits are not atomic: use coordinated PRs or an explicit transition status with links and deploy compatible documentation in a recorded order. Keep old valid links or redirects during migration.

Each publishing repository owns IMPORT.md and its confirmed release metadata. The central site links to or deterministically consumes that reference at a recorded ref; it does not independently allocate versions. Central documentation deployment must not trigger package publication. Preserve the existing 15-minute delayed confirmation and 40-minute polling policy where applicable; use the Android publishing supplement for its implementation.

## Versioned and multilingual system navigation

Apply [Versioned and multilingual documentation](versioned-multilingual-documentation.md), required by the standalone workflow, to each owning library. Keep its release catalog and translations local; do not introduce another docs repository solely for these features. The existing central architecture repository retains its system-level role.

Version central architecture by its own recorded revisions or system releases, not by an invented shared library version. Its compatibility catalog should identify evidenced combinations of library/service/schema versions and architecture revisions. Historical integration pages link to exact library documentation and installation snapshots, not moving latest IMPORT.md. Keep a latest-installation link separately labeled when useful.

Cross-repository navigation must preserve or explicitly explain changes of language, release scope and owner. Missing translations should use the canonical language for that same recorded revision; never hide a jump to newer architecture or API. Coordinate translated terminology and stable flow IDs without creating independently maintained copies of contracts. Validate both sides of these links, including language/version catalogs and fallback notices. Do not claim an inaccessible repository was translated or deployed.

## Screenshots and validation

Apply the standalone screenshot policy to both central UI references and implementation libraries: author and inspect changed images, validate relevant changes in CI, reuse verified assets at release, and keep deployment retryable without republishing packages. Do not copy an implementation screenshot into the central repo without provenance and a synchronization policy; prefer a versioned reference or generated import.

In addition to site builds, API/example checks, and image validation, verify:
- Every participating library has a human site and a raw agent entry, or an explicit blocker.
- The central site and central agent docs provide system-level coverage.
- Both directions of the navigation contract work.
- UI reference specifications have textual semantics, source refs, and truthful capture labels.
- Documentation distinguishes intended design from implemented and verified behavior.
- Compatibility and installation statements match confirmed evidence.
- Unique existing knowledge and operational gotchas survive consolidation.

Deliver changes per repository, the ownership/navigation map, actual validation results, remaining access or hosting setup, unresolved design/implementation discrepancies, and commit/PR links. Do not claim other repositories were changed when only proposals were prepared.

## Existing multi-repository maintenance and release requirements

The following retained workflow supplies detailed ownership, branch-consolidation, and release-documentation safeguards. Apply branch inspection according to the explicit input above rather than requiring a full branch audit for every documentation task.

Act as a senior software architect and technical documentation maintainer. Organize the documentation across these repositories so each subject has one authoritative home.

INPUTS

- Design repository: [DESIGN_REPOSITORY_URL]
- Implementation repositories: [REPOSITORY_URLS_AND_THEIR_ROLES]
- Target branch: [main]
- Branch consolidation: [review only / consolidate into target]
- Delivery: [local changes / pull requests / commit to target]
- Release automation scope: [audit and document / implement release-documentation integration]

OBJECTIVE

The design repository owns the architecture of the entire system. Implementation repositories own the details needed to develop, build, configure, test, deploy, and troubleshoot their respective modules.

Replace duplicated documentation with useful links. Preserve existing knowledge, especially development gotchas and architectural decisions.

DOCUMENTATION OWNERSHIP

Design repository:
- System purpose, scope, terminology, and boundaries.
- Components, responsibilities, dependencies, and interactions.
- User journeys, state transitions, failure handling, and recovery.
- Shared security requirements and trust boundaries.
- Cross-module behavioral contracts and integration requirements.
- Architectural decisions, tradeoffs, alternatives, and unresolved questions.
- Shared UI specifications and product behavior, when applicable.

Implementation repositories:
- Module purpose and implemented capabilities.
- Source structure, entry points, and implementation-specific decisions.
- Exact API serialization, configuration schemas, and usage examples.
- Dependencies, prerequisites, and supported versions.
- Build, test, packaging, installation, deployment, and rollback procedures.
- Environment variables and configuration, without exposing secrets.
- Platform/provider limitations, debugging procedures, and development gotchas.
- Known implementation gaps and operational limitations.

The design repository explains what the system must do and why. Module repositories explain how their implementations work and how to operate them.

PUBLISHING AND INSTALLATION DOCUMENTATION

For repositories that publish reusable artifacts, give installation instructions and released versions one authoritative home in the publishing repository. Prefer a root IMPORT.md, or reuse an established equivalent. Design and consuming repositories link to it instead of maintaining copies of coordinates or “latest version” snippets.

Separate ownership:
- Release tags or an established verified release ledger: release history and version progression.
- Build/publishing configuration: artifact coordinates and publication behavior.
- An editable template, preferably docs/templates/IMPORT.md.template: installation prose and supported dependency-manager examples.
- Build tooling: deterministic rendering from the template, actual coordinates, and an explicit release version.
- IMPORT.md: committed generated reference for the latest successfully published release.
- Publish workflow: validation, reservation, and upload.
- Shared finalization workflow: public-artifact confirmation and post-publication Git updates, callable automatically and manually.

Adapt this model to the actual ecosystem. Do not impose Gradle or Maven on unrelated projects or introduce a publication system for a module that is not distributed as an artifact. Account for multiple artifacts, independent version streams, and prerelease channels.

For Gradle/Maven projects, prefer generateImportDocs and an explicit -PreleaseVersion value reused by publication and rendering. Obtain coordinates from existing publishing configuration; keep Markdown templates out of workflow YAML. Include applicable Kotlin DSL, Groovy DSL, version-catalog, and Maven examples in IMPORT.md, with a generated-file notice identifying its template and regeneration command.

Derive release versions from confirmed publication history using semantic version ordering and the repository's release policy; default patch progression may be appropriate. Do not infer published versions from github.run_number, an unreleased build variable, or an unverified tag. Handle the first release explicitly. Preserve sound existing conventions and avoid duplicate version variables or version-bump-only source commits.

The publish workflow must advance the installation reference only after the intended artifacts are confirmed published under the registry's success criteria. An upload or staging acceptance is not necessarily completed publication. Failed or partial publication must not advertise the attempted version as the latest release.

When release automation changes are in scope, modify the existing workflow. Serialize version allocation and release bookkeeping, preserve the exact source commit in release tags, and handle reruns, existing tags, and partial failures without republishing immutable artifacts. If publication succeeds but documentation generation, tagging, or Git push fails, retain a recoverable record of the published version and source commit. Retry bookkeeping for that release rather than allocating or publishing it again. Use minimal workflow permissions, avoid self-triggering release loops, preserve concurrent repository changes, and commit generated documentation only when it changes.

For Android/Maven Central automation, follow the companion Android library publishing prompt: upload, a secret-free delayed-docs environment gate configured for 15 minutes, then a shared automatic/manual finalizer polling public artifacts for up to 40 minutes. The gate waits without occupying a runner. Publishing and finalization share a fixed job-level mutation lock; the delay holds no lock. Manual finalization bypasses the delay but never uploads. Recheck durable remote state after acquiring the lock, treat already-completed releases as no-ops, and never let older recovery overwrite newer release documentation. Preserve pending markers on timeout or partial publication. Document owner configuration of the environment timer; YAML alone does not set it. Do not impose this timing on unrelated registries without evaluating their requirements.

Replace duplicated current-version installation instructions in READMEs, websites, agent guides, and cross-repository docs with links to the authoritative reference. Preserve intentional historical/migration version references and local-project sample dependencies. Current-source API documentation must not imply that unreleased APIs are available in the latest published artifact.

Contributor instructions should explain the version source, template, renderer, release workflow, and failure recovery. Agent guidance must require reading IMPORT.md for latest confirmed coordinates, or the selected module/version archive for historical coordinates, rather than guessing. Historical pages must not silently install the latest package.

Audit/document mode records missing automation and proposed changes without claiming they exist. Implementation mode includes the build/template/workflow changes needed for this model; it does not authorize an actual package release.

WORKFLOW

1. Audit before editing.
Read repository instructions, documentation, relevant source code, tests, build scripts, deployment configuration, and CI workflows, including publishing history, installation templates, and generated references. Inventory documentation in all accessible repositories and inspect the branches selected by the branch-review input. Do not expand to all branches unless requested.

Identify:
- Duplicated explanations.
- Conflicting or outdated statements.
- Missing architecture or implementation guidance.
- Broken links.
- Information stored in the wrong repository.
- Unique branch content that has not reached the target branch.

Do not rely solely on commit counts or branch names. Compare actual content; equivalent changes may already exist under different commits.

2. Establish explicit ownership.
Create or update a documentation ownership policy in the design repository and link to it from every implementation README.

Assign one authoritative location to each subject. Short orientation summaries are acceptable; independently maintained copies of protocols, procedures, or flow specifications are not.

Keep exact transport formats with the implementation or an existing shared contract artifact. Link architecture documentation to that source rather than reproducing the schema.

3. Reorganize and reconcile.
Move misplaced information to its proper owner. Replace removed explanations with descriptive links to specific pages or headings.

Preserve operational details and historical context. Clearly label historical material as superseded, and retain short redirects where moving a page would break established links.

Resolve stale implementation claims against source evidence. Distinguish:
- Intended design.
- Implemented behavior.
- Known deviations.
- Verified deployment or runtime behavior.

Do not silently redefine the intended architecture to match a bug or incomplete implementation.

4. Provide clear entry points.
The design README should link to the system overview, feature flows, architectural decisions, ownership policy, and implementation repositories.

Each module README should explain its scope, provide usable setup instructions, and link directly to the relevant architecture and flows.

Use focused documents when needed. Avoid empty templates, unnecessary file proliferation, and repeating the same explanation across multiple pages.

5. Handle branches according to the selected mode.
For review-only mode, report unique changes and recommended consolidation.

When consolidation is authorized, incorporate all relevant unique work into the target branch, resolve conflicts deliberately, and preserve history where practical. Do not overwrite newer work with stale branch content. Do not delete branches unless requested.

6. Validate and deliver.
Check relative links, cross-repository paths, heading anchors, diagrams, and referenced source locations. Verify commands against repository scripts and configuration. Do not execute deployments or destructive commands merely to validate documentation.

Confirm that useful information survived the reorganization and that only intended files changed. Verify published content after committing or merging. For generated installation documentation, verify deterministic rendering, unresolved placeholders, and agreement with confirmed release metadata. Detect template/coordinate drift without advancing the documented version to an unreleased build. Preserve metadata for the last published coordinates when development coordinates change. Check failure and rerun behavior when modifying release automation.

Keep this task focused on documentation and the selected release automation scope. Report unrelated implementation defects separately unless fixing them is explicitly authorized.

FINAL REPORT

Summarize:
- What changed in each repository.
- Where architecture, implementation guidance, and authoritative installation/version information now live.
- Release-documentation ownership, automation changes or remaining gaps, and failure-safety validation.
- Which branches were consolidated and which remain unresolved.
- Validation performed and its limitations.
- Remaining contradictions, missing decisions, or inaccessible information.
- Links to commits or pull requests.

Do not claim that builds, tests, deployments, or runtime flows passed unless you actually verified them.
