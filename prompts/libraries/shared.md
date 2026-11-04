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

## Deployment questions, defaults and countdowns

- A plain interactive deployment-script invocation must default to **deploy**, not preview-only: complete prerequisites/build, produce a successful preview/plan, then offer the standard five-second deployment confirmation and apply the previewed changes. Provide an explicit preview-only action. Explicit build/status/setup/preview actions must never escalate to deployment. Noninteractive/CI execution still requires explicit deployment intent and approval; silence, EOF or an omitted action must not authorize CI deployment.

Apply these rules whenever a library owns deployment tooling, regardless of implementation language.

- Discover values from authenticated services and load valid saved answers before asking. Skip questions already answered by explicit flags or valid scoped settings. When a question is necessary, always provide a validated, sensible default where one is available; show the default and its consequence in plain language.
- For interactive deployment questions with a valid nonsecret default, show a visible five-second countdown. Enter accepts the displayed default immediately; a supplied answer takes effect immediately; silence until the timer expires selects exactly that default. State the timeout behavior beside each question. Do not time out an unresolved free-text question or invent identifiers, credentials or secret values.
- Display discovered account/identity and deployment targets rather than requiring operators to copy identifiers the tool can retrieve. Ask whether to proceed with a five-second default-yes countdown, while retaining explicit/saved target mismatch checks.
- Use operator language for infrastructure questions (for example, “Do you have an S3 bucket for deployment state?”). Where first-run resource creation is supported and authorized, explain the automatic creation default, choose collision-safe names and secure settings without further naming questions, and remember the result. Never create a replacement for an inaccessible or missing previously configured resource merely because a timer expired.
- Escape, Ctrl+C, EOF, a closed terminal and input errors cancel; they are never silence or approval. Clean up readers and terminal state, and ensure buffered input cannot answer a later question.
- Secret entry/confirmation and destructive operations require deliberate input without automatic timeout approval. CI/noninteractive runs require explicit complete inputs and applicable approval flags; interactive countdowns never authorize CI work or change a preview/status/build action into deployment.
- Test defaults, overrides, yes/no/Enter, timeout, cancellation, EOF, saved-answer reuse and interrupted setup with fake clocks/mocked services.

## Deployed artifact ownership, tagging and reports

Apply only to repositories that own deployable infrastructure. Distinguish these roles explicitly:

| Repository role | Responsibility |
|---|---|
| Application/aggregator | Compose features and shared services; aggregate their inventories into application/environment reports while preserving each creating feature's ownership. Report host-owned integrations separately. |
| Feature/library | Inventory and report only resources the feature creates, including its standalone test deployment when present. Declare host-provided dependencies as external; do not deploy or copy an application dashboard, administer host resources, or relabel application/shared resources as feature-owned. Supply safe inventory metadata for host aggregation. |

- Every deployed artifact needs discoverable project/environment ownership and purpose. Add native descriptions where supported and nonsecret tags/labels for project, environment, feature/component, creating repository and manager. Use a domain-derived identity where established; without a domain, use `<project-name>-<environment>` as the human-readable Name. Keep logical identity, physical provider ID and display name distinct; do not rename existing resources merely to add labels.
- For resources that cannot carry tags/descriptions (such as policy attachments or child configurations), keep exact logical/physical identity, owner, purpose and parent relationships in the managed inventory/report. Never infer ownership from a matching name/tag alone. Preserve unrelated labels and distinguish operator-provided/shared dependencies from owned resources.
- Produce JSON, Markdown and self-contained HTML from one normalized safe snapshot. Reuse the project's reporting protocol/library rather than inventing a parallel schema or copying an aggregator's implementation. See the [Infra Composer artifact report protocol](https://github.com/lambdawalker/design.infra.composer/blob/main/docs/artifact-reports.md).
- Feature reports show feature repository, deployment scope, operation/outcome, revision/dirty state, timing, counts, hierarchy, planned actions, physical/logical IDs and evidence age. Application reports add application/shared scope and feature selection, retaining exact creating owners and ancestor context.
- Generate reports for previews and deployment attempts, including partial failures; provide a report-only command and optional read-only verification. Preserve successful partial observations. State records mean recorded, offline catalogs mean unknown, and only exact scoped live evidence proves present/absent. Failed/unsupported checks mean unknown, never absent or success.
- Preserve observation timestamps and use scope/identity-checked caches. Store reports privately and atomically under scoped deployment records or ignored `.deploy/reports/`; print their paths. Never expose raw state, provider diagnostics, credentials, policies, environment maps, object contents or arbitrary configuration. Copy only explicitly allowlisted safe output settings and escape HTML/Markdown values.
- Tag/report changes must cover app-owned and feature-owned resource boundaries in tests: ownership, hierarchy, invalid/failed evidence, cache scope, unchanged labels, secrets omission and partial outcomes. Do not deploy cloud resources merely to validate reports.

## Deliver branches and pull requests

For requested implementation/documentation work, default to a task branch, push the completed changes, and open or update a pull request. Honor explicit local-only instructions. Do not stop after a local commit or repeatedly ask for routine push permission already covered by the task. Include concrete behavior changes, validation actually run, limitations, and relevant PR links in delivery.

Do not infer permission to merge, delete branches, publish packages, move release tags, deploy production, or send messages to others. If the environment, repository permissions, or approval review blocks a push, complete unaffected work and report the exact blocker; do not claim the PR exists.
