Act as a senior Android build/release engineer and documentation maintainer. Create or improve this Android library's GitHub Actions workflow for signed publication to Maven Central, with reliable version allocation, recoverable release bookkeeping, and generated installation documentation.

INPUTS

- Target repository: [REPOSITORY_URL]
- Release branch: [main]
- Published modules: [discover from the project, or specify]
- Publishing environment: [maven-central]
- Propagation-delay environment: [delayed-docs; owner configures a 15-minute wait timer]
- Public-artifact polling budget: [40 minutes; finalization job timeout 50 minutes]
- Version policy: [automatic stable patch increments; explicitly define any major/minor or prerelease policy]
- First release version, if nothing has been published: [explicitly supplied value or ask]
- Delivery: [local changes / pull request / commit to target branch]

REFERENCE

Use https://github.com/lambdawalker/android.apexfission.yolo as the reference for separated publication, delayed verification, and race-safe manual finalization. Inspect its current:
- .github/workflows/publish-yolo.yml and .github/workflows/finalize-yolo.yml
- scripts/release.py, scripts/finalize-release.sh, and scripts/tests/
- docs/releases.md and .github/workflows/verify-release-tooling.yml

Use https://github.com/lambdawalker/android.apexfission.permissions as an additional Android build/publication reference. Inspect its current:
- .github/workflows/publish-permission.yml
- permission/build.gradle.kts
- build.gradle.kts and gradle.properties
- scripts/release.py and scripts/finalize-release.sh
- scripts/tests/ and .github/workflows/verify-release-tooling.yml
- docs/templates/IMPORT.md.template, IMPORT.md, and docs/releases.md

Adapt the approach, not its project-specific identifiers. Do not copy its namespace, module names, SDK levels, coordinates, author, license, or versions into another library. Verify current official publishing-plugin and Central documentation before selecting APIs or plugin versions. Treat the reference as an implementation to evaluate, not proof that every edge case is solved.

SCOPE

Implement the workflow, necessary Gradle configuration, release helpers, tests, template, and documentation. Modify an existing publishing workflow rather than creating a competing release path.

Do not publish a real package, trigger a release workflow, invent credentials, change repository protection rules, or configure external accounts merely to complete this task. List any owner setup still required. Preserve unrelated source behavior and demo applications' local project dependencies.

1. AUDIT AND DEFINE PUBLICATIONS

Inspect repository instructions, modules, Gradle wrapper, AGP/Kotlin/JDK requirements, version catalogs, publishing plugins, existing CI, release tags, public Central artifacts, licenses, and documentation.

Identify each publishable Android library and its release variant. Do not accidentally publish application modules, debug variants, or internal modules.

Use existing compatible tooling where sound. The reference uses com.vanniktech.maven.publish; reuse it or select a justified compatible approach. Verify that the configured tasks produce the intended release AAR, sources, documentation artifact, POM, and signatures, with metadata required by Central. Inspect dependency scopes and generated publication metadata.

Supply accurate POM name, description, project URL, license, developer information, and SCM links. Never assume the target project's license from the reference.

For multiple artifacts, explicitly define whether versions are shared or independent and what constitutes a complete release.

2. CENTRALIZE COORDINATES AND VERSION INPUT

Keep coordinates in one authoritative build configuration and use them for both publication and documentation rendering.

Expose one explicit release version input, such as:
./gradlew :library:publishAndReleaseToMavenCentral -PreleaseVersion=X.Y.Z

Use the actual task names supported by the selected plugin. Development builds must work without release credentials or a release version. A development snapshot fallback must never silently become a stable Central publication. Validate the explicit version before any remote publication side effect.

Avoid competing version properties and pre-release source commits that only bump a hard-coded version.

3. ALLOCATE VERSIONS FROM VERIFIED HISTORY

Fetch full Git history and tags. Select versions with semantic ordering, not lexical ordering, tag dates, or github.run_number.

For the default policy, increment the latest confirmed stable patch version. Distinguish stable releases, prereleases, unrelated tags, and module-specific version streams.

