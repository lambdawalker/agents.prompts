Act as a senior Python packaging and release engineer. Implement or improve this repository's GitHub Actions automation for versioned Python package releases to PyPI. Deliver working repository changes, validation, and a maintainer runbook.

INPUTS

- Target repository: [REPOSITORY_URL]
- Release branch: [discover the default branch; usually main]
- Distribution names and package roots: [discover, or specify]
- Import names and command-line entry points: [discover]
- Release model: [Release Please release PRs by default; preserve an established equivalent]
- Version policy: [discover existing policy; specify initial version if no reliable history exists]
- GitHub environment: [pypi]
- Optional staging registry: [TestPyPI only if requested]
- Delivery: [local changes / pull request / commit to target branch]
- Actual package publication: [only when explicitly requested]

REFERENCE AND PROVENANCE

This prompt was extracted from the release automation in:
https://github.com/lambdawalker/ds.python.usnames

Inspect its current .github/workflows/publish.yml, .github/workflows/test.yml, release-please-config.json, .release-please-manifest.json, pyproject.toml, MANIFEST.in, tests/test_release.py, and docs/publishing.md when available. The reference was reviewed at commit f63cac33e4f9686930084f4eac00195acea65e48.

The related https://github.com/lambdawalker/ds.source.usnames repository publishes dataset assets to GitHub Releases through .github/workflows/release-dataset.yml; it does not implement the PyPI upload. Keep dataset releases and Python distribution releases independent when a target project has both.

Extract the approach, not project-specific names, imports, bootstrap SHAs, versions, license choices, or dataset behavior. This prompt is self-contained; inability to access the reference must not require inventing its contents.

Before choosing action versions or configuration options, check current official documentation:
- https://github.com/googleapis/release-please-action
- https://github.com/googleapis/release-please
- https://docs.pypi.org/trusted-publishers/using-a-publisher/
- https://docs.pypi.org/trusted-publishers/creating-a-project-through-oidc/
- https://github.com/pypa/gh-action-pypi-publish
- https://packaging.python.org/en/latest/tutorials/packaging-projects/
- https://docs.github.com/en/actions/how-tos/write-workflows/choose-when-workflows-run/trigger-a-workflow

REQUIREMENTS

1. INSPECT THE REPOSITORY BEFORE EDITING

Read applicable AGENTS.md files, packaging configuration, package layout, test commands, lockfiles, existing workflows, release history, tags, and maintainer documentation. Identify distribution names separately from Python import names. Determine which roots actually produce installable distributions; do not turn every Python module into an independent PyPI package.

Preserve the build backend, supported Python versions, entry points, and established dependency tooling. Use uv when the repository already uses it, with frozen/locked CI installs where appropriate; otherwise retain its supported tooling. Do not migrate backends or dependency managers just to add publishing.

Check existing PyPI project/version ownership and availability when accessible. A failed network request is not proof that a project or version does not exist. Report uncertainty. Do not invent a license, claim ownership of a distribution name, or reset established release history.

2. AUTOMATE RELEASE PREPARATION WITH REVIEWABLE VERSION CHANGES

By default, use Release Please to maintain a release PR from Conventional Commits. Feature/fix merges should prepare or update that PR; uploading follows the merged release PR's release creation. Do not enable automatic merging unless separately requested.

Configure the Python release strategy, changelog, release manifest, package name, tag convention, and pre-1.0 behavior explicitly. Explain fix:, feat:, and breaking-change behavior according to the actual configuration rather than assuming pre-1.0 releases follow the same bumps as stable releases.

Keep the authoritative package version, any maintained runtime version, the local project's lockfile entry, and the tag consistent. Use supported extra-file updaters or the existing dynamic-version mechanism; do not introduce redundant version constants. Do not modify unrelated locked dependencies while updating the project's version.

Seed bootstrap SHAs, manifests, and first-release versions from this repository's history. If no initial-version policy can be established, ask for that value while completing the other implementation work. Never copy the reference's bootstrap commit or initial version.

For multiple distributions, use a deliberate shared or independent version policy and package-specific outputs/artifacts. Account for internal dependency update order. Avoid ambiguous root-level outputs when Release Please emits path-prefixed outputs.

3. MAKE EVENT ROUTING AND SOURCE IDENTITY EXPLICIT

Support the following behavior, adapting existing workflow names:

| Event | Expected behavior |
| --- | --- |
| Pull request | Test, build, validate distributions; never publish or grant OIDC credentials |
| Push to release branch | Reconcile release PR; build/test the candidate; publish only if a release was created |
| Manual dispatch | Explicitly documented reconciliation or recovery mode; never label a publish-capable action as a dry run |
| Published GitHub release, if retained | Validate the release/tag policy and build the exact released commit for recovery/manual publication |

Events generated using GITHUB_TOKEN generally do not trigger downstream workflows. Connect Release Please's release-created, tag, and SHA outputs directly to dependent build/publish jobs in the same workflow rather than relying on a second release-event run.

Bot-created release PRs also need validation. Validate the generated candidate branch with read-only permissions, or use an established GitHub App integration that triggers ordinary PR checks. Explain whether the resulting checks attach to the PR and satisfy branch protection. Do not claim a workflow check on the base commit validates the candidate PR.

Resolve release tags to immutable commit SHAs. A publishing build must check out that exact source, never the current default-branch HEAD. Make skipped release-preparation jobs compatible with PR builds; use explicit dependency-result and cancellation checks so failed builds cannot publish.

Restrict release management and publishing to the canonical repository and intended refs/events. Validate manual recovery tags and their provenance. Define prerelease handling. Never execute untrusted PR code through pull_request_target with write credentials.

