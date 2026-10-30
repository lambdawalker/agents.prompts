# Shared library rules

Apply these rules with the selected [standalone](../standalone-library-documentation.md) or [system-integrated](../system-integrated-library-documentation.md) workflow and the relevant library section. Read each shared dependency once. These are task prompts for the target repository; reading this collection alone does not authorize modifying unrelated repositories.

## Standalone and larger-project scope

For a standalone library, keep the API, installation, examples, site, agent guides, and maintenance instructions with the library.

For a library within a larger project, keep its API contracts and release facts with its owning module/repository; link to parent architecture, host responsibilities, and integration flows. This applies to monorepo modules and multi-repository systems. Reuse an existing parent documentation site or architecture repository when established. Do not require a new central repository merely because a library has a host application. Preserve module-specific release streams and provide reciprocal links at the relevant guide or feature.

## Default to the latest confirmed stable release

The documentation root must open the latest confirmed stable release for the primary library/module, not Development. Derive it from confirmed publication metadata and the project's version/channel policy. Sort versions semantically using that ecosystem's rules; do not sort lexically or infer publication from a tag, proposed version, or successful upload alone. Resolve releases independently for independently versioned modules.

Keep Development explicitly selectable and visibly labeled in HTML, Markdown, and search. If no confirmed stable release exists, open an honestly labeled prerelease or development view according to the documented project policy; explain the absence of a stable release. Never invent one.

A stable site alias must advance only after publication is confirmed. Historical installation pages must retain their exact coordinates. Provide a usable link when automatic navigation is unavailable and preserve valid old entry URLs. Follow the detailed [versioned and multilingual policy](../versioned-multilingual-documentation.md).

## Make navigation understandable across versions

Keep the same information architecture, section names, and ordering wherever those topics exist: overview, installation/quickstart, integration, API, troubleshooting/migration, and project resources. Switching to a release and back to Development must restore the appropriate menu and selected state. Avoid two unrelated menus for the landing page and versioned guides.

Version content need not be identical. Preserve each release's guides and API facts. When a page does not exist in the target version, show an understandable fallback to that scope's overview or visibly mark the unavailable topic. If a sidebar offers useful material from Development or a different module, label that destination before the reader follows it; never pass current API off as historical content. Do not retain misleading dead links just to make menus identical.

Preserve the current guide when switching version or language if it exists. Keep module/version/language context on shared resources where practical, while explicitly labeling current maintenance/release/media resources as shared rather than archived release contracts. Keep development-only contributor information distinguishable from consumer guidance. Validate dropdown destinations, catalog links, deep links, raw Markdown, anchors, and deployed subpaths. Test release → Development → release, missing-page fallback, independent modules, locales, and browser back/forward selected states when browser testing is available.

## Explain the library visually where useful

Keep a useful existing landing-page design. Enrich it with available, relevant screenshots that explain the primary flow, important states, customization, and runnable demos. Group images into short walkthroughs with captions and descriptive alt text; use responsive sizing and lazy loading for supporting galleries. Select most or all useful scenarios without repeating near-identical images merely for coverage.

Use actual rendered components or demos, inspect the result, and record source/scenario/toolchain provenance. Never manufacture screenshots. Distinguish synthetic host previews, actual application/device captures, prototypes, and historical recordings. Screenshots from current development may illustrate an archived landing page only with an explicit label; they are not evidence that an old release offers that UI.

Nonvisual libraries do not need a fabricated UI or screenshot suite. Use real demo output, API examples, or an exact diagram only where these explain something useful. Keep essential setup and contracts in text. Preserve one curated image set and validate hashes/dimensions and required assets before publishing. Full screenshot policy remains in the selected primary workflow.

## Deliver branches and pull requests

For requested implementation/documentation work, default to a task branch, push the completed changes, and open or update a pull request. Honor explicit local-only instructions. Do not stop after a local commit or repeatedly ask for routine push permission already covered by the task. Include concrete behavior changes, validation actually run, limitations, and relevant PR links in delivery.

Do not infer permission to merge, delete branches, publish packages, move release tags, deploy production, or send messages to others. If the environment, repository permissions, or approval review blocks a push, complete unaffected work and report the exact blocker; do not claim the PR exists.
