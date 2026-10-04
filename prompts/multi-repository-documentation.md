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
- Publish workflow: orchestration and post-publication updates.

Adapt this model to the actual ecosystem. Do not impose Gradle or Maven on unrelated projects or introduce a publication system for a module that is not distributed as an artifact. Account for multiple artifacts, independent version streams, and prerelease channels.

For Gradle/Maven projects, prefer generateImportDocs and an explicit -PreleaseVersion value reused by publication and rendering. Obtain coordinates from existing publishing configuration; keep Markdown templates out of workflow YAML. Include applicable Kotlin DSL, Groovy DSL, version-catalog, and Maven examples in IMPORT.md, with a generated-file notice identifying its template and regeneration command.

Derive release versions from confirmed publication history using semantic version ordering and the repository's release policy; default patch progression may be appropriate. Do not infer published versions from github.run_number, an unreleased build variable, or an unverified tag. Handle the first release explicitly. Preserve sound existing conventions and avoid duplicate version variables or version-bump-only source commits.

The publish workflow must advance the installation reference only after the intended artifacts are confirmed published under the registry's success criteria. An upload or staging acceptance is not necessarily completed publication. Failed or partial publication must not advertise the attempted version as the latest release.

When release automation changes are in scope, modify the existing workflow. Serialize version allocation and release bookkeeping, preserve the exact source commit in release tags, and handle reruns, existing tags, and partial failures without republishing immutable artifacts. If publication succeeds but documentation generation, tagging, or Git push fails, retain a recoverable record of the published version and source commit. Retry bookkeeping for that release rather than allocating or publishing it again. Use minimal workflow permissions, avoid self-triggering release loops, preserve concurrent repository changes, and commit generated documentation only when it changes.

Replace duplicated current-version installation instructions in READMEs, websites, agent guides, and cross-repository docs with links to the authoritative reference. Preserve intentional historical/migration version references and local-project sample dependencies. Current-source API documentation must not imply that unreleased APIs are available in the latest published artifact.

Contributor instructions should explain the version source, template, renderer, release workflow, and failure recovery. Agent guidance must explicitly require reading IMPORT.md for dependency coordinates and released-version questions rather than guessing.

Audit/document mode records missing automation and proposed changes without claiming they exist. Implementation mode includes the build/template/workflow changes needed for this model; it does not authorize an actual package release.

WORKFLOW

1. Audit before editing.
Read repository instructions, documentation, relevant source code, tests, build scripts, deployment configuration, and CI workflows, including publishing history, installation templates, and generated references. Inventory documentation in all repositories and inspect every branch in the design repository.

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
