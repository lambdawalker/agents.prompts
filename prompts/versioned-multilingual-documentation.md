# Versioned and multilingual documentation — shared supplement

Act as a documentation and release engineer. Apply this policy with the target repository's primary documentation prompt. Preserve existing tools and authoritative sources; adapt paths, release schemes and language choices to the project. Reading this collection does not authorize changing its reference repositories.

## Inputs and defaults

- Target repositories/modules and primary documentation prompt: [SUPPLY_OR_DISCOVER]
- Canonical language: [PRESERVE_EXISTING; OTHERWISE en]
- Target documentation languages: [PRESERVE_EXISTING_AND_HONOR_REQUESTED; DEFAULT en AND es FOR A NEW MULTILINGUAL SITE]
- Published releases and development channels: [DISCOVER_CONFIRMED_EVIDENCE]
- Existing site, hosting, documentation and release automation: [DISCOVER]
- Release automation scope: [AUDIT_AND_DOCUMENT / IMPLEMENT_INTEGRATION]
- Delivery: [LOCAL_CHANGES / PULL_REQUESTS / AUTHORIZED_TARGET_BRANCH_COMMITS]

Implement version-aware, multilingual guides, API reference, examples, limitations, migration and installation pages. Installation history alone is not a complete versioned library manual. A developer or agent using an old dependency must be able to retrieve matching instructions without accidentally using the newest API. If no releases exist, provide honestly labeled development/source documentation and the structure for future releases; do not fabricate a release history. Honor an explicit English-only or other language choice and report precisely what is translated.

## One authored source, persistent history

Keep documentation in the owning library repository by default. A separate documentation repository is not necessary for versioning or translations. Preserve an established canonical English tree such as docs/agents/ rather than copying it into docs/en/ merely to match an example. Choose one authoritative source for each guide; humans and agents can consume generated representations of it.

Suggested responsibilities, adapting names to existing conventions:

| Path or data | Responsibility |
| --- | --- |
| docs/agents/ or established canonical-language tree | Current authored integration contracts and guides |
| docs/es/ or other locale trees | Translated prose corresponding to canonical guides |
| Translation manifest | Canonical content hash/revision for each translated page, plus provenance where needed |
| docs/releases/history/<module>/<version>.json | Small persistent release/documentation catalog entry |
| IMPORT.md | Latest confirmed installation choices per independently released module |
| sites/ or existing site directory | Templates, navigation, generation and validation |
| Generated site output | Every retained version/locale and its raw Markdown; ignored by source control |

Do not manually copy whole documentation directories for every patch release, commit repeated generated HTML, or duplicate large media per language. Git already retains authored revisions. Generate historical views from recorded immutable source/documentation revisions using the current renderer. Shared immutable assets and content-addressed reuse are appropriate when provenance agrees. Reuse content across versions only when matching inputs demonstrate it is the same; do not assume all patch releases share an API.

Every deployment must contain all cataloged versions, whether rebuilt or reconstructed from durable verified storage. A static-site deployment can replace its entire output. Never depend on leftover files in the previous deployment, an ephemeral runner cache, or expiring workflow artifacts to retain history. Clear generated output before assembling the complete site so deleted/stale routes do not leak into navigation or search. Fetch the Git history/objects required by the catalog; missing snapshots must fail clearly rather than fall back to current source. Read historical Markdown as data; never execute scripts or install dependencies from an old revision just to render its pages.

## Separate code identity, documentation and publication availability

Each catalog entry identifies:
- Module and release version, including the project's channel/prerelease rules.
- Immutable release source commit and its canonical tag where applicable.
- Immutable documentation revision or content identity, with any reviewed correction recorded separately from code identity.
- Confirmed destination-specific coordinates, consumer version strings, repository declarations and compatible dependency pins.
- Translation provenance and availability sufficient to reproduce the version's pages.
- Relevant example/media provenance without claiming a historical demo verifies current behavior.

Keep code tags immutable. Read exact-version installation data from that catalog entry, not current build properties or the latest IMPORT.md. An old page must not silently install the newest package. A later publication to another destination may add a confirmed installation option for the same module/version/source without changing its API guide or allocating another version. Reject conflicting identities. Failed, reserved or partly published attempts do not appear as available releases.

When authorized to implement finalization, archive existing confirmed records before replacing latest pointers and save the new confirmation durably with the corresponding bookkeeping. Preserve older entries, other modules, other destinations and concurrent changes. Distinguish the archive schema from destination-pointer/journal schemas so existing recursive discovery does not mistake archive entries for release attempts. Preserve idempotent recovery and prevent an older recovery from overwriting latest documentation.

Seed history only from verified evidence. State the initial archive boundary; a tag alone is not evidence of successful publication. Do not invent historical snapshots or suggest that today's documentation existed at an older release. If old guides are unavailable, explicitly show that limitation or record a reviewed correction against that release.

For corrections, retain the original code SHA and separately record the corrected documentation identity, checked against the old API. Ensure every pinned identity remains reachable after the repository's actual merge strategy. A SHA from an unmerged PR can be lost after squash merging. Prefer an already retained main-history revision; a documented Git content-tree/blob identity or equivalent durable snapshot is also valid when retained and verified. Do not create self-referential commit hashes. Translation corrections may have their own immutable content identity and canonical-source hash checks. Expose code, documentation and translation provenance without confusing them.