Reconcile Git release tags with Central metadata. Stop for investigation when publication history and tags disagree. A tag alone does not prove publication, and a public artifact alone does not prove its source SHA.

If Central already has releases but tags are absent, bootstrap progression from verified published metadata without inventing historical provenance. If no release exists, require an explicit initial-version policy. Network errors must not be interpreted as an empty registry.

Record the exact source SHA and complete intended publication set before building.

4. BUILD A CONTROLLED WORKFLOW

Prefer workflow_dispatch on the configured release branch. Ordinary releases should not require manually guessing the next patch number. Provide a clearly separate recovery input such as resume_version.

Split the workflow into three stages: publish, propagation-delay, and finalize. Provide a separate finalization workflow with both workflow_call and workflow_dispatch entry points; automatic and manual confirmation must use the same implementation.

Use one stable, repository-specific JOB-LEVEL concurrency group for the publishing job and the finalizer's mutating job, with cancel-in-progress: false. Cover allocation, reservation, upload, confirmation, and Git finalization with this lock. Keep the delay job outside it. Do not hold a workflow-level or caller-job lock while invoking a reusable workflow that needs the same lock: that can block manual recovery or deadlock the automatic path. Do not use per-run, per-workflow-name, or per-version groups that allow overlapping mutations of shared release history.

Concurrency alone is insufficient: preserve durable reservations and upload-started markers across reruns, runner failures, and non-Actions publication paths. GitHub's default concurrency behavior is not a durable FIFO queue; newer pending requests can replace older pending requests even with cancel-in-progress: false. Document this and make recovery safe to invoke again.

Configure the required JDK, Android SDK/build tools, Gradle wrapper, and any helper runtime according to the target project. Use compatible, explicitly versioned actions and the repository's pinning policy.

Run appropriate unit tests, lint/build checks, release assembly, generated-POM validation, and release-helper tests before publication. Build from the selected immutable source SHA.

Use the designated GitHub environment for release secrets and any existing approval gates. Expose secrets only to steps that need them. For the reference's plugin, the environment mapping is:

MAVEN_CENTRAL_USERNAME -> ORG_GRADLE_PROJECT_mavenCentralUsername
MAVEN_CENTRAL_PASSWORD -> ORG_GRADLE_PROJECT_mavenCentralPassword
SIGNING_IN_MEMORY_KEY -> ORG_GRADLE_PROJECT_signingInMemoryKey
SIGNING_IN_MEMORY_KEY_PASSWORD -> ORG_GRADLE_PROJECT_signingInMemoryKeyPassword

Validate required secret presence without printing values. Document Central Portal token setup, verified namespace ownership, signing-key requirements, and the optional password for an encrypted signing key. Never put keys or tokens in Git, logs, caches, or test fixtures.

5. RECORD THE ATTEMPT BEFORE UPLOADING

Before contacting Central for publication, durably reserve the version and source association. The reference uses release-pending/X.Y.Z pointing to the source commit; an equivalent durable journal is acceptable.

Record or make recoverable:
- Expected SHA-256 hashes for the complete validated artifact set.
- A durable upload-started marker created before remote upload; reject a second upload invocation for the same reservation.
- Version and coordinates for every intended artifact.
- Exact source SHA.
- Publication/deployment identifier when available.
- Release phase and how to inspect the result.

An unresolved pending release must block blindly allocating another release. Reserve before upload, not after receiving success: a runner may lose its connection after Central accepts the artifact.

Release tags and pending-attempt references have different meanings. Never advertise an attempt marker as a completed release.

6. PUBLISH AND VERIFY COMPLETION

Invoke the plugin's actual publish-and-release task, not a staging/upload-only task while claiming completion.

Separate plugin completion from public registry propagation:

