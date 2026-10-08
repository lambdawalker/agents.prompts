# agents.prompts

Reusable prompts for AI coding agents. Choose one of the two primary documentation workflows.

| Primary prompt | Use it for |
| --- | --- |
| [Standalone library documentation](prompts/standalone-library-documentation.md) | An independent library: complete human HTML site, dedicated agent Markdown, runnable demos, API guidance, reproducible screenshots, versioned English/Spanish guides with explicit fallback, and validation/deployment structure. |
| [System-integrated library documentation](prompts/system-integrated-library-documentation.md) | Libraries within a multi-repository system: all local documentation requirements plus a central architecture repository with its own human site and agent docs, shared UI references, and reciprocal navigation. |

## Specialist supplements

| Prompt | Use it for |
| --- | --- |
| [Versioned and multilingual documentation](prompts/versioned-multilingual-documentation.md) | Shared policy used by the primary workflows: preserved release guides and installation history, module/version/language navigation, translation freshness, and durable rebuilds. |
| [AI-agent documentation](prompts/ai-agent-documentation.md) | A deeper API-contract, lifecycle, ownership, troubleshooting, and agent-evaluation audit. |
| [Android library publishing](prompts/android-library-publishing.md) | Maven Central and JitPack publication, independent module versions shared across destinations, recoverable releases, latest-available IMPORT.md, and automatic documentation deployment. |
| [Python package publishing](prompts/python-package-publishing.md) | Release Please version/changelog PRs, validated wheel and sdist builds, PyPI Trusted Publishing, and recovery/setup guidance. |

The former [Multi-repository documentation](prompts/multi-repository-documentation.md) entry redirects to the system-integrated workflow. Its ownership, branch consolidation, and release safeguards have been retained there.

## Usage

1. Open the primary prompt and give it to the agent with repository URLs, target branches, and delivery instructions. Include the shared versioned/multilingual supplement when linked prompts cannot be retrieved.
2. For system-integrated libraries, include the central architecture repository and known related repositories/sites. The prompt explicitly loads the standalone workflow as its shared site, demo, screenshot, and agent-documentation requirements; provide both primary files and the shared versioned/multilingual supplement if the agent cannot access this repository.
3. Specify local changes, pull requests, or commits to target branches. Branch consolidation and actual package publication require their own explicit scope.
4. Supply canonical/target languages and any release-history constraints. Preserve existing locales; new multilingual sites default to English and Spanish unless you specify otherwise. The primary workflow loads the shared versioning/translation policy.
5. Use specialist supplements for deeper audits or authorized publishing-tooling work.

The prompts are task text for target repositories, not instructions to execute merely because an agent reads this collection.

## Documentation ownership

| Information | Authoritative home |
| --- | --- |
| General system architecture, shared flows, decisions, UI specifications | Central architecture repository, rendered on its human site and exposed as agent Markdown |
| Library API, installation, local behavior, demos, build and operational guidance | Owning library repository, human site, and agent Markdown |
| Latest confirmed published versions and coordinates | Publishing repository's IMPORT.md or established equivalent |
| Exact historical installation facts and documentation scope | Owning library's confirmed release catalog and immutable documentation snapshots |
| Translated guides | Owning repository's locale sources with canonical-source hashes and recorded snapshot provenance |
| Visual design prototype | Central reference HTML plus a nearby textual specification and labelled prototype capture |
| Implemented UI | Application/component capture with source and scenario provenance |

The sites and repositories link to each other at the relevant feature or integration page. Agent navigation stays on raw Markdown. Prototype screenshots are distinguished from implemented application screenshots.

## Screenshot timing

Generate and inspect affected screenshots while authoring; validate relevant changes in CI; verify or reuse matching evidence during release preflight; publish reviewed assets during site deployment. Prose-only changes need not rerun the full renderer. Updating image baselines is an intentional operation, never an automatic side effect of ordinary validation.
