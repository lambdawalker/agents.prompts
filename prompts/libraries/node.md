# Node.js libraries

Pair this section with the [shared library rules](shared.md) and the selected [standalone](../standalone-library-documentation.md) or [system-integrated](../system-integrated-library-documentation.md) workflow. Use [draw.backgrounds](https://github.com/lambdawalker/draw.backgrounds) as a working reference. Adapt the rules to the package's actual entry points, platforms, and release scope.

## Package and integration

- Keep package names, public exports, supported Node.js versions, and release facts with the owning package. In a larger project, also document workspace installation and link to the host's integration and lifecycle guidance.
- Declare supported entry points in `package.json` and document their module format, runtime compatibility, and import examples. Separate browser-compatible code from Node-only or native features when the package supports both. Keep native dependencies optional when the core genuinely works without them. Verify the core with optional dependencies omitted.
- Keep the package allowlist intentional. Exclude site dependencies, internal tooling, fixtures, and generated review artifacts unless they are part of the consumer contract. Preserve the established package manager and lockfile. Verify distribution license and npm scope ownership before the first publication.
- Organize shared primitives, runtime adapters, and domain-specific implementations by ownership. Preserve supported imports and existing behavior during file moves. For deterministic generators, verify matching outputs before and after a refactor with fixed inputs and seeds.
- Provide executable examples for every supported entry point. Document synchronous and asynchronous behavior, input limits, output ownership, errors, optional dependencies, and platform constraints where relevant.

## Tag-driven npm releases

1. Prepare the package version and lockfile in a reviewed change. Merge it into the release branch, usually `main`. Tag the exact release commit with an immutable stable SemVer tag `vX.Y.Z`. Keep earlier tags intact, including tags that predate the release workflow.
2. Add a GitHub Actions workflow triggered by version-tag pushes. Validate the full tag syntax, ensure it matches the targeted package manifest, and confirm the commit belongs to the intended release branch. For independently versioned workspace packages, scope tags and validation to the intended package.
3. Install locked dependencies, run tests, pack the package, inspect the archive contents, and install that exact tarball in a clean consumer project. Exercise every public entry point under its supported runtime and dependency conditions. Pass that same tarball to the publish job.
4. Use npm Trusted Publishing with OIDC for automated publication. Restrict publishing permissions to the publish job and document npm/GitHub setup, including first-publication bootstrap if required by the registry. Keep release retries on the same tag. Before retrying, query the exact package version and compare `dist.integrity` with the verified tarball. Identical integrity is a successful retry. Conflicting integrity requires reconciliation. Source corrections require a new version and tag.
5. Document exact npm coordinates and Git-tag installs separately in `IMPORT.md` or the established installation guide. Verify supported install commands in a clean consumer. Treat a Git tag as source availability. Confirm npm availability against the registry before documenting a version as published.

## Website, agent guides, and release history

- Build human-facing HTML and raw agent Markdown from the same maintained sources. Provide `llms.txt` or the project's established agent index. Cover installation, quickstart, API contracts, runnable recipes, limitations, troubleshooting, and migration. Keep site-only dependencies out of consumer installs.
- Apply the [versioned and multilingual policy](../versioned-multilingual-documentation.md). Default new sites to English and Spanish, track translation freshness, and fall back to the same version's canonical guide when a translation is missing or stale. Preserve version and language across navigation. Serve the latest confirmed stable release by default. Label Development explicitly and use it when no stable release is confirmed.
- After tag publication, independently verify the successful publication run, exact source commit, and npm tarball integrity. Propose a reviewable release-catalog update containing the tag, source and documentation revisions, package identity, integrity, and publication evidence. Keep historical sources reachable in Git or durable snapshots. Rebuild every retained version from those sources without executing historical scripts or depending on expiring workflow artifacts.
- Build and validate docs on pull requests. Deploy approved changes from the release branch through GitHub Pages Actions or the established host. Verify project subpaths, links, anchors, raw guides, examples, locale fallback, and historical navigation. Keep confirmation and site deployment retryable independently of package publication.

## Rendered output and CI

- For visual libraries, generate the gallery from real public examples. Record inputs, seeds, dimensions, renderer/toolchain, and file hashes where relevant. Inspect candidate output separately. Update reviewed images and their manifests together. Ordinary CI validates reviewed fixtures. A separate manually triggered workflow can generate candidates for review.
- When multiple renderers share a design, test representative outputs and document intentional differences. Use identical inputs and dimensions for performance comparisons. Report rasterization separately from encoding and file writes where those are distinct stages.
- Verify package tests, packed imports, executable examples, and the documentation build before handoff. Document release setup and recovery in the target repository. Apply the shared rules for authorization before publishing or deploying.
