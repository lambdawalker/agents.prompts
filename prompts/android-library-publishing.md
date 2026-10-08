Act as a senior Android build/release engineer and documentation maintainer. Create or improve this Android library's GitHub Actions publishing system for Maven Central and JitPack, with independent module releases, shared version identities across destinations, recoverable publication bookkeeping, and installation documentation generated from confirmed availability.

INPUTS

- Target repository: [REPOSITORY_URL]
- Release branch: [main]
- Published modules: [discover from the project, or specify]
- Enabled destinations: [Maven Central and public JitPack; retain other registries only if requested]
- GitHub environments: [maven-central; jitpack, with no secrets for public builds]
- Module versioning: [independent per module; shared across destinations for each module]
- Propagation-delay environment: [delayed-docs; owner configures a 15-minute wait timer]
- Public-artifact polling budget: [40 minutes; finalization job timeout 50 minutes]
- Version policy: [reuse latest version/original source for unchanged release inputs; otherwise increment the global module patch; define explicit major/minor overrides]
- Cross-module release dependencies: [discover stable published pins and their repositories]
- JitPack polling budget: [30 minutes; job timeout allows setup, build and finalization]
- Documentation website: [discover existing workflow and GitHub Pages configuration]
- First release version, if nothing has been published: [explicitly supplied value or ask]
- Delivery: [local changes / pull request / commit to target branch]

REFERENCE

Use https://github.com/lambdawalker/android.apexfission.carddetector as the primary reference for destination-aware independent module publication. Inspect its current:
- publishing/repositories.yml, generated repositories.properties, and scripts/publishing_config.py
- .github/workflows/publish-card-detector.yml, publish-card-detector-model.yml, publish-card-detection.yml, publish-jitpack.yml, and finalize-carddetector.yml
- scripts/module_release.py, release_identity.py, jitpack_build.py, jitpack_release.py, import_docs.py, and scripts/tests/
- jitpack.yml, root/module Gradle publishing configuration, and dependency pins
- docs/releases/, docs/templates/MODULE_IMPORT.md.template, IMPORT.md, and docs/releases.md
- .github/workflows/documentation.yml and verify-release-tooling.yml
- scripts/setup_github_environments.py and github_environment_api.py

The retired private Maven destination is not a default requirement. Preserve historical provenance without re-enabling retired destinations. Examples in a runbook do not prove publication; inspect confirmed records and actual build results.

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

Adapt the approach, not its project-specific identifiers. Do not copy its namespace, module names, SDK levels, coordinates, author, license, or versions into another library. Verify current official publishing-plugin, Central, JitPack and GitHub Actions documentation before selecting APIs or plugin versions. Useful JitPack references: https://docs.jitpack.io/building/, https://docs.jitpack.io/faq/, https://docs.jitpack.io/api/ and https://docs.jitpack.io/private/. Treat the reference as an implementation to evaluate, not proof that every edge case is solved.

SCOPE

Implement the workflow, necessary Gradle configuration, release helpers, tests, template, and documentation. Modify an existing publishing workflow rather than creating a competing release path.

Do not publish a real package, trigger a release workflow, invent credentials, change repository protection rules, or configure external accounts merely to complete this task. List any owner setup still required. Preserve unrelated source behavior and demo applications' local project dependencies.

1. AUDIT AND DEFINE PUBLICATIONS

Inspect repository instructions, modules, Gradle wrapper, AGP/Kotlin/JDK requirements, version catalogs, publishing plugins, existing CI, release tags, public Central artifacts, licenses, and documentation.

Identify each publishable Android library and its release variant. Do not accidentally publish application modules, debug variants, or internal modules.

Use existing compatible tooling where sound. The reference uses com.vanniktech.maven.publish; reuse it or select a justified compatible approach. Verify that the configured tasks produce the intended release AAR, sources, documentation artifact, POM, and signatures, with metadata required by Central. Inspect dependency scopes and generated publication metadata.

Supply accurate POM name, description, project URL, license, developer information, and SCM links. Never assume the target project's license from the reference.

