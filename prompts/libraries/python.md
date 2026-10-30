# Python libraries

Use this section with the selected primary documentation workflow and [shared library rules](shared.md). Use the [Python package publishing supplement](../python-package-publishing.md) only for the requested publishing scope.

## Standalone or part of a larger project

- Standalone: document supported Python versions, installation, package/import names, optional extras, complete examples, API contracts, tests, and release/migration guidance locally.
- Within a larger project: retain package API/version ownership and link to the host's architecture, configuration, pipelines, services, and lifecycle responsibilities. Distinguish editable/workspace installs from confirmed released distributions. Do not require splitting a monorepo package into a new repository.

## Contracts, dependencies, and examples

Use the project's established environment/dependency manager and lock/build configuration. Document sync versus async entry points, event-loop/thread/process requirements, cancellation/timeouts, retry behavior, context managers, resource cleanup, errors, typing, and data/model schemas where relevant. Explain credentials and optional external services without exposing secrets. Prefer self-contained executable examples and deterministic tests; label network, GPU, model, or cloud prerequisites explicitly.

If APIs/MCP servers, CLIs, notebooks, web demos, or model caches are actual capabilities, document their startup, configuration, boundaries, and troubleshooting. Do not imply every library needs a server, UI, publishing workflow, or GPU. Preserve established release automation; wheel/sdist validation and registry publication are separate from documentation deployment.

## Documentation and visuals

Use screenshots of existing UI/demo applications, rendered notebooks, or meaningful CLI output when these clarify a workflow. Record source/scenario provenance, inspect images, and avoid private data. Pure backend packages do not need fabricated interfaces or screenshot tests. Publish the latest confirmed stable release as the default documentation view, using Python version semantics and the project's prerelease/yanked-release policy. Keep module/version/language navigation understandable while preserving historical APIs, exact install facts, and shared parent-project links.