Use read-only workflow defaults. Give repository/issue/PR writes only to release management as needed. Give id-token: write only to the isolated publishing job. Disable persisted checkout credentials where not needed. Pass untrusted inputs through quoted environment variables rather than interpolating them into shell code.

Serialize actual publication per distribution across automated, manual, and recovery entry points, with cancel-in-progress: false. A concurrency group containing event names must not allow two different event paths to upload the same package simultaneously. Apply appropriate job timeouts.

4. BUILD AND VALIDATE BEFORE UPLOAD

Start with a clean distribution output directory. Run the existing meaningful tests on supported Python versions, including the minimum supported version where practical. Produce a wheel and source distribution through the project's PEP 517 backend, using python -m build or the repository's equivalent.

Before publishing:
- Validate tag/version consistency, including PEP 440 normalization where applicable.
- Check built wheel and sdist metadata, not only the source version file.
- Run python -m twine check --strict on the exact artifacts.
- Inspect contents for missing modules/resources and unintended secrets, caches, datasets, or local files.
- Install the wheel in a fresh virtual environment and smoke-test imports and existing CLI entry points from outside the checkout, with isolated Python execution where appropriate.
- Ensure the sdist can build a usable wheel without relying on files omitted from that archive.
- Keep large externally downloaded assets separate unless packaging them is an explicit project requirement.

For native extensions, use a suitable platform/interpreter wheel matrix and validate each supported artifact rather than pretending a pure-Python wheel covers every platform.

Upload validated distributions as a named workflow artifact and fail if missing. The publishing job must download the matching artifact from that run; it must not rebuild from a different source. Use unique artifact names per distribution/matrix component and preserve source SHA, filenames, versions, and hashes for recovery.

5. PUBLISH WITH PYPI TRUSTED PUBLISHING

Use PyPI Trusted Publishing with GitHub OIDC and the official PyPA publishing action. Use a dedicated GitHub environment, normally pypi, with the actual PyPI project URL. Do not add a long-lived PyPI token as the default authentication method.

Keep building/testing and publishing in separate jobs. Publish only after validation succeeds and the selected event authorizes a release. Do not run unrelated project scripts in the credential-bearing publishing job.

If TestPyPI is requested, configure its separate publisher/environment and endpoint. Clearly distinguish a TestPyPI rehearsal from production availability. Avoid unsafe mixed-index dependency installation in smoke tests.

Select compatible maintained action versions after consulting official sources. Follow existing action-pinning policy; prefer immutable commit pins with readable version comments where practical. Do not invent action SHAs.

6. PROVIDE RECOVERY WITHOUT CHANGING RELEASE IDENTITY

Document that GitHub release creation and PyPI availability are separate facts. If building or uploading fails after release creation, retain the source/tag identity and support retrying the relevant failed jobs from that run. A new Release Please run may report that no release was created and therefore may not republish it.

Do not overwrite or move existing release tags, delete public distributions, or reuse an existing version for changed code. Do not use blanket skip-existing behavior as evidence of success.

For partial uploads, compare existing PyPI filenames and hashes against retained validated artifacts. Either safely upload only confirmed missing artifacts through a documented recovery path, or stop with a precise reconciliation instruction. If artifacts have expired, do not assume a rebuilt archive is byte-identical; verify against durable hashes and registry metadata before proceeding.

After upload, use bounded retries to verify expected files/version through PyPI and perform a clean exact-version installation where feasible. Report propagation delays or incomplete verification honestly. Only mark a version as available in README/IMPORT.md or release records after confirmation. Do not introduce a new documentation subsystem solely for this workflow.

7. DOCUMENT THE EXACT OWNER SETUP

Create or update docs/publishing.md and link it from the README. Include:

- The actual distribution name, imports, package roots, and install commands.
- The everyday Conventional Commit -> release PR -> maintainer merge -> GitHub release -> validated artifacts -> PyPI flow.
- First-release/bootstrap behavior and the chosen prerelease/pre-1.0 policy.
- A table of exact Trusted Publisher values: PyPI project, GitHub owner, repository name, workflow filename, and GitHub environment.
- New-project pending publisher versus existing-project publisher setup.
- Required current PyPI account prerequisites and relevant setup links.
- GitHub Actions permission to create release PRs, release-manager job permissions, and any branch-protection interaction.
- Environment setup and any owner-chosen reviewers/ref restrictions; automated runs may use the branch ref even when checking out tagged source, while release-event runs use tag refs.
- Which settings are implemented in repository files and which account settings remain for the owner.
- Local validation commands, safe rehearsal behavior, manual dispatch semantics, partial-upload recovery, and troubleshooting for publisher mismatch, tag/version mismatch, missing PR checks, and duplicate filenames.

Do not claim remote settings have been configured unless verified. Never request secrets in chat or print them in logs.

8. VERIFY AND DELIVER

Run the relevant tests, clean build, metadata validation, and installed-artifact checks. Validate workflow syntax with an appropriate Actions-aware tool when available. Add focused tests for custom version/tag or release-routing logic if introduced.

Review event/job conditions against the table above, including skipped jobs, failed builds, forks, bot-created PRs, duplicate release events, and recovery. Do not publish a real package merely to test an implementation request.

Deliver the workflow/configuration changes, any necessary packaging fixes, release documentation, and validation evidence. Summarize changed files, checks actually run, remaining owner setup, and the concrete next step for a first release. State any unverified CI/account behavior explicitly. Follow the requested delivery mode and preserve unrelated work.
