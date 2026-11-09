# Terminal setup and deployment wizards

Use this prompt to build or improve a terminal wizard that configures local
tools, credentials, GitHub environments, or deployment workflows. Pair it with
the target's publishing or infrastructure rules when those apply.

## Inspect and define the scope

- Identify the target repository, supported operating systems, actual tools,
  and external destinations. Read the existing entry points, configuration,
  tests, and migration constraints. Reuse established project conventions.
- Define each operation's prerequisites, local writes, remote writes, and
  recovery path. Separate first-run setup, routine updates, inspection,
  recovery, and reset.
- Use a cross-platform launcher for Python tooling where appropriate. For a thin
  standalone launcher, consider a pinned uv script with inline dependencies.
  Keep reusable implementation in the tool package and project settings in
  the consumer's existing configuration directory.

## Make the next action obvious

- Use a checkbox menu for independent operations. Preselect all applicable
  operations for a genuine first run. On later runs start with an empty
  selection and enable **Setup** after at least one choice. Use durable
  setup evidence to detect a returning run. A missing generated file alone
  should not reselect every operation.
- Provide **What will this do?** using the current selection. Explain
  dependencies, such as the need for a verified local signing key before
  configuring its deployment environment.
- Resolve values before asking. Offer to reuse a saved or discovered value,
  state its source without revealing it, and allow replacement. Keep
  passphrases masked and make text fields visually distinct from the panel.
- For non-sensitive prompts with a safe default, show a visible countdown.
  Stop it on keyboard, paste, mouse, or selection interaction with the
  prompt. Password entry and consequential confirmations require an
  explicit response. Reset timers and widget state when changing screens.
- Put long diffs or notes in their own scroll area. Keep the checkbox and
  action buttons visible and reachable on compact terminals. At every screen
  transition, set visibility and enabled state explicitly. A prior step can
  hide a button reused by the next step.
- Use explicit action labels such as **Apply changes**, **Overwrite
  workflows**, and **Keep existing**. For consequential operations, start
  with an unchecked acknowledgment and enable the action only after it is
  checked. Changing the selected target clears the acknowledgment. Reserve
  **Close** for the result and show whether changes were applied, skipped, or
  failed. Show 100% only after the operation succeeds.

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
- Keep workflow generation deterministic. Separate project configuration from
  templates and runtime computations. Generate missing files when authorized,
  show diffs for changed files, and require explicit overwrite approval.
  Track generated hashes where useful. Keep unrelated workflows intact.
- Track each operation's result. Distinguish configuration collected, changes
  applied, and writes that failed or have an uncertain outcome. Make retries
  safe after partial writes. In CI summaries, give the exact destination and
  version when known and the appropriate recovery step.
- Keep diagnostic logs useful without exposing sensitive data. Record the
  operation, error category, exit code, and safe tool status. Show where the
  log is stored or how to enable it.

## Verify and deliver

Test the normal entry points and the transitions that commonly regress.
Cover first and repeat runs, edited prompts stopping countdowns, long reviews
on a small terminal, canceled confirmations, vault recovery, and delayed
discovery completing after the user moves on. Mock external writes or use
disposable test resources. Run relevant checks on Linux and Windows when
both are supported. Document launch commands, prerequisites, credential
storage, and recovery. Deliver reviewable repository changes and report what
was actually verified.

Reference implementations include
[Android Repository Setup](https://github.com/lambdawalker/python.android-repo-setup)
and [Maven Key](https://github.com/lambdawalker/python.utilities.maven-key).
Use their patterns without copying project-specific coordinates or policy.