For multiple modules, default to independent module versions, with one version/source identity per module shared across destinations. Publish exactly the selected library; enforce selection in Gradle before publication tasks execute. A demo APK may be uploaded as a CI artifact, but must not become a Maven/JitPack publication.

When a module depends on a sibling, build its release against an explicit compatible, already published dependency pin rather than an unpublished project dependency. Validate the pin in its POM and Gradle API/runtime variants and record the dependency's real repository. Development/demo builds may continue to use project dependencies. Verify packaged assets against the selected source; retrieve required Git LFS objects and reject pointer files or mismatched hashes. Keep recordings, screenshots and unrelated large media out of documentation archives.

2. CENTRALIZE COORDINATES AND VERSION INPUT

Keep coordinates in one authoritative build configuration and use them for both publication and documentation rendering.

Expose one explicit release version input, such as:
./gradlew :library:publishAndReleaseToMavenCentral -PreleaseVersion=X.Y.Z

Use the actual task names supported by the selected plugin. Development builds must work without release credentials or a release version. A development snapshot fallback must never silently become a stable Central publication. Validate the explicit version before any remote publication side effect.

Avoid competing version properties and pre-release source commits that only bump a hard-coded version.

3. ALLOCATE SHARED MODULE IDENTITIES FROM VERIFIED HISTORY

Fetch full Git history and tags. Maintain a canonical immutable source tag such as MODULE/vX.Y.Z, separate from destination-specific publication state. Order semantic versions numerically, not by tag dates, strings or workflow run numbers. Ignore unrelated tags and explicitly define prerelease handling.

Read history across every enabled and historical destination, including confirmed records and durable reservations. Preserve legacy tags; do not retag old releases or invent source provenance. Reject the same module/version associated with conflicting source SHAs. A source tag alone is not public availability; an artifact alone is not source provenance. Network failures are not empty registries. Existing untracked public versions require deliberate reconciliation; an allocation floor may be bootstrapped from verified public versions without claiming their source or reusing them for cross-destination publication.

For each selected module:
- If there is no history, use the explicit initial-version policy.
- If current release inputs match the latest global module identity, reuse that version AND the original tagged source. If the selected destination already confirms that identity, exit successfully without upload, a new tag, or documentation mutation.
- If release inputs changed on a descendant source, allocate X.Y.(Z+1) from the latest global module identity, even if the selected destination has missed many releases.
- Reject an older checkout, an older explicit version, or reuse of an existing version for changed inputs. An explicit new major/minor/patch version must exceed the latest identity.
- A destination catching up must never build current HEAD under an old tag. Reused source must contain the needed publication protocol; otherwise require a new version instead of mutating historical source.

Define and test a deterministic release-input fingerprint. Include selected library source/resources/assets, packaged docs, build scripts, dependency catalogs, wrapper/toolchain configuration and relevant shared release tooling. Exclude generated IMPORT.md, confirmation records, unrelated sibling-only source and unrelated module properties. Be conservative about uncertain inputs, and document the policy. Exact SHA equality is sufficient for reuse; generated-docs-only commits should not force endless patch increments. Changes to documentation actually packaged in an artifact count as release inputs.

Record semantic version separately from each destination's actual consumer coordinate/version. Coordinates can differ between Central and JitPack even for the same module identity. Reconcile public destination metadata before writes; refuse untracked collisions or newer unexplained releases.

3A. DECLARE DESTINATIONS ONCE

Use a canonical registry mapping stable destination IDs to GitHub environment and publisher protocol (central, jitpack, and optional Maven-compatible services). Generate manual repository dropdowns and Gradle-readable public configuration from it; CI must detect drift. Remove retired destinations from selectable options without destroying their historical records. Do not keep URLs, credentials or secrets in the public registry.

Use per-destination confirmed records and pending journals, each keyed by module. Store the endpoint, source, semantic version, actual coordinates/consumer version, hashes, phase and dependency pins. Missing/null records must not imply availability.

4. BUILD A CONTROLLED WORKFLOW

