You are acting as a senior software architect, developer advocate, API designer, and AI-agent documentation specialist.

Your task is to analyze this repository and create or improve the documentation needed for an AI coding agent to correctly understand, use, integrate, modify, and troubleshoot the project.

The documentation is not intended only for humans. Treat it as a machine-consumable interface contract between this repository and AI coding agents.

The goal is that an AI agent unfamiliar with the project can determine:

- What the project is.
- What problem it solves.
- When it should be used.
- When it should not be used.
- How to install or integrate it.
- What its public APIs are.
- Which API should be chosen for a particular situation.
- What assumptions the project makes.
- What invariants must be preserved.
- What limitations exist.
- What lifecycle, ownership, threading, security, platform, performance, or resource-management rules exist.
- What common mistakes must be avoided.
- How errors and edge cases should be handled.
- How to verify that an implementation using the project is correct.
- How to modify the project safely if the agent is working on the repository itself.

Do not merely summarize the existing README.

First inspect the actual implementation, tests, examples, build files, CI configuration, release configuration, and existing documentation. Documentation must describe the current implementation rather than assumptions about how the project probably works.

Prefer facts derived from the source code over existing prose when they disagree.

DOCUMENTATION ARCHITECTURE

Use the following conceptual separation.

1. AGENTS.md

AGENTS.md is primarily for AI agents that are modifying or maintaining this repository.

It should answer:

- What is this repository?
- What are the important modules and directories?
- Which files contain public APIs?
- Which parts are implementation details?
- How is the project built?
- How are tests run?
- How are linting, formatting, static analysis, and validation run?
- What commands should be executed before submitting changes?
- What architectural rules must not be violated?
- What public API compatibility rules exist?
- Which generated files should not be manually edited?
- Where are the authoritative installation reference, its editable template, and regeneration instructions?
- How are released versions distinguished from development versions?
- What documentation must be updated when behavior changes?
- Are there repository-specific conventions or gotchas?
- Where should an agent look for deeper information?

Keep AGENTS.md focused on working ON the repository.

Do not turn AGENTS.md into the complete usage manual for consumers of the project.

If the repository contains independently maintained modules with substantially different rules, consider nested AGENTS.md files. Do not create them unnecessarily.

2. docs/agents/

Create a dedicated documentation area for agents trying to USE the project.

Recommended structure:

docs/agents/index.md\
docs/agents/quickstart.md\
docs/agents/concepts.md\
docs/agents/api.md\
docs/agents/recipes.md\
docs/agents/limitations.md\
docs/agents/troubleshooting.md\
docs/agents/migration.md

Adjust this structure when appropriate for the project. Do not create empty or meaningless documents simply to satisfy the suggested layout.

3. docs/agents/index.md

This is the canonical starting point for an AI agent trying to consume the project.

It should answer, preferably in this order:

1. What is this project?
2. What problem does it solve?
3. When should it be used?
4. When should it not be used?
5. What is the smallest correct integration?
6. What concepts must be understood?
7. Which API should be selected for each common situation?
8. What important limitations or invariants exist?
9. What commonly goes wrong?
10. Where can deeper information be found?

Optimize this page for fast retrieval.

An agent should be able to read this page and make a reasonable first implementation without reading the entire repository.

4. quickstart.md

Provide the smallest complete, correct, idiomatic integration. Link to IMPORT.md (or the established authoritative installation reference) for dependency declarations and released versions instead of copying them into quickstart. Keep the usage example here.

Examples must contain enough context to actually understand how they are used.

Avoid using pseudo-code when a compilable or executable example can reasonably be provided.

The first example should demonstrate the normal path, not every customization supported by the project.

Move advanced behavior to recipes.

5. concepts.md

Explain the conceptual model behind the project.

Document terminology precisely.

Explain relationships between important abstractions.

Explain state machines, ownership models, lifecycle behavior, data flow, architecture boundaries, or execution models when relevant.

An AI agent should understand WHY the APIs are shaped the way they are after reading this document.

6. api.md

Document the public API surface that an external consumer should use.

Do not simply copy function signatures.

For important APIs document:

- Purpose.
- When to use it.
- When not to use it.
- Inputs.
- Outputs.
- Defaults.
- Preconditions.
- Postconditions.
- Side effects.
- Lifecycle behavior.
- Threading behavior.
- Ownership rules.
- Resource-management behavior.
- Error behavior.
- Cancellation behavior.
- Platform restrictions.
- Version restrictions.
- Security considerations.
- Performance considerations.
- Common mistakes.
- Related APIs.
- Minimal example.