- Publish job: validate, reserve, mark upload-started, invoke the actual publish-and-release task, retain useful deployment identifiers/logs, and expose the immutable version/source as job outputs. Waiting performed inside the publishing plugin still consumes this job's runner; do not claim the environment wait removes that work.
- Propagation-delay job: after successful ordinary publication, reference the secret-free delayed-docs environment with a 15-minute wait timer. The wait is configured in GitHub Settings, not by YAML timeout-minutes. GitHub waits before assigning a runner; use only a short no-op step after the gate opens. Do not substitute a runner sleep or hold the release concurrency lock during this gate.
- Finalization job: after the gate, call the shared finalization workflow with the reserved version and expected source. Poll public Maven endpoints for up to 2400 seconds (40 minutes), exiting early when complete. Allow a 50-minute execution timeout for setup and final Git operations. This provides roughly 15 + 40 = 55 minutes for propagation after plugin completion, plus queue/setup time; it is not a guaranteed end-to-end duration.

The manual finalization entry point takes an existing version, bypasses the delay, and never builds, allocates, reserves, or uploads artifacts. It must work without Maven/signing secrets. A retained resume_version input should use this same finalizer and skip the delay. Handle skipped dependency jobs explicitly in the automatic finalize condition without allowing failed or cancelled publication to trigger ordinary automatic finalization. Unknown upload outcomes require deliberate manual recovery.

After obtaining the mutation lock, fetch remote state again and resolve the requested version from durable records. Validate the expected source when supplied by the publishing job. Reject unknown versions, conflicting tags, inconsistent marker records, or source mismatches. If the same release has already been finalized by the other path, exit successfully without polling, committing, or writing tags. An older completed release must also be a no-op and must never overwrite newer installation metadata, including while a later release is pending. Determine completion from consistent provenance records, not merely the presence of a tag.

For every intended artifact, confirm public POM identity, usable release AAR, sources, nonempty documentation artifact, Gradle metadata where published, and required detached signatures. Compare public artifacts with the SHA-256 hashes recorded before upload. Signature-file presence alone is not cryptographic signature verification; describe exactly what is checked. Do not equate staging acceptance or plugin success with public availability.

Handle transient registry errors with bounded retries. Stop clearly on contradictory identity/hash metadata or permanent failures. Missing artifacts may still be propagating: poll within the budget, but never finalize a partial artifact set. On timeout or failure, retain attempt markers and leave the confirmed release documentation unchanged. Availability checks supplement provenance; they do not independently prove who built or uploaded the artifacts.

7. GENERATE AUTHORITATIVE INSTALLATION DOCUMENTATION

Create or reuse:
- docs/templates/IMPORT.md.template: human-editable content.
- generateImportDocs: deterministic build task.
- verifyImportDocs: drift validation.
- IMPORT.md: committed generated installation reference.

Render using the actual publication coordinates and confirmed release version. Include applicable Gradle Kotlin DSL, Groovy DSL, version-catalog, and Maven examples, supporting all public artifacts.

Add a generated-file notice naming the template and regeneration command. Keep Markdown content out of workflow YAML.

Without a version override, local regeneration should use the last recorded confirmed release, not a proposed next version. Preserve the published metadata when development coordinates change; detecting drift must not silently advertise unpublished coordinates.

Replace independently maintained current-version dependency snippets in README, docs, website, and agent guidance with links to IMPORT.md. Preserve migration history, compatibility statements, lockfiles, and samples intentionally using project dependencies.

Explicitly instruct humans and agents to read IMPORT.md for installation, coordinates, and released-version questions rather than guessing.

8. FINALIZE WITHOUT LOSING PROVENANCE OR CONCURRENT WORK

Only after confirmed publication:
- Generate and verify IMPORT.md with the published version.
- Commit only intended generated documentation changes, if any, using the Actions bot identity.
- Create the stable release tag at the exact source SHA used to build the artifact.
- Remove or complete the pending attempt record.

The later documentation commit need not be the release-tag target.

Fetch current remote state before finalization. Preserve unrelated concurrent changes and stop if installation inputs changed in a way that would make the generated reference inaccurate. Never force-push the release branch or move existing release tags.

