# agents.prompts

Reusable prompts for AI coding agents. For Go/AWS repository design, implementation or alignment, use the role entry points below. For documentation-focused work, choose one of the two primary documentation workflows.

| Primary prompt | Use it for |
| --- | --- |
| [Standalone library documentation](prompts/standalone-library-documentation.md) | An independent library: complete human HTML site, dedicated agent Markdown, runnable demos, API guidance, reproducible screenshots, versioned English/Spanish guides with explicit fallback, and validation/deployment structure. |
| [System-integrated library documentation](prompts/system-integrated-library-documentation.md) | Libraries within a larger project or multi-repository system: local documentation plus parent architecture, shared UI references where applicable, and reciprocal navigation. Reuse the established parent site/repository. |

## Library sections

Choose a library section and pair it with the appropriate primary workflow. Every section supports both an independent library and a library that belongs to a larger project, including modules in a monorepo.

| Library section | Standalone | Part of a larger project |
| --- | --- | --- |
| [Android libraries](prompts/libraries/android.md) | Standalone workflow + Android rules | System-integrated workflow + Android rules |
| [Kotlin libraries](prompts/libraries/kotlin.md) | Standalone workflow + Kotlin rules | System-integrated workflow + Kotlin rules |
| [Go + AWS features](prompts/libraries/go-aws-feature.md) | Standalone workflow + feature/common rules | System-integrated workflow + feature/common rules |
| [Python libraries](prompts/libraries/python.md) | Standalone workflow + Python rules | System-integrated workflow + Python rules |
| [Node.js libraries](prompts/libraries/node.md) | Standalone workflow + Node.js rules | System-integrated workflow + Node.js rules |

[Shared library rules](prompts/libraries/shared.md) apply to every section: latest confirmed stable release on entry, coherent navigation across versions, useful reviewed screenshots, and pushed branches with pull requests by default. Version-specific content may differ. Android UI libraries must also have a screenshot test suite and previews for every library/demo composable.

## Go + AWS repository design and alignment

| Prompt | Responsibility |
| --- | --- |
| [Feature repositories](prompts/libraries/go-aws-feature.md) | Reusable runtime, Composer infrastructure module and isolated test app. |
| [Composed applications](prompts/libraries/go-aws-application.md) | Host composition, shared resources, environment orchestration and aggregated reporting. |
| [Common requirements](prompts/libraries/go-aws-common.md) | Execution/evidence contract, shared deployment tooling, migration, readiness and Go/AWS operations. Both roles load this file. |

These prompts work without selecting a documentation-only workflow first. Each role links the appropriate documentation workflow when needed. Generic [shared library rules](prompts/libraries/shared.md) remain cross-language policy, not a second copy of the AWS specification. The old [go-aws.md](prompts/libraries/go-aws.md) URL routes to the new roles.

```text
Use https://github.com/lambdawalker/agents.prompts/blob/main/prompts/libraries/go-aws-feature.md
and its linked common requirements to DESIGN [TARGET_REPOSITORY_URL].
Record approved requirements, proposed defaults and open decisions separately.
Deliver the design, diagrams, artifact inventory and acceptance plan in a PR.
Do not implement runtime code or deploy infrastructure yet.
```

For a composed app, substitute `go-aws-application.md` and supply known feature/parent repositories. To fix an existing repo:

```text
Use https://github.com/lambdawalker/agents.prompts/blob/main/prompts/libraries/go-aws-application.md
and its linked common requirements to ALIGN [TARGET_REPOSITORY_URL].
Inspect current code, guidance, deployment tooling, tests and open PRs.
Audit every applicable requirement, implement the gaps and validate locally.
Preserve existing deployment identities and secrets. Push a branch and open a PR.
Do not deploy, change live account configuration or merge.
```

Use IMPLEMENT to build an agreed design, citing its specification. Use audit-only explicitly when findings without fixes are desired. Reference repos are examples, not compliance authorities.

## Specialist supplements