Provide a clearly named workflow_dispatch entry point per publishable module, with a generated repository selector and optional version override. Ordinary releases should not require guessing a patch number. Shared workflow_call implementations are called by these entry points; document that they do not themselves display a Run workflow button. Provide a separate manual finalizer taking destination, module and version.

For Central, split the workflow into publish, propagation-delay, and finalize. JitPack uses local validation, source-tag reservation, remote build/public verification, and finalization, with no Central delay or signing secrets. Provide a separate finalization workflow with both workflow_call and workflow_dispatch entry points; automatic and manual confirmation must use the same implementation.

Use one stable, repository-specific JOB-LEVEL concurrency group for the publishing job and the finalizer's mutating job, with cancel-in-progress: false. Cover allocation, reservation, upload, confirmation, and Git finalization with this lock. Keep the delay job outside it. Do not hold a workflow-level or caller-job lock while invoking a reusable workflow that needs the same lock: that can block manual recovery or deadlock the automatic path. Do not use per-run, per-workflow-name, or per-version groups that allow overlapping mutations of shared release history.

Concurrency alone is insufficient: preserve durable reservations and upload-started markers across reruns, runner failures, and non-Actions publication paths. GitHub's default concurrency behavior is not a durable FIFO queue; newer pending requests can replace older pending requests even with cancel-in-progress: false. Document this and make recovery safe to invoke again.

Configure the required JDK, Android SDK/build tools, Gradle wrapper, and any helper runtime according to the target project. Use compatible, explicitly versioned actions and the repository's pinning policy.

Run appropriate unit tests, lint/build checks, release assembly, generated-POM validation, and release-helper tests before publication. Build from the selected immutable source SHA.

Use the selected destination’s GitHub environment for its release secrets and existing approval gates. Resolve that environment from the registry in ordinary and recovery paths; do not hard-code a different environment in a reusable workflow. Public JitPack requires no JitPack publishing token, signing key or custom repository URL; an associated GitHub environment may remain empty. GitHub supplies its automatic token for permitted tag/documentation writes. Private JitPack access requires a separately scoped authentication/subscription design; never assume public-access rules apply. Expose secrets only to steps that need them. For the reference's plugin, the environment mapping is:

MAVEN_CENTRAL_USERNAME -> ORG_GRADLE_PROJECT_mavenCentralUsername
MAVEN_CENTRAL_PASSWORD -> ORG_GRADLE_PROJECT_mavenCentralPassword
SIGNING_IN_MEMORY_KEY -> ORG_GRADLE_PROJECT_signingInMemoryKey
SIGNING_IN_MEMORY_KEY_PASSWORD -> ORG_GRADLE_PROJECT_signingInMemoryKeyPassword

Validate required secret presence without printing values. Document Central Portal token setup, verified namespace ownership, signing-key requirements, and the signing-key password when the key is encrypted (it may remain unset for an unencrypted key). Never put keys or tokens in Git, logs, caches, or test fixtures.

5. RECORD THE ATTEMPT BEFORE UPLOADING

Before any upload or JitPack build request, durably reserve the module version/source and destination attempt. Create or validate the canonical MODULE/vX.Y.Z source tag and destination journal atomically where possible. Example journals: release-pending/MODULE/X.Y.Z for legacy Central compatibility, and release-pending/DESTINATION/MODULE/X.Y.Z for other destinations. Reuse an existing canonical tag only when its source agrees; never move it.

Record or make recoverable:
- For Maven uploads, expected SHA-256 hashes for the complete validated artifact set. For JitPack, local candidate evidence and source-content expectations, with verified remote hashes recorded after the provider build.
- For Maven uploads, a durable upload-started marker created before remote upload; reject a second upload invocation for the same reservation. For JitPack, retain the destination reservation and build-request evidence; repeated polling must not allocate a new identity.
- Version and coordinates for every intended artifact.
- Exact source SHA.
- Publication/deployment identifier when available.
- Release phase and how to inspect the result.

An unresolved destination/module attempt must block blindly retrying its upload. Pending identities also participate in global version allocation so another destination cannot assign the same number to different source. Keep unrelated modules independent, and permit another destination to publish the same reserved source identity when safe. Reserve before upload, not after receiving success: a runner may lose its connection after Central accepts the artifact.