## Module, version and language navigation

Provide accessible module, version and language controls when those dimensions exist. A single-module project needs no artificial module selector. Resolve latest independently per module; do not imply that independently released libraries share one version number. Keep development pages visibly separate from confirmed releases in HTML, raw Markdown and search results.

A suitable route pattern is /<language>/<module>/<version>/<guide>/, with equivalent version-scoped raw Markdown. Preserve existing URLs or provide valid redirects. Keep dots and other legal version characters intact: configure explicit slugs if the framework normalizes them. Retain stable heading anchors or correctly map translated anchors.

Version/language changes should retain the selected guide where available. Otherwise show the selected scope's index with an understandable fallback, never a different version's page disguised as the requested one. A module switch should choose an appropriate documented version policy, typically that module's latest confirmed release. Provide a catalog usable without JavaScript. Localize navigation, selectors, labels, fallback notices and catalogs, not only page bodies. Language metadata, canonical/alternate links and search scope or result labels must match real generated routes. A Spanish catalog must not silently send readers to English development guides.

Keep links between guides inside the selected module/version/language unless a dependency or cross-module transition is intentional and labeled. Source, full-example and historical-media links must use the appropriate immutable revision. Do not rewrite raw-agent links into HTML routes. Expose a small version-aware agent index and discovery catalog so agents can choose a release without scraping selectors.

## Translations with enforceable freshness

Use one canonical language and support the requested locales; English plus Spanish is the default for a new multilingual site. Translate the actual consumer guides and API explanations, including ownership, cancellation, errors, limitations, migration and prerequisite descriptions. Do not create empty translated shells or call the work complete after translating only the homepage. Report any untranslated scope.

Preserve API identifiers, package names, coordinates, version strings and executable code. Share/extract code from the same checked source where practical. Translated surrounding prose must not change the meaning of resource ownership, threading, callbacks, security or compatibility guarantees. If code comments are localized deliberately, keep behavior/identifiers unchanged and validate that distinction; otherwise preserve complete code fences byte-for-byte.

Associate each translated page with a canonical-source hash or immutable revision. Updating that marker requires reviewing the translation, not automatically copying a new hash. A missing or stale translation should show an explicit notice and the canonical-language page from the same selected documentation revision. Never substitute the latest translated API for an old version. Preserve published translations across later source edits; do not make an older translation disappear merely because the editable current translation moved forward.

Capture locale-specific UI screenshots when they are needed to explain translated UI. Otherwise reuse accurately labeled original-language media with localized captions/alt text; do not imply the pictured UI was translated. Preserve screenshot evidence and capture configuration.

## Dependency-aware examples

Check that the installation path actually supplies every artifact imported by the quickstart. A detector-only artifact cannot satisfy a sample importing an optional model bundle. Give explicit module/dependency guidance and verify compatible combinations where possible; do not equate matching version numbers with compatibility.

Show bundles' exact dependency pins and distinguish their API from a separately selected newer dependency. If two JitPack module tags share the same group/artifact, explain the resulting version conflict instead of telling readers to install both. Do not copy this limitation into projects with distinct artifact coordinates. Label source-tree demonstrations and unverified compatibility honestly.

## Release and deployment integration

Extend the existing confirmed-release documentation flow when authorized; otherwise audit it and identify concrete gaps. Generate latest IMPORT.md and exact-version installation views from the same confirmed facts. Rebuild documentation only after successful finalization, including successful JitPack confirmation where supported. Do not presume a bot's commit triggers a push workflow. On GitHub, a trusted successful top-level publication/recovery workflow completion is one possible explicit trigger; validate repository/branch/conclusion and protect deployment permissions.

Build from the latest confirmed catalog while rendering each entry's recorded source. Guard against stale deployments. Keep a manual documentation-only retry which cannot publish packages, and preserve any existing release locks, environment delays and recovery rules. New locales or documentation corrections must not require republishing immutable library artifacts. Changing these prompts does not itself authorize a package release, tag movement or production deployment.

## Validation and delivery

Use focused tests and actual generated output to verify:
- Older records survive latest-pointer replacement; destination catch-up augments only the matching identity; retries are idempotent and pending publications stay absent.
- Semantic version ordering, independent module streams, exact consumer coordinates, dependency repositories and pins.
- Old guide content remains pinned after current guides change; missing objects fail; recorded identities survive the intended merge strategy.
- Missing/stale translations fall back visibly to the correct canonical snapshot; fresh translations preserve code and contract meaning.
- Every advertised module/version/language choice resolves, including dropdown option values, catalog/sidebar links, deep links, language alternatives and raw Markdown anchors.
- Clean site assembly retains all cataloged versions without leftovers from prior builds; project-subpath and dotted-version routes work.
- Quickstart dependencies match its imports; optional bundles and same-artifact conflicts are explicit.
- Localized navigation and representative rendered pages work on keyboard, desktop and narrow screens when a browser is available. Do not claim visual verification from HTML checks alone.

Document commands for authoring, translation review, history seeding, exact-version generation, corrections, verification and independent deployment retry. Report the actual release/locale coverage, archive boundary, fallback behavior, tests run, blocked checks and PR/commit links. Preserve existing hosting and contributor instructions. Do not claim historical compatibility, publication availability or translations that were not verified.