Explicitly distinguish public API from internal implementation.

If a symbol should not normally be used by consumers, say so.

7. recipes.md

Document task-oriented examples.

Prefer descriptions such as:

"Use X when you need to..."

instead of descriptions such as:

"X is a configurable abstraction that..."

Include realistic recipes covering the most important workflows.

Each recipe should state:

- The situation.
- The recommended API.
- Why that API is appropriate.
- Complete example code.
- Important caveats.
- Expected behavior.

Where useful, create separate recipe files and link them from recipes.md.

8. limitations.md

Document things the project intentionally does not support.

Also document:

- Platform limitations.
- Runtime limitations.
- Known ambiguity in underlying APIs.
- Unsupported combinations.
- Approximate or heuristic behavior.
- Scalability limits.
- Performance constraints.
- Security boundaries.
- Cases where a caller must implement additional logic.
- Behavior that depends on external systems.

Use precise wording.

Do not hide limitations behind marketing language.

If something cannot be known with certainty, say so explicitly.

9. troubleshooting.md

Document failures based on actual project behavior.

For each common problem, describe:

- Symptom.
- Likely cause.
- How to diagnose it.
- Correct fix.
- Incorrect fixes or workarounds that should be avoided.

Include relevant build, runtime, platform, dependency, configuration, and integration problems.

10. migration.md

Document removed, deprecated, replaced, or behaviorally changed APIs.

For every important migration include:

- Old API or behavior.
- New API or behavior.
- Version where the change occurred.
- Why it changed when useful.
- Replacement example.
- Any behavioral differences.

Use explicit directives such as:

"Do not generate new code using X."

This is important because AI systems may retrieve obsolete examples.

DOCUMENTATION STYLE FOR AI AGENTS

Write documentation as explicit rules rather than vague descriptions.

Weak:

"Foo provides a flexible mechanism for authentication."

Strong:

"Use Foo when the caller needs interactive authentication.

Do not use Foo for background service authentication.

Foo requires an Activity-scoped host.

Calling Foo from a background-only component is unsupported."

Whenever possible express behavior using statements like:

- Use X when...
- Do not use X when...
- X guarantees...
- X does not guarantee...
- The caller owns...
- The library owns...
- This callback executes on...
- This object remains valid until...
- After X is called...
- Before calling X...
- If Y occurs...
- Never...
- Prefer...
- Avoid...

Reduce interpretation wherever possible.

DECISION TABLES

Create decision tables when several APIs solve related problems.

For example:

Situation | Recommended API

The exact table depends on the repository.

The goal is that an AI agent can mechanically map a requirement to the intended API.

If several approaches are technically possible but only one is preferred, state which one is preferred and why.

NEGATIVE DOCUMENTATION

Include incorrect patterns.

Agents benefit greatly from explicit examples of what not to do.

Create sections such as:

Incorrect patterns\
Common mistakes\
Unsupported usage\
Do not do this

Show examples that may compile but are logically incorrect when appropriate.

Explain why they are wrong and show the correct alternative.

Pay special attention to:

- Lifecycle mistakes.
- Threading mistakes.
- Resource leaks.
- Ownership mistakes.
- Repeated initialization.
- Incorrect cleanup.
- Race conditions.
- Unsafe retries.
- Invalid state transitions.
- Deprecated APIs.
- Security mistakes.
- Performance traps.
- Incorrect platform assumptions.

SEMANTIC DOCUMENTATION

Do not document parameters by merely repeating their names.

Weak:

enabled:\
Whether the feature is enabled.

Strong:

enabled:\
Controls whether new work is processed.

When false:

- Input may continue to arrive.
- New work is not started.
- Existing work is not necessarily cancelled.
- Callbacks are not emitted for skipped work.

Changing this value does not recreate the processor.

Document observable consequences.

SOURCE OF TRUTH

Determine where each type of information belongs.

Prefer this model:

Source code and API comments:\
Exact symbol behavior and local contracts.

docs/agents:\
Usage decisions, workflows, architecture concepts, limitations, and recipes.

AGENTS.md:\
Instructions for agents modifying the repository.

Examples or demo modules:\
Known-good executable integrations.

Tests:\
Behavioral guarantees and edge cases.

Do not duplicate large sections of documentation unnecessarily.

Instead link to the canonical source.

If this repository belongs to a multi-repository system, do not duplicate system-level architecture here if another repository is the designated architecture source.

