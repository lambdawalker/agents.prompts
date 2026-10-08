# Standalone library documentation

Act as a senior library maintainer, technical writer, and documentation engineer. Inspect this repository and implement a complete documentation system for its actual capabilities.

Target repository: <REPOSITORY_URL_OR_CURRENT_CHECKOUT>
Existing documentation URL, if any: <OPTIONAL_URL>
Canonical language and target locales: preserve existing choices; default to English and Spanish for a new multilingual site unless the user specifies otherwise.
Release-history scope: discover confirmed releases and state the initial archive boundary.
Hosting: preserve the existing provider; default to GitHub Pages for GitHub repositories without an established provider.

GOAL

Humans should be able to evaluate, install, integrate, customize, troubleshoot, and maintain the project using a polished HTML website. They should only need to leave the site to inspect full demo source, download artifacts, or interact with the repository. AI agents should have a small Markdown entry point and dedicated, factual Markdown documentation that does not require parsing HTML, site components, or JavaScript. Both surfaces must describe the same implementation and clearly identify their version scope.

Use android.apexfission.permissions as an architectural reference, not a source of APIs or platform assumptions to copy:
https://github.com/lambdawalker/android.apexfission.permissions
Its pattern is sites/ for an Astro/Starlight site, docs/agents/ for canonical agent guides, IMPORT.md for installation facts, runnable application demos, code-rendered screenshot fixtures, and build-time asset synchronization.

1. INSPECT BEFORE WRITING

Read repository contributor instructions, public source, tests, examples, package metadata, release automation, existing documentation, and screenshot tooling. Reuse established paths and tools when practical. Do not overwrite unrelated work or introduce a second documentation stack without a concrete need.

Inventory supported features, exported declarations, configuration, platform/version requirements, lifecycle and ownership rules, errors, limitations, and existing runnable demonstrations. Trace important claims to source or tests. Never invent APIs, package versions, compatibility claims, demo links, test results, or screenshots.

Create a compact coverage table mapping each public feature to its human guide, agent guide, runnable example, and screenshot scenario where useful. Identify genuinely missing examples; do not add redundant demos simply to increase their count. Proceed with reasonable project-specific decisions and record assumptions.

2. ESTABLISH CLEAR ENTRY POINTS AND OWNERSHIP

Prefer this structure, adapting it to the repository:

- README.md: concise project introduction, installation summary, human site link, agent entry link, and demo link.
- IMPORT.md: canonical current published installation coordinates and supported dependency syntaxes, or an equivalent existing file.
- AI_INTEGRATION_GUIDE.md: a short pointer to docs/agents/index.md, not a duplicate guide.
- docs/agents/index.md: consumer-agent entry point and retrieval map.
- docs/agents/quickstart.md, concepts.md, api.md, recipes.md, limitations.md, troubleshooting.md, migration.md: focused integration documents. Split large references by subsystem when useful.
- docs/screenshots/: curated original documentation images, a scenario manifest, and regeneration instructions, only if visuals are useful.
- sites/: human site source, locked dependencies, theme, build scripts, and local preview instructions.
- Existing examples/app/sample modules: actual runnable demonstrations.
- scripts/ and CI workflows: reproducible rendering, synchronization, validation, and deployment.

Keep AGENTS.md for repository contributor instructions. Do not use it as the consumer API manual or imply that the repository contains an autonomous agent service. Link to the consumer entry point from it where appropriate. Preserve old documentation entry links with short redirects when reorganizing.

Define canonical ownership explicitly: source for API behavior, release metadata for availability, compiled demos for examples, docs/agents for machine-oriented contracts, and screenshot fixtures for images. Generate repeated installation facts, signatures, snippets, and asset copies where practical. Human explanations may be authored separately, but must share the same verified facts. Never manually maintain several competing version numbers.

3. BUILD A COMPLETE HUMAN WEBSITE

Preserve the current framework; for a new documentation site, Astro/Starlight is a reasonable default after checking current official documentation and project compatibility. Keep site dependencies separate from library runtime dependencies.

Include:
- Overview: purpose, suitable use cases, boundaries, and a realistic first example.
- Installation: actual published coordinates, prerequisites, platform requirements, and configuration.
- Complete quickstart: dependencies, imports, setup, executable integration, and expected behavior.
- Conceptual guides: control flow, state, ownership, asynchronous behavior, lifecycle, threading, resources, and recovery where relevant.
- Feature and customization guides with copyable examples and meaningful explanations of defaults.
- Public API reference, including overloads, parameters, defaults, return values, contracts, and links to source.
- Task recipes that explain when and why to use each approach.
- Runnable demo catalog and walkthroughs.
- Limitations, troubleshooting, migration, and compatibility information.
- Build, test, screenshot refresh, contribution, and release instructions appropriate to this repository.
- A useful screenshot gallery where applicable, and a visible AI documentation link.