Release tags and pending-attempt references have different meanings. Never advertise an attempt marker as a completed release.

6. PUBLISH AND VERIFY MAVEN CENTRAL COMPLETION

Invoke the plugin's actual publish-and-release task, not a staging/upload-only task while claiming completion.

Separate plugin completion from public registry propagation:

- Publish job: validate, reserve, mark upload-started, invoke the actual publish-and-release task, retain useful deployment identifiers/logs, and expose the immutable version/source as job outputs. Waiting performed inside the publishing plugin still consumes this job's runner; do not claim the environment wait removes that work.
- Propagation-delay job: after successful ordinary publication, reference the secret-free delayed-docs environment with a 15-minute wait timer. The wait is configured in GitHub Settings, not by YAML timeout-minutes. GitHub waits before assigning a runner; use only a short no-op step after the gate opens. Do not substitute a runner sleep or hold the release concurrency lock during this gate.
- Finalization job: after the gate, call the shared finalization workflow with the reserved version and expected source. Poll public Maven endpoints for up to 2400 seconds (40 minutes), exiting early when complete. Allow a 50-minute execution timeout for setup and final Git operations. This provides roughly 15 + 40 = 55 minutes for propagation after plugin completion, plus queue/setup time; it is not a guaranteed end-to-end duration.

The manual finalization entry point takes an existing version, bypasses the delay, and never builds, allocates, reserves, or uploads artifacts. It must work without Maven/signing secrets. A retained resume_version input should use this same finalizer and skip the delay. Handle skipped dependency jobs explicitly in the automatic finalize condition without allowing failed or cancelled publication to trigger ordinary automatic finalization. Unknown upload outcomes require deliberate manual recovery.

After obtaining the mutation lock, fetch remote state again and resolve the requested version from durable records. Validate the expected source when supplied by the publishing job. Reject unknown versions, conflicting tags, inconsistent marker records, or source mismatches. If the same release has already been finalized by the other path, exit successfully without polling, committing, or writing tags. An older completed release must also be a no-op and must never overwrite newer installation metadata, including while a later release is pending. Determine completion from consistent provenance records, not merely the presence of a tag.

For every intended artifact, confirm public POM identity, usable release AAR, sources, nonempty documentation artifact, Gradle metadata where published, and required detached signatures. Compare public artifacts with the SHA-256 hashes recorded before upload. Signature-file presence alone is not cryptographic signature verification; describe exactly what is checked. Do not equate staging acceptance or plugin success with public availability.

Handle transient registry errors with bounded retries. Stop clearly on contradictory identity/hash metadata or permanent failures. Missing artifacts may still be propagating: poll within the budget, but never finalize a partial artifact set. On timeout or failure, retain attempt markers and leave the confirmed release documentation unchanged. Availability checks supplement provenance; they do not independently prove who built or uploaded the artifacts.

6A. BUILD AND CONFIRM JITPACK SEPARATELY

JitPack builds tagged source itself; it is not a Central upload endpoint. Configure jitpack.yml to invoke an explicit build helper with compatible toolchains and the Gradle wrapper. Parse a canonical module tag, verify it identifies HEAD and check any supplied build commit. Reject unsupported branch/snapshot/commit publication requests under this stable-release policy. Install only the selected unsigned library into MavenLocal; guard against sibling, application and remote upload tasks. Never expose Central credentials to this build.

Distinguish semantic X.Y.Z from the tag-derived consumer version. JitPack represents tag folders with `~`, for example MODULE~vX.Y.Z. Determine actual coordinates from the chosen publication layout and verify them; do not assume Central's artifact names survive JitPack harvesting. Single-publication and multi-module layouts differ. The CardDetector reference selects one publication and uses repository coordinates. If independently tagged modules share a group/artifact, consumers cannot select both versions simultaneously: explain that limitation and use a verified published dependency for bundles. If both must coexist independently, select and validate a distinct-artifact layout instead of copying that limitation blindly.