In that case:

- Keep system architecture in the architecture/design repository.
- Keep implementation details in this repository.
- Link to the architectural source for cross-service flows, system interactions, protocol design, and architectural rationale.
- Keep build instructions, deployment details, module-specific behavior, implementation gotchas, local API documentation, troubleshooting, and module-specific limitations here.

EXAMPLES MUST STAY CORRECT

Whenever possible, documentation examples should originate from code that is compiled or tested by CI.

Prefer:

demo source\
test fixtures\
sample modules\
extracted source snippets

over independently maintained copies of code embedded in Markdown.

Avoid documentation drift.

If practical, configure CI so important documented examples are compiled or tested.

VERSION AWARENESS

Clearly identify documentation version when relevant.

Document:

- Current published version, obtained from the authoritative installation reference rather than repeated in independently maintained pages.
- Minimum supported platform/runtime.
- Important dependency requirements.
- Breaking changes.
- Deprecated APIs.
- Removed APIs.
- Replacement APIs.

When old code must no longer be generated, explicitly say:

"Do not generate new code using X."

Do not assume an AI agent knows which online examples are obsolete.

PUBLISHING AND AUTHORITATIVE INSTALLATION GUIDANCE

For a project that publishes packages, use one canonical installation/version reference, preferably IMPORT.md at the repository root. Reuse an established equivalent where appropriate. For projects without published artifacts, document the actual source-based setup; do not invent releases or add irrelevant publishing infrastructure.

IMPORT.md should describe the latest successfully published release, actual artifact coordinates, applicable package-manager installation forms, and multi-artifact/version distinctions. It should be generated and committed from an editable template, preferably docs/templates/IMPORT.md.template, using the build's publishing metadata and an explicit confirmed release version. Mark it as generated and identify the template and regeneration command.

For Gradle/Maven, prefer a deterministic generateImportDocs task accepting -PreleaseVersion and drawing coordinates from existing publishing configuration. Include applicable Kotlin DSL, Groovy, version-catalog, and Maven examples in this reference. Adapt the mechanism to other ecosystems; do not duplicate coordinates in unrelated constants or embed large templates in CI YAML.

Add an explicit instruction in existing contributor and consumer-agent entry points:
“Read IMPORT.md before answering questions about installation, dependency coordinates, or the current released version. Use the values documented there. Do not guess versions, use workflow run numbers, or treat development configuration as evidence of publication. Edit the template and regenerate; do not manually edit the generated reference.”

If the reference is missing, stale, or conflicts with registry/release evidence, report the discrepancy and verify the release before changing it. Do not fabricate a latest version. Unreleased source behavior must be labeled separately from behavior supported by the documented published release.

Link README, quickstart, recipes, website pages, and llms.txt to that reference. Avoid independently maintained versioned dependency snippets. Keep intentional migration history, compatibility ranges, lockfiles, and samples using local project dependencies; these serve different purposes.

Inspect and document the existing release mechanism:
- Prefer semantic release tags or a verified release ledger as persistent version history. Sort versions semantically, distinguish prereleases and independent module streams, and handle the initial release explicitly.
- Pass the selected version explicitly through one mechanism to all relevant publications and the documentation renderer. Do not use github.run_number as the library version or add competing version sources.
- Update IMPORT.md only after the intended publication is confirmed successful. Upload/staging success alone may be insufficient. Failed or partial releases must not advance the documented latest release.
- Preserve artifact provenance: release tags should identify the source commit used to build the artifact; a later generated-documentation commit can remain separate.
- Prevent concurrent version allocation, duplicate immutable publication, and release loops caused by bot documentation commits.
- Define recovery when publication succeeds but rendering, tagging, or Git push fails. Recover the same published version and source commit; do not blindly republish or allocate another release.
- Use minimal workflow permissions, preserve concurrent changes, and create a bot commit only when generated content changes.

For an Android/Maven Central project using delayed release confirmation, document each stage separately: upload; a secret-free delayed-docs environment with a 15-minute wait timer; and a shared automatic/manual finalizer that polls public artifacts for up to 40 minutes. State that the environment gate consumes no runner while waiting, but plugin waiting and finalizer polling do. Document the owner-configured timer, existing release-branch restrictions, and required publishing secrets. The manual action takes a reserved version, skips the delay, and never builds or uploads. Explain the shared job-level mutation lock, why the delay is outside it, remote-state revalidation, and idempotent completion. An older finalized version must not overwrite newer installation docs. Retain pending markers on timeouts or partial artifacts and link to the recovery runbook. When implementation is authorized, apply the companion Android library publishing prompt; otherwise report gaps without claiming the mechanism exists.