Prefer atomic Git updates when available so branch, release tag, and pending-reference updates cannot partially complete. Otherwise implement explicit recoverable finalization states.

Use only necessary permissions. Respect branch protection; support the repository's permitted PR or bot process rather than introducing bypass credentials.

Prevent bot commits from creating release loops. If generated documentation must refresh a site, account for the fact that GITHUB_TOKEN writes may not trigger ordinary push workflows. Use an explicit supported trigger, such as a successful workflow_run, with appropriate branch and trust checks.

9. MAKE RECOVERY SAFE

Provide the separate manual finalization action described above. It resolves the recorded source/version and hashes, verifies public artifacts, skips uploading, and retries documentation/tag finalization under the shared mutation lock. It can run during the automatic environment wait. If publication or another finalizer is active, it waits for that lock. The delayed automatic finalizer must safely become a no-op if manual recovery finishes first.

Cover:
- Validation failure before upload.
- Definitively rejected publication.
- Unknown outcome after timeout or runner failure.
- Publication still processing.
- Some artifacts public and others missing.
- Publication succeeded but rendering, tagging, or Git push failed.
- Existing tags, concurrent branch changes, and branch-protection refusal.
- Repeated finalization after a completed release.

Do not clear pending state automatically when a remote publication may still succeed. Never blindly re-upload immutable coordinates.

Document how a maintainer verifies a definitively failed attempt before clearing exactly its reservation. For partial releases, require reconciliation of the intended artifact set; do not treat the whole release as complete or invent an automatic rollback of public artifacts.

10. VALIDATE WITHOUT A REAL RELEASE

Test helpers with fixtures, mocked registry responses, and temporary Git repositories. Include:
- Semantic ordering such as 0.1.9 versus 0.1.10.
- Initial release and no-tags bootstrap.
- Divergent tags and Central history.
- Invalid versions and unresolved pending attempts.
- Recovery resolving the original source and skipping upload.
- Partial publication and registry timeouts.
- Failed publication leaving committed IMPORT.md unchanged.
- Deterministic rendering and unknown placeholders.
- Generated POM coordinates/version and the intended publication artifacts.
- Concurrent main changes, existing tags, and finalization failures.
- Recovery preserving immutable versions and source provenance.
- Automatic and manual finalization racing for the same release: one mutation and one successful no-op.
- Manual completion during the environment wait, followed by automatic finalization.
- A request for an older completed release while newer docs or a newer reservation exist.
- Unknown versions, expected-source mismatches, conflicting tags, and inconsistent markers.
- Polling success before the deadline, transient failures, timeout, partial artifacts, and hash mismatches.
- Identical job-level lock keys across publishing and both finalization paths, with the delay outside the lock and no caller/callee lock nesting.
- Ordinary automatic completion, skipped-delay recovery, and failed/cancelled publication dependency conditions.

Run relevant Gradle checks and validate workflow YAML, expressions, shell scripts, and permissions as practical. Keep credentials out of ordinary CI. Report checks that cannot run in the available environment.

DELIVERABLES

Provide the working workflow, build configuration, helpers, focused tests, installation template/reference, and release runbook.

Include an owner setup checklist: create maven-central with the four documented secret names; create delayed-docs with a 15-minute wait timer, no secrets, and no required reviewers if automatic continuation is intended; allow the release branch where deployment restrictions apply. Verify current GitHub plan/repository-visibility support for wait timers and report limitations. Merely referencing an environment does not configure its timer. Document existing bot contents/tag permissions and branch-protection requirements without requesting bypass tokens. Explicitly distinguish owner setup that remains unverified from settings actually inspected.

The runbook must explain first-time setup, ordinary release, version policy, exact source selection, artifact set, publication success criteria, installation generation, protected-branch handling, site updates, and failure recovery.

Finish with a concise report listing changes, validation results, required GitHub/Central setup, limitations, and commit or PR links. State explicitly that no real release was performed unless separately authorized and actually completed.