Validate an unsigned candidate locally, reserve the source identity/destination journal, then request the intended artifact to start the remote build. Poll both provenance and public artifacts within the budget. Retry queued/building statuses, temporary missing artifacts, rate limits and transient server/network errors. Treat failed builds and contradictory identity as terminal with useful build-log links.

Require successful remote build status at the exact reserved full commit SHA AND valid public POM, AAR, sources, documentation and available Gradle metadata. Validate dependencies, bytecode compatibility and packaged assets. A successful status alone is insufficient. Do not require Central detached signatures or compare independently rebuilt ZIPs against local candidate hashes as proof of correctness. Record the downloaded public artifact hashes after verification; validate source-specific content contracts against the reservation. State the limits of trusting provider-reported provenance.

Keep application-controlled source tags immutable, but do not claim this makes the external service's artifacts immutable immediately. Check current provider rebuild/retention policies; changes to a build configuration require a new source identity, not silently moving a release tag. Recovery targets the same reserved source and version, never a new allocation or blind rebuild. A first live build is separate validation requiring authorization; local/CI build success must not be reported as confirmed JitPack publication.

7. GENERATE AUTHORITATIVE INSTALLATION DOCUMENTATION

Create or reuse:
- docs/templates/IMPORT.md.template: human-editable content.
- generateImportDocs: deterministic build task.
- verifyImportDocs: drift validation.
- IMPORT.md: committed generated installation reference.

For EACH module, select the highest confirmed semantic version across destinations. Show only destinations confirming that version at the same source SHA. If A has a newer release than B, direct consumers to A; when B catches up, show both as choose-one options. Reject conflicting source identities at the same highest version. Tags, pending builds and partial uploads never replace confirmed installation instructions. Keep independent modules and their pinned dependencies explicit.

Render using each destination's actual group/artifact/consumer version and required repository declarations, including dependency repositories. Record destination facts separately from mutable development configuration; never imply a JitPack version string is the same as Central's semver. Do not advertise an older destination as a source of the newest release. Include applicable Gradle Kotlin DSL, Groovy DSL, version-catalog, and Maven examples, supporting all public artifacts.

Add a generated-file notice naming the template and regeneration command. Keep Markdown content out of workflow YAML.

Without a version override, local regeneration should use the last recorded confirmed release, not a proposed next version. Preserve the published metadata when development coordinates change; detecting drift must not silently advertise unpublished coordinates.

Replace independently maintained current-version dependency snippets in README, docs, website, and agent guidance with links to IMPORT.md. Preserve migration history, compatibility statements, lockfiles, and samples intentionally using project dependencies.

Explicitly instruct humans and agents to read IMPORT.md for installation, coordinates, and released-version questions rather than guessing.

8. FINALIZE WITHOUT LOSING PROVENANCE OR CONCURRENT WORK

Canonical source tags are reserved before publication, particularly because JitPack needs a tag to build. Only after confirmed publication:
- Generate and verify IMPORT.md with the published version.
- Commit only intended generated documentation changes, if any, using the Actions bot identity.
- Verify the existing canonical source tag still resolves to the artifact source. Create a missing tag only for explicitly supported legacy recovery, never as proof of availability.
- Remove or complete the pending attempt record.

The later documentation commit need not be the release-tag target.

Fetch current remote state before finalization. Preserve unrelated concurrent changes, newer application/build inputs and sibling/destination confirmation records. Generate against current release-branch metadata using the reserved publication facts. Do not reject delayed finalization solely because HEAD advanced. If the executing release protocol or renderer changed incompatibly, stop for reconciliation rather than mixing assumptions. Never force-push the release branch or move existing release tags.

Prefer atomic Git updates when available so branch, release tag, and pending-reference updates cannot partially complete. Otherwise implement explicit recoverable finalization states.

Use only necessary permissions. Respect branch protection; support the repository's permitted PR or bot process rather than introducing bypass credentials.