Document template ownership, generation and verification commands, release success criteria, and failure recovery in contributor/build guidance. Do not claim automation exists unless it is implemented and verified. If release tooling changes are outside the requested scope, document the gap and recommendation. When authorized, adapt the existing workflow and build tooling rather than introducing a competing release path. Do not perform an actual publication solely to validate documentation.

Validate deterministic rendering, correct coordinates/version for each artifact, absence of unresolved placeholders, and links to the reference. A verifyImportDocs task or equivalent CI check should detect drift using the last confirmed release metadata, not the next development version. Preserve published coordinates if current build configuration has changed. Test failure/rerun paths with fixtures or mocks when changing automation; a failed publish must leave the latest-release reference unchanged.

LLMS.TXT

If the project publishes a documentation website, create or update llms.txt when appropriate.

Keep llms.txt concise.

It should help an AI system discover the project's canonical documentation.

It should contain:

- Project name.
- Short description.
- What the project is for.
- Link to the agent entry page.
- Link to quickstart.
- Link to the authoritative installation and published-version reference.
- Link to public API documentation.
- Link to concepts.
- Link to recipes.
- Link to limitations.
- Link to migration guidance.
- Links to other highly important machine-readable resources.

Do not duplicate the complete documentation inside llms.txt.

Treat it as a routing and discovery document.

If Markdown versions of documentation pages can be published, prefer exposing them.

PUBLIC API INVENTORY

Identify every public API intended for consumers.

Produce a clear inventory grouped logically by purpose.

For every important public class, interface, function, annotation, configuration object, callback, enum, builder, command, endpoint, schema, or extension point, determine whether documentation exists.

Do not overwhelm the agent with every internal symbol.

Document what a consumer should actually use.

If there are APIs that are public for technical reasons but are effectively internal, document that distinction.

INVARIANTS

Create an explicit section for invariants when the project has non-obvious rules.

Examples:

- A method must only be called once.
- A callback always executes on a specific thread.
- An object must be closed.
- A resource is transferred to the callback and must not be reused.
- A transaction is immutable after commit.
- A coroutine must be cancelled with its owner.
- A state transition cannot move backwards.
- Configuration must be completed before initialization.
- A given operation is idempotent.
- A particular ordering must be preserved.

Agents should not have to infer critical invariants from implementation details.

OWNERSHIP

Explicitly document ownership whenever resources are involved.

For example:

- Who creates the resource?
- Who owns it?
- Who releases it?
- Can it be retained after a callback?
- Can it be mutated?
- Can it be reused?
- Can it be shared between threads?

This applies to memory buffers, files, streams, handles, connections, images, contexts, views, database sessions, transactions, network clients, coroutines, executors, and similar resources.

CONCURRENCY AND THREADING

If applicable document:

- Which thread APIs may be called from.
- Which thread callbacks run on.
- Whether APIs are thread-safe.
- Whether operations are synchronous or asynchronous.
- Cancellation semantics.
- Backpressure behavior.
- Whether callbacks may overlap.
- Ordering guarantees.
- Reentrancy behavior.
- Executor or dispatcher ownership.
- Whether slow consumers affect producers.

Do not leave these behaviors implicit when they matter.

LIFECYCLE

If applicable document:

- Initialization point.
- Valid lifetime.
- Cleanup mechanism.
- Behavior during pause/resume.
- Behavior after disposal.
- Behavior during configuration changes.
- Whether callbacks may arrive after release.
- Whether work survives process, request, component, or application lifecycle boundaries.

SECURITY

Document security boundaries and assumptions where relevant.

Explain:

- What data is trusted.
- What data is untrusted.
- What validation is performed.
- What validation callers must still perform.
- Secrets or credentials requirements.
- Persistence guarantees.
- Cryptographic assumptions.
- Authentication or authorization requirements.
- Network trust assumptions.
- Known attack surfaces.
- Sensitive logging restrictions.

Do not imply stronger security guarantees than the implementation actually provides.

PERFORMANCE

Document behavior that may materially affect application performance.

Examples:

- Expensive initialization.
- Caching.
- Recommended object reuse.
- Memory ownership.
- Batch behavior.
- Frame dropping.
- Rate limiting.
- Network calls.
- Database behavior.
- GPU or accelerator usage.
- Blocking operations.
- Work that must not occur on a UI thread.