Do not substitute a link to GitHub or the agent manual for an essential human explanation. Integrators should not have to reconstruct the setup from scattered source files. Label partial snippets clearly; provide a complete runnable example nearby.

Use readable typography, responsive navigation, accessible contrast, keyboard navigation, heading anchors, syntax highlighting, copyable code, and sensible image sizing. Add search when the documentation size warrants it. Respect reduced motion. Test project-subpath URLs rather than assuming deployment at the domain root.

4. WRITE DEDICATED AGENT DOCUMENTATION

Keep docs/agents/index.md small. Include the project purpose and non-goals, documentation ref/version, authoritative installation pointer, a task-to-API decision table, critical invariants, and links to focused documents. An agent should be able to choose its next file without reading the whole corpus.

Document exact public declarations and imports, all public overloads and relevant types, defaults, parameter meanings, preconditions, postconditions, errors, side effects, callback timing, thread/lifecycle constraints, cancellation, cleanup, ownership, and unsupported cases where applicable. Keep explanations adjacent to declarations. Clearly distinguish public API from internals.

Include a complete minimal integration, task-oriented recipes, known failure patterns and corrections, migration notes, and links to runnable source. Explain what the library does and what the host must do. Include negative constraints that prevent plausible but incorrect integrations. Label placeholders and pseudocode; prefer verified code. Critical information must exist as text, not only in screenshots.

Publish these canonical files as raw Markdown at stable site URLs, for example <SITE_BASE>/agents/index.md, and publish the installation document similarly. Add <SITE_BASE>/llms.txt as a concise discovery index linking to raw Markdown. Treat llms.txt as a convenience, not a guarantee that every agent discovers it.

During publishing, keep links between agent guides pointing to raw .md URLs, including anchors. Resolve code links to the correct repository ref. Do not turn every relative Markdown link into a GitHub HTML page. Preserve code fences, external URLs, images, and fragments with a Markdown-aware transformation or a tested equivalent. Recurse through nested guide directories. Clear generated targets so deleted source files do not survive as stale outputs. Validate the published raw files and their links without requiring client-side rendering.

5. CONNECT EXAMPLES TO REAL DEMOS

Use existing runnable demo applications when sufficient; add focused examples only for meaningful coverage gaps. Each catalog entry must name the demonstrated feature, prerequisites, exact run command or navigation route, actions to try, expected result, important failure/recovery paths, and full source location.

Compile or execute the relevant demos. Extract site snippets from named regions in runnable examples where practical, or provide another repeatable drift check. Keep illustrative fixtures distinct from production-ready integrations. For versioned docs, pin demo/source links to that release tag or commit; label main-branch examples as development examples.

6. IMPLEMENT REPRODUCIBLE SCREENSHOTS WHEN THEY HELP

For UI projects, capture actual rendered components or runnable demos. For nonvisual libraries, skip screenshots unless a real visualization or demo improves understanding; document that decision. Never manufacture a UI screenshot with image generation or manual redrawing.

Choose the existing platform-appropriate renderer where possible. Android Compose previews can document deterministic library-owned UI. Emulator/device instrumentation is needed for actual system UI and platform interactions. Static preview images do not prove permission dialogs, navigation, camera hardware, lifecycle transitions, or other platform behavior.

Define named scenarios for the important documented states: primary flow, configuration differences, empty/loading/error/recovery states where present, and selected accessibility/layout variants. Avoid an exhaustive permutation matrix without value. Use synthetic data; control viewport, density, OS/API level, theme, locale, font scale, fonts, clock, animation position, randomness, and network inputs as needed.

Provide separate commands for rendering candidates, intentionally updating accepted images, and validating against accepted images. Normal CI must never silently accept changed baselines.

Create a machine-readable manifest mapping stable image filenames to scenario IDs, source fixture paths, capture kind, render configuration, captions/alt text, and related demos/guides. Record captured source commit and relevant input/toolchain hashes. Store verification of later unchanged inputs separately: a new commit alone does not make an identical image stale. Avoid self-referential commit hashes in generated committed files.

Automate copying selected render outputs into stable docs/screenshots filenames. Fail on missing required scenarios. Keep one canonical curated image set; treat copies in site public assets as generated and ignored. If the test tool requires separate baseline locations, document and validate the relationship rather than silently maintaining conflicting copies.

Generate images before finalizing visual explanations. Open and inspect them, then use what is actually visible to write captions, walkthroughs, and surrounding content. Check clipping, text readability, incorrect states, theme problems, and misleading crops. An image's existence is not evidence that the capture is correct. If rendering is unavailable, report the exact blocker and do not claim new captures or substitute unlabelled old images.

7. SCREENSHOT AND RELEASE POLICY

Use the same rendering implementation locally and in CI, with distinct responsibilities:

A. Authoring: the implementing agent renders affected scenarios, inspects the results, updates accepted documentation images intentionally, and submits code, docs, and image changes together for review.