Prevent bot commits from creating release loops. If generated documentation must refresh a site, account for the fact that GITHUB_TOKEN writes may not trigger ordinary push workflows. Use an explicit supported trigger, such as successful workflow_run completion of the manually launched module publication workflows AND the recovery finalizer. Watch the top-level workflows, not just a workflow_call-only helper. Require the release branch, same trusted repository and successful conclusion; check out latest release-branch metadata after finalization rather than the original source SHA. Build/verify the site including IMPORT.md, guard against stale deployment, and deploy with the required Pages permissions. Never execute untrusted pull-request code in a privileged workflow_run job. Keep a manual documentation-only retry that cannot publish packages. Validate this chain with an actual post-release documentation deployment when evidence is available; distinguish build success from deployment success.

9. MAKE RECOVERY SAFE

Provide the separate manual finalization action described above. It resolves the recorded source/version and hashes, verifies public artifacts using the destination adapter, skips Maven uploading, and retries documentation/tag finalization under the shared mutation lock. It can run during the automatic environment wait. If publication or another finalizer is active, it waits for that lock. The delayed automatic finalizer must safely become a no-op if manual recovery finishes first.

For JitPack, reading an uncached artifact may itself request the same tagged build; document this provider behavior rather than promising that recovery can never start remote work. Do not invoke a force-rebuild/delete API or move the tag automatically.

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

Keep a selected-module CI matrix and a local verification repository. Test Central and unsigned JitPack publication configurations without remote upload or signing credentials. Add focused coverage for:
- Multiple releases to A followed by catch-up to B: reuse the latest module identity/original SHA, create no duplicate canonical tag, and no-op after confirmation.
- Docs-only/sibling-only changes, model dependency pin changes, shared build changes, legacy identity conflicts and pending identities across destinations.
- Latest-only installation selection, numeric ordering, equal-version same-source destinations, conflicting sources, unpublished rename notices and accurate dependency repositories.
- JitPack tag parsing, single-module task selection, actual consumer coordinates, queued/building retries, failed builds, API source mismatch, partial artifacts and real LFS assets.
- Registry/dropdown drift, retired destinations and destination-appropriate environment/secret isolation.
- Successful top-level publication/recovery triggering documentation with latest metadata, without a bot-push assumption or release loop.

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

11. OWNER SETUP EXPERIENCE

Where a setup helper is needed, reuse or adapt the reference's cross-platform Python terminal wizard. Read enabled environments/settings from the same registry. Mask tokens and secrets, show existing nonsecret defaults, preserve existing values on blank input, reject missing required values, preserve multiline signing-key files, and present a concrete review before writes. Respect existing environment protections. Encrypt secrets through the GitHub API, sanitize errors/logs, and report partial/uncertain writes for safe reruns. Do not request Maven credentials for public JitPack. The temporary setup token is distinct from publishing credentials and must not be persisted. Offline UI tests must synchronize with widget state or disable cosmetic animation timing rather than rely on runner speed; include Windows coverage when supporting Windows.

DELIVERABLES

Provide the working workflows, canonical destination registry and generated dropdowns, build configuration, helpers, focused tests, installation template/reference, documentation-deployment integration, and release runbook. Include an environment setup helper when needed.

Include a destination-specific owner setup checklist. Public JitPack needs no access token, custom endpoint or signing key; explain any empty GitHub environment and optional protections. Only when Central is enabled: create maven-central with the four documented secret names; create delayed-docs with a 15-minute wait timer, no secrets, and no required reviewers if automatic continuation is intended; allow the release branch where deployment restrictions apply. Verify current GitHub plan/repository-visibility support for wait timers and report limitations. Merely referencing an environment does not configure its timer. Document existing bot contents/tag permissions and branch-protection requirements without requesting bypass tokens. Explicitly distinguish owner setup that remains unverified from settings actually inspected.

The runbook must explain first-time setup, ordinary release, version policy, exact source selection, artifact set, publication success criteria, installation generation, protected-branch handling, site updates, and failure recovery.

Finish with a concise report listing changes, validation results, required GitHub/destination setup, actual verified publication/build evidence, limitations, and commit or PR links. State explicitly that no real release was performed unless separately authorized and actually completed.