Avoid speculative optimization advice. Base recommendations on implementation behavior.

VALIDATION

Document how a consumer can determine that an integration is correct.

Examples:

- Expected callbacks.
- Expected state transitions.
- Health checks.
- Test APIs.
- Debug logs.
- Sample output.
- Integration tests.
- CLI validation commands.
- Build commands.

An agent should not stop at "the code compiles" when additional verification is necessary.

AGENT EVALUATION

Create an agent-evaluation plan for the documentation.

Design approximately 10 to 20 realistic tasks an AI coding agent should be able to complete using only the published documentation.

Examples include:

- Install the project using the authoritative released-version reference without guessing coordinates or versions.
- Implement the simplest supported use case.
- Use several related APIs correctly.
- Handle a failure condition.
- Configure an advanced feature.
- Avoid a known invalid pattern.
- Explain a limitation correctly.
- Migrate from an old API.
- Correctly manage lifecycle or cleanup.
- Correctly select between similar APIs.

For each evaluation define:

- Prompt.
- Expected API choice.
- Important behaviors expected.
- Invalid behaviors that should cause failure.
- Whether generated code can be compiled or tested automatically.

Where practical, implement automated checks for these evaluations.

Treat documentation failures discovered by these tests as documentation bugs.

README

The normal README remains human-friendly.

It should provide:

- Brief project description.
- Main use case.
- A concise installation link to IMPORT.md or the established canonical reference; keep versioned dependency examples in that reference.
- Minimal usage example.
- Link to complete documentation.
- Link to agent documentation when appropriate.

Do not force every implementation detail into README.md.

DOCUMENTATION QUALITY RULES

Avoid:

- Marketing language where behavioral precision is needed.
- Empty statements such as "easy," "flexible," "powerful," or "robust" without explaining behavior.
- Restating symbol names without explaining semantics.
- Examples using deprecated APIs.
- Pseudo-code when real code is feasible.
- Contradictions between README, examples, docs, and source.
- Duplicating architectural documentation across repositories.
- Large documentation files that mix unrelated concerns.
- Assuming agents will infer lifecycle or ownership rules.
- Assuming agents know platform-specific behavior.
- Relying entirely on source-code inspection for critical usage rules.

Prefer:

- Small focused documents.
- Explicit decisions.
- Concrete examples.
- Tables.
- Invariants.
- Negative examples.
- Links to canonical sources.
- Executable examples.
- Version-specific statements.
- Observable behavior.
- Clear ownership.
- Clear error semantics.

ANALYSIS PROCESS

Before changing documentation:

1. Inspect the repository structure.
2. Identify project type and intended consumers.
3. Identify public APIs.
4. Inspect current README and documentation.
5. Inspect sample/demo applications.
6. Inspect tests.
7. Inspect build and dependency configuration.
8. Inspect CI.
9. Inspect release/publishing configuration, confirmed release history, installation templates, and generated-version references.
10. Identify lifecycle rules.
11. Identify threading/concurrency behavior.
12. Identify resource ownership.
13. Identify errors and failure behavior.
14. Identify security assumptions.
15. Identify platform/version restrictions.
16. Identify deprecated or obsolete APIs.
17. Identify common implementation traps.
18. Identify documentation contradictions.
19. Determine what belongs in this repository versus external architectural documentation.

Then create or update the agent documentation.

Do not invent guarantees that cannot be verified from the implementation.

When behavior cannot be conclusively determined, explicitly mark it as uncertain and explain what evidence is missing.

FINAL DELIVERABLE

At completion, the repository should allow a capable AI coding agent with no prior knowledge of the project to:

1. Understand what the project is for.
2. Decide whether it should be used.
3. Select the intended APIs.
4. Produce a minimal correct integration.
5. Implement common advanced scenarios.
6. Avoid known invalid patterns.
7. Respect lifecycle, threading, ownership, and security rules.
8. Understand limitations.
9. Diagnose common failures.
10. Validate its implementation.
11. Distinguish current APIs from obsolete ones.
12. Safely modify the repository when acting as a contributor.

Prefer improving or consolidating existing documentation over creating redundant files.

The final documentation should be optimized for correctness, retrieval, and unambiguous decision-making rather than prose volume.

Think of the documentation as part of the project's public API.

If an AI agent can misunderstand an important behavior after reading the documentation, the documentation is incomplete.