| Prompt | Use it for |
| --- | --- |
| [Versioned and multilingual documentation](prompts/versioned-multilingual-documentation.md) | Shared policy used by the primary workflows: preserved release guides and installation history, module/version/language navigation, translation freshness, and durable rebuilds. |
| [AI-agent documentation](prompts/ai-agent-documentation.md) | A deeper API-contract, lifecycle, ownership, troubleshooting, and agent-evaluation audit. |
| [Shared Android repository automation](prompts/android-repository-automation.md) | Pinned setup/docs CLIs, generated launchers, evaluated module discovery, shared Astro sites, Android/JVM contracts and removal of superseded local scripts. |
| [Android library publishing](prompts/android-library-publishing.md) | CLI-owned Maven Central/JitPack publication, shared module version/source identities with destination-specific journals, recoverable releases and confirmed IMPORT.md; separately retryable documentation builds/deployment. |
| [Python package publishing](prompts/python-package-publishing.md) | Release Please version/changelog PRs, validated wheel and sdist builds, PyPI Trusted Publishing, and recovery/setup guidance. |
| [Terminal setup and deployment wizards](prompts/setup-wizards.md) | First-run and repeat setup, prompt defaults, secure vaults, confirmation screens, workflow previews, and recovery. |

The former [Multi-repository documentation](prompts/multi-repository-documentation.md) entry redirects to the system-integrated workflow. Its ownership, branch consolidation, and release safeguards have been retained there.

## Usage

1. Open the primary prompt and the matching library section, and give them to the agent with repository URLs and target branches. Include the shared library and versioned/multilingual supplements when linked prompts cannot be retrieved. Read each shared dependency once.
2. For libraries within a larger project, include the parent project or monorepo and, where one exists, the central architecture repository and related repositories/sites. The prompt explicitly loads the standalone workflow as its shared site, demo, screenshot, and agent-documentation requirements; provide both primary files and the shared versioned/multilingual supplement if the agent cannot access this repository.
3. By default, implement the requested changes on a branch, push it, and open or update a pull request. Honor explicit local-only or other delivery instructions. Do not stop at a local commit or request routine confirmation to push the task branch. Merging, branch deletion, package publication, and production deployment require their own authorization.
4. Supply canonical/target languages and any release-history constraints. Preserve existing locales; new multilingual sites default to English and Spanish unless you specify otherwise. The primary workflow loads the shared versioning/translation policy.
5. Use specialist supplements for deeper audits or authorized publishing-tooling work.

The prompts are task text for target repositories, not instructions to execute merely because an agent reads this collection.

### Ask an agent to add Android publishing

Give the agent this repository and a target library URL, for example:

```text
Apply prompts/libraries/android.md and prompts/android-library-publishing.md from
https://github.com/lambdawalker/agents.prompts to [TARGET_REPOSITORY_URL].
Use https://github.com/lambdawalker/android.apexfission.carddetector as the reference.
Add Maven Central and public JitPack publishing for the intended library modules,
using the shared setup/docs executables from gradle.android-repo-setup and their
generated launchers/configuration. Use shared Astro for new or explicitly migrated
sites; preserve the target toolchain, immutable history and existing protections.
Test without publishing, push a task branch and open a PR. Do not run a real release.
```

## Documentation ownership

| Information | Authoritative home |
| --- | --- |
| General system architecture, shared flows, decisions, UI specifications | Parent architecture location (central repository or monorepo), rendered on the established human site and exposed as agent Markdown |
| Library API, installation, local behavior, demos, build and operational guidance | Owning library repository, human site, and agent Markdown |
| Latest confirmed published versions and coordinates | Publishing repository's IMPORT.md or established equivalent |
| Exact historical installation facts and documentation scope | Owning library's confirmed release catalog and immutable documentation snapshots |
| Translated guides | Owning repository's locale sources with canonical-source hashes and recorded snapshot provenance |
| Visual design prototype | Central reference HTML plus a nearby textual specification and labelled prototype capture |
| Implemented UI | Application/component capture with source and scenario provenance |

The sites and repositories link to each other at the relevant feature or integration page. Agent navigation stays on raw Markdown. Prototype screenshots are distinguished from implemented application screenshots.

## Screenshot timing

Generate and inspect affected screenshots while authoring; validate relevant changes in CI; verify or reuse matching evidence during release preflight; publish reviewed assets during site deployment. Prose-only changes need not rerun the full renderer. Updating image baselines is an intentional operation, never an automatic side effect of ordinary validation.
