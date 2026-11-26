# Android and JVM library publishing

Act as a senior Android/JVM build and release engineer. Implement or migrate this repository's publication system using the shared tooling in [gradle.android-repo-setup](https://github.com/lambdawalker/gradle.android-repo-setup). Load [Shared Android repository automation](android-repository-automation.md), the appropriate [Android](libraries/android.md) / [Kotlin](libraries/kotlin.md) section and [versioned documentation policy](versioned-multilingual-documentation.md). Read each dependency once.

## Inputs and scope

- Target repository and release branch: [URL; main unless established otherwise].
- Intended published modules: [discover and review evaluated Gradle inventory].
- Destinations: [Maven Central and public JitPack; retain other registries only when requested and fully supported].
- Tooling pins: [inspect setup/docs pins and separately versioned Gradle plugins; choose verified compatible revisions].
- Version policy: [shared semantic version/source identity per module; independent module streams; destination-specific availability and recovery].
- Hosting: [preserve existing documentation workflow, including artifact-only validation].
- Delivery: [push task branch and open/update PR unless explicitly directed otherwise].

Implement configuration, Gradle integration, generated launchers/workflows, validation and runbooks. Do not stop at a workflow skeleton or secret list. Modify the existing release path rather than adding a competing one. Do not publish a package, run a live JitPack release build, change live environments/protections, move immutable tags or deploy production merely to validate tooling. Report required owner setup separately.

## Inspect current implementations

The shared CLI's current source, supported configuration and tests define the implementation contract. Read its bootstrap, module discovery, release assets, reconciliation and JVM guides linked from the automation supplement. Inspect the exact pinned revision, not just main or the latest release label. Upgrade only after capability checks pass. Generic release engine changes belong upstream; do not copy that engine back into a consumer.

Use these consumers as examples to inspect, without assuming every main branch has the same migration status:

- [Card Detector](https://github.com/lambdawalker/android.apexfission.carddetector): independent core/model publications, explicit published dependency pins, model/LFS asset validation and domain-specific docs projections.
- [Demo Common](https://github.com/lambdawalker/android.apexfission.demo-common) and [Permissions](https://github.com/lambdawalker/android.apexfission.permissions): Android build/publication conventions and consumer documentation.
- [Coordinates](https://github.com/lambdawalker/android.apexfission.math.coordinates): root Kotlin/JVM JAR publication using the same release engine.
- [Signature](https://github.com/lambdawalker/android.apexfission.signature): headless/source-only documentation and validation, not evidence of a published Maven release.

Inspect `.project/setup/`, `.project/docs/`, `.project/verification/`, Gradle files, generated workflows, `jitpack.yml`, `IMPORT.md` and `docs/releases/` where present. Old paths such as `scripts/module_release.py` or `publishing/repositories.yml` are migration inputs, not files to recreate. Do not copy another project's namespace, license, coordinates, toolchain, signing identity or version. Check current official provider/plugin documentation when changing that integration.

## Define publications and Gradle contracts

Discover actual module kinds through evaluated Gradle plugins. Keep stable release IDs distinct from paths/directories. Publish only selected library release variants, never applications, debug variants, internal mocks or unintended siblings. Preserve normal demo project dependencies and unrelated source behavior.

Android component repositories retain the [required runnable demo app](libraries/android.md#every-component-repository-needs-a-runnable-demo-app), including its shared catalog and top App settings action. Exclude that application from library publication, while retaining its build/verification in repository CI; publication setup must not remove the demo or replace normal source dependencies with a stale released artifact. This Android host requirement does not add an APK to a JVM-only library.

Keep compatible root/library/application conventions. CLI versions and Gradle plugin versions are separate. JVM modules retain compatible Java/Kotlin/Maven integration where the chosen Android publication plugin cannot support them. Configure root/nested scoped tasks and the release properties expected by the pinned engine, including `releaseVersion`, `releaseModule`, `jitpackBuild` and `jitpackScopedTag` where applicable.

Validate the unsigned local publication before reserving or uploading: POM identity/dependencies, AAR or JAR, sources, nonempty documentation archive and Gradle module metadata. Configure explicit per-module verification contracts rather than inheriting Demo Common defaults. Check package prefixes, bytecode target, API/runtime dependency versions/scopes, forbidden dependencies, assets and exclusions. Keep the Gradle daemon JDK separate from the published bytecode target.

JVM modules select `packaging: jar` and explicit JVM metadata rules; Android defaults to AAR. Validate executable API/runtime variants separately from documentation variants. Central requires the recorded artifact/signature set. JitPack may omit Gradle metadata under the supported verifier policy; independently rebuilt ZIPs require source/content validation rather than equality to local ZIP bytes. Never reinterpret an AAR reservation as JAR or vice versa.

Provide accurate POM project, SCM, developer and license metadata. Missing license is an owner decision blocking real publication; do not copy a reference license. Public JitPack needs required metadata in immutable source, not only Actions environment variables. Clearly labeled local fixtures are not real release metadata.

For sibling runtime dependencies, publish against an explicit compatible already-published pin and validate it in POM/Gradle variants. Preserve independent core/model versions and correct provider repositories. Hydrate required Git LFS assets, reject pointer files and verify immutable source/hash/format contracts. Exclude recordings, screenshots, debug fixtures and unrelated binaries from source/javadoc archives. Keep intended model assets only in their selected artifact.

Exercise the wrapper exactly as workflows/JitPack do; preserve its executable bit or use `bash gradlew` consistently. Local development must work without publishing credentials or a stable release override. Development fallback versions must never silently become public releases.

## Generate a single maintained release path

Declare destinations once in `.project/setup/repositories.yml` and generate the matching public properties/dropdowns. Keep endpoints/credentials in their supported nonpublic settings, not the public registry. Reject unsupported publisher protocols before reservation. Remove retired selectable destinations while retaining historical facts.

Use CLI-generated per-module manual publishing workflows and shared finalization/reconciliation. Each module action fixes its module internally. Use the pin's repository selector, one optional semantic `version` input and supported narrowly scoped override inputs; do not create competing initial/resume version fields. Recovery has its own action and never allocates or uploads. Document the exact generated field names rather than inventing a consumer-specific interface.

Preserve the shared repository mutation lock with `cancel-in-progress: false` across allocation/reservation/finalization. Keep Central's propagation delay outside that lock and avoid nested caller/callee locks. Concurrency is not durable journaling or a FIFO queue; the pinned release engine must enforce safety after runner failures and direct Gradle invocations too.

Configure the selected destination's actual GitHub environment. Public JitPack needs no Central credentials, publishing token or signing key. Central uses its existing secret mapping, commonly:

```text
MAVEN_CENTRAL_USERNAME -> ORG_GRADLE_PROJECT_mavenCentralUsername
MAVEN_CENTRAL_PASSWORD -> ORG_GRADLE_PROJECT_mavenCentralPassword
SIGNING_IN_MEMORY_KEY -> ORG_GRADLE_PROJECT_signingInMemoryKey
SIGNING_IN_MEMORY_KEY_PASSWORD -> ORG_GRADLE_PROJECT_signingInMemoryKeyPassword
```

Validate presence without printing values. Preserve the configured Central delay/poll budgets and provider gates; the established default is a secret-free delayed-docs environment with a 15-minute wait when supported, then bounded public polling. A YAML environment reference does not configure a wait timer. Keep read-only validation permissions separate from trusted release/tag/docs writes; never request bypass credentials to defeat protected branches.

## Preserve immutable version identity and durable journals

Use the shared allocator, fingerprint, upload guard and finalizer. For new versions, one `release-identity/<module>/<version>` binds exact source and build-input fingerprint across destinations. Destinations retain their own reservations, upload markers, hashes, receipts and confirmation status. Independent destinations do not authorize different source for the same new identity. Historical provider-specific equal version numbers from different sources remain distinct historical facts.

Blank version input uses the shared policy: first release 0.1.0; unchanged inputs reuse the canonical original source/version, or no-op for an already-confirmed destination; changed inputs require the next valid module patch beyond known identity history. Explicit stable X.Y.Z must satisfy the pin's ordering, source and pending-attempt checks. Do not derive a catch-up release from current HEAD under an older tag. Preserve the allocator's historical collision/ancestry checks and semantic ordering (0.1.10 > 0.1.9).

Build fingerprints include selected runtime/build/toolchain/verification inputs and exclude mutable generated installation pointers, release archives and unrelated sibling changes according to the shared policy. Do not broaden exclusions simply to reuse an old version. Packaged documentation changes may affect artifact inputs.

After unsigned local validation, reserve source identity and destination attempt durably before any remote side effect. Keep canonical `<module>/vX.Y.Z-<destination>` source tags immutable and preserve legacy tags/consumer versions. Annotated pending/uploading journals are recovery records, not confirmed releases. Record complete expected artifact hashes, source, coordinates, dependency pins and the immutable resolved verification snapshot.

Wire the CLI guard into actual Central upload tasks, including aggregate publish-and-release paths, before uploading. Local verification does not invoke the upload guard. Capture/persist the provider deployment receipt when available, including uncertain failures; retain useful sanitized logs. A workflow-only guard that direct Gradle upload can bypass is insufficient.

Unresolved, partial, active or uncertain outcomes retain their journals and block unsafe retries. Never blindly upload twice, delete pending tags because CI failed, force-move source tags or substitute today's verification config for an old attempt. Recovery without a snapshot must use the original immutable source contract; if unavailable, report the blocker or explicitly verify with original tooling in an isolated historical checkout. Do not rewrite old journals to claim the current contract applied.

## Confirm and reconcile safely

Central plugin completion/staging acceptance is not public availability. Confirm every recorded public artifact, POM identity/dependencies, content/hash and required detached signatures before advancing docs. Distinguish signature presence from cryptographic signature verification. Poll transient failures within budget; contradictory identity/hash is a failure, and a partial set never finalizes.

JitPack builds the reserved immutable source, installing only the selected unsigned publication into MavenLocal. Verify full provider-reported source SHA and usable public POM/AAR-or-JAR/sources/docs with dependency/asset/bytecode contracts. Record actual remote hashes; do not require Central signatures or compare independent ZIP metadata against local archives. Source tags with folders may yield consumer versions such as `<module>~vX.Y.Z-jitpack`; verify actual group/artifact/version layout. Explain same-artifact independently tagged module collisions rather than suggesting incompatible coordinates can coexist.

Ordinary release, automatic finalization and manual recovery use the same shared implementation. Finalization may verify and mutate metadata; it never runs a local artifact build, uploads, allocates a version or requests signing credentials. JitPack artifact requests can start an uncached build of the already-reserved tag: recovery is not provider-side-effect-free. Do not force a rebuild, change the source or allocate a new identity during recovery. Re-fetch state under the lock, validate expected source and all journal identities, preserve concurrent main/sibling changes, and use atomic/leased ref operations. Already-completed releases are idempotent no-ops and must not overwrite newer installation metadata. Preserve journals if final Git bookkeeping fails so recovery remains possible.

Use supported preview/apply reconciliation rather than ad hoc tag deletion. Fully verified public artifacts can finalize without upload. For an attempted provider upload/build, archive failure only with repeated terminal provider evidence and absence of all public artifacts, matching source/journal identity and unchanged refs. The shared reconciler also supports narrowly scoped pre-upload Central abandonment: prove the new reservation never passed the atomic upload guard, verify no public artifacts and atomically fence a late guard before archiving. Do not extend this exception to legacy or uncertain upload attempts. Missing receipts, unknown status, authentication errors, partial artifacts, mismatched hashes and ambiguous JitPack `tagNotFound` remain `needs-review`. Preserve the original annotated journal object under the failed-attempt reference and keep source/version identities immutable. Narrow JitPack pending overrides preserve older attempts and never bypass upload-started guards.

## Generate truthful installation and Astro documentation

Use `android-repo-docs` for template-based `IMPORT.md`, source examples, history and shared Astro builds. Setup owns release confirmation; the docs executable cannot create publication facts. Keep the existing human-editable template, generated-file notice and deterministic check/update commands rather than introducing another local Python renderer.

Only confirmed availability advances latest installation facts. Render actual per-destination consumer coordinates, source and dependency pins; do not infer publication from proposed versions, tags or a successful build. Preserve exact destination-qualified archives before replacing latest pointers, and preserve historical paths. Keep history separate from pending journal discovery. README/current guides link to canonical installation facts; historical pages use their own archived facts, never latest IMPORT.md.

Pin historical guides/examples/API/translation trees to retained immutable source/documentation provenance. Prose fixes and new locales do not republish artifacts. Apply the automation supplement's shared Astro, raw Markdown, fallback, alias, media and root/project-prefix checks. Separate docs-only retry from package upload. Trigger site updates only within the existing authorized deployment policy; preserve artifact-only validation. A docs deployment failure must never re-upload the package.

## Owner setup, validation and delivery

Use the shared Rust setup wizard and generated `setup.sh` / `setup.bat` / `.project/setup/bootstrap.ps1`; do not recreate a Python GitHub environment wizard. Preserve existing encrypted local vault/signing conventions, secret masking, concrete review, blank-input preservation and partial-write recovery. Distinguish temporary GitHub setup authentication, Actions `GITHUB_TOKEN`, Central credentials, Maven OpenPGP keys and APK keystores. No secrets in source, logs, process arguments, fixtures or reports. See the [terminal wizard prompt](setup-wizards-terminal.md).

Document namespace/token/signing prerequisites, environment names and actual permissions. Inspect secret-free environments too. Preserve existing timers/protection rules; report unsupported plan/visibility features and required owner actions without pretending they were configured. Do not run setup against live settings as part of offline migration validation.

Run appropriate Gradle tests/build/lint and unsigned local verification, generated bootstrap/workflow/registry drift checks, relevant config/YAML checks and documentation checks. Test selected Android and JVM artifact contracts as applicable. Verify recovery paths with shared-tool fixtures or focused integration checks, never a live upload. Generic engine unit tests belong in the CLI; do not copy them into every consumer. Retain tests for project-specific dependency/asset/metadata behavior.

Review failures that commonly break migration: wrong source/pin, stale launcher cache, missing binary PATH, wrong scoped task, skipped upload guard, missing receipt, changed verification snapshot, duplicate workflows, partial publication, concurrent finalization and stale docs. Confirm the shared engine's applicable tests cover version ordering, unchanged-source reuse/no-op, pending attempts, provider hash/source mismatches and idempotent recovery. Report unavailable checks accurately.

Deliver declarative configuration, maintained launchers/workflows, compatible Gradle contracts, focused domain tests, confirmed installation/history handling and a release/recovery runbook with exact commands. Remove superseded unused scripts and README-only script folders after checking all callers. Report CLI and Gradle plugin pins separately, checks actually run, required owner/upstream work and the PR link. State that no real release or deployment occurred unless separately authorized and actually performed.
