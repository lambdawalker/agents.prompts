# Setup and deployment wizards

Use this prompt to design, implement, or align a setup or deployment wizard
for an Android, iOS, web, or other project. It defines a common workflow for
the terminal and a local browser workspace. Pair it with the target's
repository, signing, publishing, and infrastructure rules. Follow their
tooling, timing, approval, and storage policies where they are more specific.

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
- Keep one implementation of task discovery, validation, approvals, execution,
  and persistence. The terminal and browser render the same workflow and invoke
  the same typed operations. Noninteractive launchers call that engine with
  explicit inputs, target and applicable approval. Keep project-specific
  commands, tools, resources, and verification in the owning implementation.
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

- Use a checkbox menu for independent operations. Preselect all applicable
  operations for a genuine first run. On later runs start with an empty
  selection and enable **Setup** after at least one choice. Use durable
  setup evidence to detect a returning run. A missing generated file alone
  should not reselect every operation.
- Provide **What will this do?** using the current selection. Explain
  dependencies, such as the need for a verified local signing key before
  configuring its deployment environment.
- Show the current action, target, effective value, and default or override.
  Keep passphrases masked and make text fields visually distinct from the panel.
- For non-sensitive prompts with a safe default, show a visible countdown.
  Say exactly what happens on expiry. Enter accepts the displayed default.
  Stop the timer on deliberate editing, paste, clicks or selection changes.
  Mouse movement and terminal resize must never type text or submit an answer.
  Password entry, unresolved required inputs, and destructive confirmations
  have no timer. Use the project policy for other approval prompts and timer
  durations. For deployment tooling without an override, follow the
  [shared deployment defaults](libraries/shared.md#deployment-questions-defaults-and-countdowns).
  Reset timers and widget state when changing screens. EOF, interruption and
  input errors cancel pending decisions.
- Put long diffs or notes in their own scroll area. Keep the checkbox and
  action buttons visible and reachable on compact terminals. At every screen
  transition, set visibility and enabled state explicitly. A prior step can
  hide a button reused by the next step.
- Keep long-running work off the input/render loop and discard stale results.
  Update countdowns in place. Keep redirected output and CI logs readable
  without animation or escape sequences. Respect terminal capabilities,
  `NO_COLOR`, and supported Windows console behavior. Restore the terminal
  after success, failure, and cancellation.
- Use explicit action labels such as **Apply changes**, **Overwrite
  workflows**, and **Keep existing**. For consequential operations, start
  with an unchecked acknowledgment and enable the action only after it is
  checked. Changing the selected target clears the acknowledgment. Reserve
  **Close** for the result and show whether changes were applied, skipped, or
  failed. Show 100% only after the operation succeeds.

## Browser wizard

Use the [Infra Composer browser wizard](https://github.com/lambdawalker/go.infra.composer/tree/a613f7d7d078a5e3d05366d33b284430c06766ad/deploycli/internal/wizardweb/ui)
as the interaction and layout reference. Reuse the existing browser where
compatible. For other engines, implement the same workspace and action
semantics with the project's tooling.

| Place | Contents and behavior |
| --- | --- |
| Browser tab | Use `Setup: <repository>` or `Deploy: <repository>` for the wizard's purpose. Use a bundled favicon. |
| Header | Put the main menu and opened-target tabs on the left. Put the GitHub link, displayed as `/owner/repository` when applicable, and the System/Light/Dark control on the right. Use the menu for New/Open target and explicitly scoped project-wide operations. Creating a draft or switching tabs selects a target. It never runs setup or deploy. Switch between operations or protect the active run from retargeting. |
| Left pane | Show phased progress with the current stage and its substeps. Keep project/target context available as a separate view in the same column. Support collapsing progress and switching context from the pane's side controls. |
| Center workspace | Provide **Actions**, **Settings**, **Overview**, and **Process output** tabs. Show supported actions as cards with their consequences. Prefer a read-only preview as the initial deploy-wizard selection when available. Keep safe settings and the available actions visible from startup. Enable submission only in the relevant step. Make actionable fields editable and show validation near each field. Keep nonsecret edits after failed checks. Retain the saved target and inventory in Overview while a new plan runs. |
| Right pane | When artifacts exist, show a searchable hierarchy and a separate selected-artifact details panel. Selecting a proposed change or related artifact reveals its details. Keep the tree, details, and other visual views synchronized. |
| Fixed action bar and dialogs | Keep the active action and its controls in view when content scrolls. Put eligible countdowns on **Continue**, with **Pause** beside it. Pause on deliberate interaction or when leaving the tab. Use blocking dialogs for vault creation/unlock, provider sign-in and short consequential confirmations. Reserve **Close wizard** for ending the session. |

- Follow the reference's compact workspace, restrained borders and status
  colors, and clear separation between navigation, detail panels, and buttons.
  Keep panels resizable where space allows and stack the panes at narrow
  widths. Keep tabs keyboard-accessible, provide a keyboard equivalent if
  drag reordering is offered, and make focus visible. Use status text alongside
  color, and keep controls reachable when the log or artifact tree scrolls.
- Have the existing local launcher offer terminal or browser on supported
  desktops. Preserve documented entry points and offer explicit terminal,
  browser, and browser-without-auto-open modes. Keep headless/SSH defaults and
  noninteractive CI terminal-based. Give the operator a manual sign-in or
  browser-open path when automatic opening is unavailable. Keep the local
  wizard separate from the website or backend it deploys.
- Serve the local workspace on loopback with an available port and a private
  session credential. Keep the credential out of logs, ordinary request URLs,
  referrers, and reusable records. Validate the host, origin and session on
  requests, and check the current action, prompt and target on the server.
  Serve app assets locally with a restrictive content policy. Renderer requests
  select declared actions and submit typed fields. The server owns command
  selection, credential access, execution, and redaction. A remotely hosted
  control plane requires its own authentication and authorization design.
- After completion, failure, or stop, keep a reviewable result and a route
  back to Actions in the same session. Distinguish a request to stop from
  confirmed completion or cancellation. Offer copyable sanitized diagnostics
  and show confirmed outputs. The process log must not expose raw state,
  command arguments containing secrets, or provider sign-in transcripts.

### Show only evidence the operation can produce

| Capability | Presentation when supported |
| --- | --- |
| Preview and apply | Show the exact scope, proposed changes, and any decision still needed. Put deletes and replacements first. Show safe before/after values and replacement reasons when reported. Label fallback file diffs or deployment previews according to their actual guarantees. |
| Inventory and ownership | Distinguish recorded state, proposed artifacts, and live checks. Give each artifact a type, short purpose, stable identity and owner when known. Keep the owning component label distinct from the source repository. Where an importable component and its demo host share a repository, use labels such as `email-verification` and `host-app`. Owner filters apply to both the tree and proposed changes, with clearly scoped counts. |
| Dependencies and topology | Show **Depends on** and **Used by** from recorded links and navigate to those artifacts. Offer topology only when there are actual relationships to draw. Keep a usable list or tree without WebGL. |
| History and drift | Show verified receipts or reports for the selected target, including the latest success and failed attempt, revision, outcome and a safe report download. If the provider supports a read-only drift check, show its timestamp and which resources changed, are missing or could not be verified. A drift check never applies changes. |

For setup-only workflows, the artifacts may be generated files, configured
destinations or verified build outputs. Leave unsupported features out of the
workspace and mark missing evidence honestly.

## Protect credentials and signing material

- Classify answers before storing them. Save permitted, validated reusable
  answers promptly in an encrypted local vault so interrupted runs can
  resume. Respect stricter destination rules for ephemeral tokens. Keep
  the vault outside the checkout in the project's user-owned secrets
  directory, such as `~/.projects/<project>/secrets`.
- Use authenticated encryption and a supported password KDF. Write vault
  updates atomically and serialize concurrent writers. Never change the
  permissions of an existing directory the tool does not own. Keep secrets
  out of logs, diffs, process arguments, repository files, and screenshots.
- On first use, create and confirm the unlock passphrase explicitly. Use the
  project's existing storage location and migration rules. In the browser,
  show masked placeholders for saved values, retain them on blank replacement,
  and never expose plaintext saved credentials through the browser. Lock idle sessions using
  the project policy and resume the same step after successful unlock. Keep
  provider-managed sign-in caches in their normal storage.
- Mask sensitive values in vault inspection and require explicit consent to
  reveal them. Before reset, say exactly which data will be cleared and
  which files and remote settings are affected. Preserve the existing vault
  if replacement fails.
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
  Browser apply requires explicit approval. For destructive removal, reset,
  or consequential replacement, show intent, scope and consequences and
  require deliberate confirmation, including the exact target name where
  appropriate. A countdown never authorizes a destructive action.
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

Test the normal entry points and the transitions that commonly regress.
Cover first and repeat runs, edited prompts stopping countdowns, long reviews
on a small terminal, canceled confirmations, vault recovery, and delayed
discovery completing after the user moves on. Mock external writes or use
disposable test resources. Run relevant checks on Linux and Windows when
both are supported, and on macOS for Xcode-specific execution. For the
browser, verify keyboard navigation, narrow layout, environment isolation,
idle lock/unlock, stale requests, report redaction, cancellation, and return
to Actions. Include failed and partial operations and unsupported capabilities.
Verify that preview, status, and drift cannot apply changes, and that terminal,
browser, and CI preserve the same target and approval rules. Document launch
commands, prerequisites, credential storage, and recovery. Report what was
actually verified.

Reference implementations include
[Android Repository Setup](https://github.com/lambdawalker/gradle.android-repo-setup)
and [Maven Key](https://github.com/lambdawalker/python.utilities.maven-key).
Use their patterns without copying project-specific coordinates or policy.