B. Pull requests: validate relevant screenshot scenarios and documentation synchronization when UI, fixtures, resources, rendering dependencies, or capture configuration changes. Upload candidates and useful diffs on failure. Ensure workflow filters and required-check design do not leave required checks pending. A manual workflow must remain available for regeneration and debugging.

C. Site builds: synchronize reviewed images and raw agent Markdown, validate the manifest and links, and build HTML. A prose-only change should not require a full device/emulator run. Missing images required by a page must fail validation.

D. Release preflight: verify documentation, examples, and screenshot evidence for the exact release source and render configuration. Reuse successful evidence only when source/ref and relevant inputs are demonstrably compatible; never use an arbitrary latest artifact. If evidence is missing, stale, or expired, rerun validation before publication. Use the exact release SHA, not a moving branch head.

E. Publication: publish reviewed content and verified assets. Do not first discover UI changes or silently update baselines during artifact upload. Screenshot regeneration is not mandatory for a release whose relevant inputs are unchanged and have valid verification evidence.

Keep documentation deployment separately retryable from package upload. A site failure after a successful package release must not cause that package version to be uploaded again. Promote latest published installation facts only after registry availability is confirmed. Make publication retry-safe and prevent older concurrent deployments from replacing newer documentation. Review event-trigger behavior explicitly; do not assume a workflow's automated commit starts another push workflow.

Use read-only permissions for validation and isolate deployment permissions to trusted deployment jobs. Do not expose publishing credentials to screenshot rendering or untrusted pull requests. Preserve existing publishing behavior unless a change is necessary and within the requested scope.

8. VERSIONED AND MULTILINGUAL DOCUMENTATION

Read and apply [Versioned and multilingual documentation](versioned-multilingual-documentation.md) as the shared policy for this workflow. Implement complete versioned guides/API/examples and exact-version installation pages, with module-aware version/language navigation, canonical-source translation hashes, and explicit same-version fallback. Preserve one authored source tree; keep documentation in this repository unless another ownership model already exists. A new multilingual site includes English and Spanish by default; honor explicit locale choices. If the supplement is unavailable, report that dependency rather than claiming the full workflow was implemented.

Archive confirmed per-module release facts before latest pointers are overwritten. Render all retained snapshots on every deployment from durable immutable inputs; do not depend on prior deployed files or expiring artifacts. Keep latest IMPORT.md distinct from each historical installation page. Do not translate identifiers or accidentally route a bundled-model example to detector-only installation instructions.

9. VALIDATION

State whether the website and agent guides describe a release or main. Do not present unreleased APIs as supported by the current published dependency. If stable and development docs coexist, make the distinction visible in both HTML and Markdown. Source links, demos, installation facts, screenshots, and API contracts must agree with the stated scope.

Before completion:
- Build the production site from a clean dependency install.
- Validate internal links, anchors, images, raw agent URLs, installation values, and deployment base paths.
- Check public API and feature coverage against source and the coverage table.
- Compile or run the canonical quickstart and relevant demos where supported.
- Render and inspect affected screenshots; validate accepted baselines separately from updating them.
- Inspect representative rendered site pages on desktop and narrow screens when a browser is available.
- Confirm generated assets are reproducible and stale generated files are removed.
- Confirm CI jobs distinguish validation, intentional image updates, and deployment.

Report checks actually performed, failures, and checks blocked by the environment. Do not claim deployment or visual verification without evidence. Deliver the implemented structure, a short maintenance guide with exact commands, and any precise hosting setup the owner must complete. Follow the user's authorization for committing or publishing; do not make unrelated repository changes.

SUCCESS CRITERIA

A human can complete an integration from the website; an agent can do the same starting at one Markdown file without parsing HTML. Both obtain the same verified API and version facts for the selected module and release. Historical guides remain accessible; requested translations cover consumer documentation and navigation, with explicit same-revision fallback when missing or stale. Demos are runnable and linked. Useful screenshots are real, reproducible, reviewed, and traceable. Future maintainers have one clear workflow for keeping all surfaces aligned.


## Companion prompts and scope

This is the complete primary workflow for a standalone library. It does not require a central system architecture repository. Keep the library's architecture and integration contracts locally; link to dependencies without claiming ownership of their documentation.

For a deeper API documentation audit, read [AI-agent documentation](ai-agent-documentation.md). For authorized Android release-tooling implementation, read [Android library publishing](android-library-publishing.md). These are specialist supplements, not prerequisites for using this prompt. If the library belongs to a larger coordinated system, use [System-integrated library documentation](system-integrated-library-documentation.md).

For published packages, generate IMPORT.md from an editable template such as docs/templates/IMPORT.md.template, existing publication metadata, and the last confirmed release. Mark generated files and provide deterministic generation and verification commands. Preserve confirmed published coordinates when development coordinates change. Never infer publication from a proposed version or workflow run number. Preserve established release automation; documenting a site does not authorize a package release.
