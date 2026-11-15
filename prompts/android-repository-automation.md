# Shared Android repository automation

Apply this supplement with [Android libraries](libraries/android.md), the selected documentation workflow and, when publishing is in scope, [Android library publishing](android-library-publishing.md). It also applies to JVM modules in these repositories. These ecosystem-specific rules take precedence over generic suggestions to create local scripts, site applications or deployment vaults.

## Inspect the actual shared tooling

Use [gradle.android-repo-setup](https://github.com/lambdawalker/gradle.android-repo-setup) as the automation implementation, rather than copying scripts from a library. Read its current README and relevant guides:

- [Bootstrap](https://github.com/lambdawalker/gradle.android-repo-setup/blob/main/docs/bootstrap.md), [module discovery](https://github.com/lambdawalker/gradle.android-repo-setup/blob/main/docs/module-discovery.md) and [application conventions](https://github.com/lambdawalker/gradle.android-repo-setup/blob/main/docs/application-conventions.md).
- [Standalone docs](https://github.com/lambdawalker/gradle.android-repo-setup/blob/main/docs/standalone-docs.md), [source docs](https://github.com/lambdawalker/gradle.android-repo-setup/blob/main/docs/source-docs.md), [documentation media](https://github.com/lambdawalker/gradle.android-repo-setup/blob/main/docs/documentation-media.md) and [Astro migration audit](https://github.com/lambdawalker/gradle.android-repo-setup/blob/main/docs/astro-migration-audit.md).
- [Project checks](https://github.com/lambdawalker/gradle.android-repo-setup/blob/main/docs/shared-project-checks.md), [release assets](https://github.com/lambdawalker/gradle.android-repo-setup/blob/main/docs/release-assets.md), [release reconciliation](https://github.com/lambdawalker/gradle.android-repo-setup/blob/main/docs/release-reconciliation.md) and [JVM libraries](https://github.com/lambdawalker/gradle.android-repo-setup/blob/main/docs/jvm-libraries.md).

Inspect the consumer's committed pins, configs, generated workflow manifest, actual workflows, Gradle integration and release history. Check what the pinned revision implements; upstream main and an open PR are not evidence that a released executable has that capability. Pin a verified published release or full immutable commit, never a floating branch. Do not replace an intentional source pin merely because a newer version number exists. Verify parity first, including routing, assets and cross-platform bootstrap. Report a required upstream capability separately rather than silently modifying the CLI outside the task scope.

`android-repo-setup` and `android-repo-docs` are separate Rust executables with independently configured pins. Gradle convention/publication plugins are separately versioned artifacts: a CLI upgrade does not upgrade or install them. Preserve compatible AGP, Kotlin, Gradle, JDK, SDK and bytecode targets.

## Keep consumers declarative

| Ownership | Consumer files / role |
| --- | --- |
| Setup, inventory, publishing destinations, verification contracts | `.project/setup/config.yaml`, `repositories.yml`, generated registry/module properties and workflow manifest |
| Maintained setup bootstrap | `setup.sh`, `setup.bat`, `.project/setup/bootstrap.ps1` |
| Guides, source/example extraction, media and site configuration | `.project/docs/config.yaml`, optional `.project/docs/introductions/`, canonical Markdown and evidence manifests |
| Maintained documentation bootstrap | `docs.sh`, `docs.bat`, `.project/docs/bootstrap.ps1` |
| Generic screenshot/JUnit inventories and isolated source validation | `.project/verification/checks.json` |
| Discovery, secrets wizard, workflow generation, release bookkeeping/recovery | Shared `android-repo-setup` executable |
| Imports, source docs, media checks, history, shared Astro build/serve | Shared `android-repo-docs` executable |

Generate launchers and workflows using the pinned tool; review diffs before overwriting existing files. Use `bootstrap check`, `workflows --check` and `setup check` as supported by that pin. Commit declarative inputs and generated tracked outputs together. Keep executable caches ignored. The `.ps1` files implement the generated Windows bootstrap behind the `.bat` launchers; retain them. They are not legacy release scripts or authored documentation.

Use evaluated Gradle discovery, review `discover --save`, preserve exclusions and stable release IDs, and distinguish Gradle paths from directories. Support nested/remapped projects and root path `:` / directory `.`. Inspect each composite build separately. Do not discover publications by regex or assume every `android.*` repository produces an AAR.

Remove superseded local Python release, registry, bootstrap, generic docs and validation implementations only after equivalent CLI checks and recovery paths work. Remove duplicate handwritten release workflows deliberately; generation does not necessarily remove unmanaged files. Search CI, Gradle, configs, runbooks, tests and imports for every removed entry point. Retain active domain-specific projections, screenshot review galleries, data preparation, asset/model checks and focused acceptance tests where the CLI cannot express them. Do not delete an imported helper just because its command-line entry point is unused. Remove an empty `scripts/` directory or a README-only placeholder; do not recreate it as a deliverable.

## Use shared Astro documentation

For new or explicitly migrated Android documentation, select `site.backend: astro` in `.project/docs/config.yaml`. The CLI bundles Astro/Starlight and locked frontend dependencies. Consumers keep content, configuration, narrow presentation overrides and genuine project-specific projections; do not scaffold another generic npm renderer, Python site generator or copy of the shared frontend. A `sites/package.json` may remain for domain-specific preparation/tests when required.

Existing Zola or external command configurations do not migrate merely by upgrading the CLI. Deliberately remove competing `site.build` / `site.serve` arrays when adopting the built-in backend. Port useful design overrides and preserve old routes before deleting obsolete Zola/templates/renderer code. Use the selected pin's supported config schema; accepting a field is not proof that its rendering behavior is implemented.

Keep `docs/agents/` as canonical consumer guides, `IMPORT.md` as generated confirmed installation facts, reviewed locale sources/manifests and immutable release history. Configure public-source groups, editable introductions and runnable examples rather than maintaining duplicate snippets. Example `start` and `end` HTML comments delimit the region replaced in the target Markdown; retain both markers and the surrounding authored prose. A source/hash acceptance manifest records review inputs, not proof of binary/API compatibility. Review API changes before accepting new hashes.

Use immutable publication source and retained `documentation_ref`/translation provenance to build historical guides with the current trusted renderer. Never execute historical scripts or silently substitute current APIs when an object is unavailable. Source links resolve to the appropriate retained source; newly added documentation examples may require the recorded documentation ref. Preserve confirmed per-destination installation facts and compatibility constraints. Do not invent releases for a source-only library: show Development, source integration and its actual build instructions.

Translations track hashes of exact canonical English input. Preserve code fences, identifiers and executable examples; accepting hashes requires a meaning review. Missing/stale translations visibly fall back to English at the selected revision. Keep raw Markdown, `llms.txt`, catalogs, scoped search, version/language navigation and legacy HTML/raw aliases consistent with the human site. Preserve the current guide when switching scope, and explain missing-guide fallback.

Run the supported commands through maintained launchers, for example:

```sh
sh ./docs.sh check
sh ./docs.sh imports --check
sh ./docs.sh history verify
sh ./docs.sh build --check
```

Verify both `/` and the actual GitHub Pages project prefix with `--base` as supported by the pin. Validate routes, anchors, raw links, translations, media, module/version scopes and source provenance, plus focused project acceptance tests. Fetch required immutable Git history and Git LFS objects; reject pointer files and mismatched media hashes. Use Node/npm required by the pinned Astro backend (currently Node 24 in generated jobs); exact frontend versions belong to the CLI lockfile, not a copied consumer dependency list. Source-pinned bootstrap needs Git and Rust. Launchers expose executables to their children, not to later shell/Actions steps: explicitly install or expose the binary where Gradle/direct commands need it.

Keep docs validation, artifact creation and deployment distinct. Documentation-only retry never allocates a library release or uploads an artifact. Preserve artifact-only pipelines and existing hosting authorization; do not enable Pages deployment simply to make a build ready.

## Verify modules and evidence honestly

Configure per-module publication contracts explicitly: package prefix, API/runtime dependencies, bytecode target, packaged assets and exclusions. Avoid inheriting another library's verifier defaults. Preserve immutable model/source pins, required downloaded assets and independent core/model versions. Runtime dependencies must use real compatible published pins during publication, while demos may retain project dependencies.

For JVM-only modules use `kind: jvm-library` in the existing `android_modules` schema and `packaging: jar`, with explicit JVM/bytecode/dependency checks. Keep the JVM Maven task/property integration and real upload guards/receipt capture. Check whether the chosen Gradle plugin supports JVM; the Android publication plugin must not be applied blindly. Validate JAR/POM/sources/javadoc/module metadata and API/runtime variants. Reuse the shared release engine instead of inventing another journal format. JVM-only workflows need no Android SDK, APK signing or screenshot jobs. Mixed builds dispatch checks per selected module.

Use generic project-check inventories and localization checks where supported, retaining domain tests. Candidate screenshot manifests and matching hashes are evidence of rendering/provenance, not maintainer approval. Keep candidate generation, baseline acceptance and regression comparison separate. Never auto-accept changed images or source hashes to silence CI. Headless libraries need no invented UI; emulator/host images do not prove physical hardware, biometric or camera behavior.

Use the shared signing/vault workflow and compatible application conventions. Keep APK signing separate from Maven OpenPGP signing. Missing local signing material should produce actionable verification and preserve normal project sync; do not silently rotate signing identity. Never commit keys, vaults, passwords or local signing properties. Retain the shared tool's existing user-owned secret storage rather than introducing a parallel `.deploy/` vault for an Android library.

Finish with the pins and module kinds inspected, files migrated/retained and why, exact checks actually run, remaining upstream/owner setup and a pushed branch/PR. A tooling migration is not authorization for package publication, live environment changes or production deployment.
