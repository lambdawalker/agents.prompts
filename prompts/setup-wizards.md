# Setup and deployment wizards

Use this prompt to design, implement, or align a setup or deployment wizard
for an Android, iOS, web, or other project. It defines a common workflow for
the terminal and a local browser workspace. Pair it with the target's
repository, signing, publishing, and infrastructure rules. Follow their
tooling, timing, approval, and storage policies where they are more specific.

Read this shared prompt first, then load the interface prompt for the work in
scope. Load both when the project provides both interfaces.

| Project | Execution and platform-specific work |
| --- | --- |
| Android | Apply [Shared Android repository automation](android-repository-automation.md). Reuse the Rust `android-repo-setup` CLI, generated launchers, and existing vault. Keep module and variant discovery, Gradle, SDK/JDK checks, signing, and publishing in their existing tools. Add reusable browser support to the shared tool when that work is in scope. |
| iOS | Reuse the project's Xcode or other existing automation. Model the applicable workspace, scheme, configuration and signing or publishing destination. Run Xcode, signing, archiving and distribution steps only where their required macOS tools, identities and permissions are available. |
| Web | Reuse the project's build and deployment tools. Model the applicable package or site, build output, preview or production target and hostname. If deployment already uses Composer, keep the pinned CLI and the project's supported build hooks, as in [Card Designer](https://github.com/lambdawalker/site.card-designer/blob/main/README.md#aws-deployment). |
| Compatible Go + AWS | Follow the [Go + AWS wizard rules](libraries/go-aws-common.md#deployment-wizard-requirements-for-deployable-go--aws-projects). Reuse the shared Infra Composer CLI, terminal and browser approval flows, and deployment record and vault locations. |

Choose the terminal library, local server, and provider integrations to fit the
project's supported languages and operating systems. A project can use different
tools for local setup, app release, and backend deployment. Identify which tool
owns each operation and its state, even when one workspace exposes them together.

## Shared workflow

### Inspect and define the scope

- Identify the target repository, supported operating systems, actual tools,
  and external destinations. Read the existing entry points, configuration,
  tests, and migration constraints. Reuse established project conventions.
- Define each operation's prerequisites, local writes, remote writes, and
  recovery path. Separate first-run setup, routine updates, inspection,
  recovery, and reset. For a design task, document the target scopes,
  supported actions, and evidence available for each interface.
- Within each owning tool, implement task discovery, validation, approvals,
  execution, and persistence once. If it offers both terminal and browser
  interfaces, have them render the same workflow and invoke the same typed
  operations. Noninteractive launchers call that engine with explicit inputs,
  target and applicable approval. A workspace spanning tools coordinates their
  typed operations; each tool still owns its commands, credentials, state,
  resources, and verification.
- Define a small data contract for project and target identity, available
  actions, settings (type, label, source, help, validation and sensitivity),
  progress stages and substeps, prompts, results, and any supported artifact
  evidence. Validate again at submission. Tie answers and approvals to the
  current prompt and target so a stale tab or delayed discovery cannot act on a
  newer operation. Serialize conflicting writes and wait for cleanup before
  allowing a new operation in that scope.
- When workflows span tools, exchange validated nonsecret outputs and keep
  each mutation with its owner. For example, Android setup can export the
  selected app's package name and public signing certificate fingerprints.
  The deployment tool can consume that record to publish the association file
  under the backend's verified domain. Keep app release scope and backend
  deployment scope explicit.
- Use a cross-platform launcher for Python tooling where appropriate. For a thin
  standalone launcher, consider a pinned uv script with inline dependencies.
  Keep reusable implementation in the tool package and project settings in
  the consumer's existing configuration directory.
- Resolve inputs before asking. Use explicit values first, then valid saved
  answers for the exact scope, verified discovery, and project defaults. A
  conflict between an explicit identity and the saved scope requires resolution.
  Report the source of a suggestion without exposing a secret. Allow overrides
  where safe. Save accepted, validated reusable answers without marking an
  operation complete prematurely.

## Terminal wizard

### Make the next action obvious

Use the [terminal wizard prompt](setup-wizards-terminal.md) for task selection,
defaults, countdowns, confirmation, and terminal presentation.

## Browser wizard

Use the [browser wizard prompt](setup-wizards-browser.md) for the local site,
including its workspace layout, session, controls, and evidence views.

## Protect credentials and signing material

- Classify answers before storing them. Save permitted reusable secrets
  promptly in the owning tool's encrypted vault and validated nonsecret answers
  in scoped project records so interrupted runs can resume. Respect stricter
  destination rules for ephemeral tokens. Use the owning tool's established
  vault location and migration rules: Android retains its existing vault;
  deployable Go + AWS projects use the ignored repository `.deploy/` vault
  required by the deployment rules. For a new tool without a defined policy,
  use a user-owned secrets directory outside the checkout.
- Use authenticated encryption and a supported password KDF. Write vault
  updates atomically and serialize concurrent writers. Never change the
  permissions of an existing directory the tool does not own. Keep plaintext
  secrets out of logs, diffs, process arguments, tracked files, and screenshots.
- When creating a needed vault for the first time, capture and confirm its
  unlock passphrase explicitly. Unlock an existing vault without replacing
  its passphrase. Keep provider-managed sign-in caches in their normal storage.
- Mask sensitive values in vault inspection. Require explicit consent for any
  terminal reveal supported by the project. Before reset, say exactly which
  data will be cleared and which files and remote settings are affected.
  Preserve the existing vault if replacement fails.
- Distinguish an Android APK keystore from an OpenPGP publishing key. If
  managing OpenPGP keys, discover local keys in the background and keep
  **Create a new key** available while discovery runs. Ignore late results
  after the user leaves the step. Show identities, full fingerprints, and
  dates when available. Verify the selected key and passphrase before use.
  Confirm public keyserver uploads and local key deletion separately.

## Preview, apply, and recover

- Review the destination and planned creates, updates, and preserves before
  remote writes. Preserve existing protection rules and secrets where the
  user chooses to keep them. Recheck the reviewed state before applying
  if a concurrent change could invalidate the plan.
- When the engine supports saved plans, bind approval to the exact plan,
  target, source, configuration, and built artifacts. Require another preview
  on mismatch. Otherwise show the best available diff, verify the reviewed
  target and inputs again before mutation, and state the limits of the review.
  For destructive removal, reset, or consequential replacement, show intent,
  scope and consequences and require deliberate confirmation. Ask for the
  exact target name where appropriate. A countdown never authorizes a
  destructive action.
- Keep workflow generation deterministic. Separate project configuration from
  templates and runtime computations. Generate missing files when authorized,
  show diffs for changed files, and require explicit overwrite approval.
  Track generated hashes where useful. Keep unrelated workflows intact.
- Track each operation's result. Distinguish configuration collected, changes
  applied, and writes that failed or have an uncertain outcome. Make retries
  safe after partial writes. In CI summaries, give the exact destination and
  version when known and the appropriate recovery step. Treat a successful
  build, upload, publication, and post-deploy validation as separate results.
- Keep diagnostic logs useful without exposing sensitive data. Record the
  operation, error category, exit code, and safe tool status. Show where the
  log is stored or how to enable it.

## Verify and deliver

Test first and repeat runs, vault recovery, canceled confirmations, partial
failures, and delayed discovery completing after the user moves on. Mock
external writes or use disposable test resources. Verify that preview, status,
and drift cannot apply changes. Check that terminal, browser, and CI use the
same target and apply their documented approval policies. Document launch
commands, prerequisites, credential storage, and recovery. Report what was
actually verified.
