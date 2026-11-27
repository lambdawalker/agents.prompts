# Browser setup and deployment wizard

Read [Setup and deployment wizards](setup-wizards.md) for the shared workflow,
vault, and execution rules. Apply this prompt to the local browser site. Use
the [terminal wizard prompt](setup-wizards-terminal.md) for the console.

## Workspace and session

Use the [Infra Composer browser wizard](https://github.com/lambdawalker/go.infra.composer/tree/ff065d4b853c7b0a2981da342767a569f18633fb/deploycli/internal/wizardweb/ui)
as the interaction and layout reference. Reuse the existing browser where
compatible. For other engines, implement the same workspace and action
semantics with the project's tooling.

| Place | Contents and behavior |
| --- | --- |
| Browser tab | Use `Setup: <repository>` or `Deploy: <repository>` for the wizard's purpose. Use a bundled favicon. |
| Header | Put the main menu and opened-target tabs on the left. Put the GitHub link, displayed as `/owner/repository` when applicable, and the System/Light/Dark control on the right. Show discovered modules/apps or opened deployment environments as primary target tabs as soon as available. Do not add a synthetic repository tab or a separate module-selection step when discovery already supplies those targets. Use the menu for New/Open target where applicable and explicitly scoped project-wide operations, including Vault and supported exports. Creating a draft or switching tabs selects a target. It never runs setup or deploy. Keep an active run bound to its original target across tab switches, and block conflicting work until that run releases its locks. |
| Left pane | Show phased progress with the current stage and its substeps. Keep project/target context available as a separate view in the same column. Support collapsing progress and switching context from the pane's side controls. |
| Center workspace | Provide **Actions**, **Settings**, **Overview**, and **Process output** tabs; add **Topology** when a recorded relationship graph can support it. Show only the active target's supported actions as cards with clear descriptions of consequences; do not require a separate explanation button. Submit only actions selected on that target's tab, and revalidate the target and action list on the server. A shared repository operation may execute once while retaining the target that requested it. Prefer Preview as the initial deploy-wizard selection when available. Preview must not apply deployment changes; disclose any prerequisite or state-bootstrap writes and their approvals before starting them. A pending bootstrap choice must not advance on a timer. Keep safe settings and the available actions visible from startup. Enable submission only in the relevant step. Make actionable fields editable and show validation near each field. Keep nonsecret edits after failed checks. Retain the saved target and inventory in Overview while a new plan runs. |
| Right pane | When artifacts exist, show a searchable hierarchy and a separate selected-artifact details panel. Follow the active target: an Android app may show APK outputs, a library AAR or publication coordinates, and a deployment environment its own resources. Label expected but unbuilt artifacts as such. Selecting a proposed change or related artifact reveals its details. Keep the tree, details, and other visual views synchronized. |
| Fixed action bar and dialogs | Keep the active action and its controls in view when content scrolls. Put eligible countdowns on **Continue**, with **Pause** beside it. Pause on deliberate interaction or when leaving the tab; keep the timer stopped until the operator explicitly resumes or submits. An action-menu timer may select Preview only where the project policy allows; it never approves Apply. Use blocking dialogs for vault creation/unlock, provider sign-in and short consequential confirmations. Reserve **Close wizard** for ending the session, and state what happens to an active operation before closing. |

- Follow the reference's compact workspace, restrained borders and status
  colors, and clear separation between navigation, detail panels, and buttons.
  Keep panels resizable where space allows and stack the panes at narrow
  widths. Keep tabs keyboard-accessible, provide a keyboard equivalent if
  drag reordering is offered, and make focus visible. Use status text alongside
  color, and keep controls reachable when the log or artifact tree scrolls.
- Show a loading state while discovering targets and preparing the first
  workspace. Put brief operation status in the fixed bottom bar with an icon,
  text, and appropriate color. Keep detailed diagnostics in Process output;
  do not duplicate transient status labels in the central Actions or Settings
  panels. Show connection failure as an actionable error, not a successful
  step or an ignorable decoration.
- In Settings, show the full known input form for the active target before
  running its selected actions. Group fields by action and put shared **Common
  fields** first. Render each semantic input once per target; omit groups with
  no editable fields. Populate verified nonsecret defaults and compatible
  session drafts, identify their source, and preserve explicit local overrides.
  A typed value shared across target tabs becomes a default in the other tabs
  only for the same semantic key and scope. Keep confirmation fields coupled
  to their secret, validate the complete form, and submit exactly the shown
  values to the owning engine instead of asking one field at a time again.
  Never hydrate an existing vault secret into a browser input. Where the engine
  can report a saved value's presence, show a green saved-in-vault indicator
  without exposing the value; tie signing indicators to the corresponding key
  path and alias rather than treating any saved password as a match.
- Have the existing local launcher offer terminal or browser on supported
  desktops. Preserve documented entry points and offer explicit terminal,
  browser, and browser-without-auto-open modes. Keep headless/SSH defaults and
  noninteractive CI terminal-based. Give the operator a manual sign-in or
  browser-open path when automatic opening is unavailable. Keep the local
  wizard separate from the website or backend it deploys.
- Serve the local workspace on loopback with an available port and a private
  credential scoped to this wizard instance. Bootstrap the browser with a
  one-time local handoff that removes any launch secret before it can enter
  ordinary request URLs, persistent history, referrers, or logs. Reject
  mismatched Host values and cross-origin API requests. Require a valid session
  for every private API, event stream, and report/download request, plus CSRF
  protection for state-changing requests. Check the current action, prompt and
  target on the server. Serve app assets locally with a restrictive content
  policy. Renderer requests select declared actions and submit typed fields.
  The server owns command selection, credential access, execution, and
  redaction. Render provider data and process output as escaped text. A
  remotely hosted control plane requires its own authentication and
  authorization design.
- Render saved credentials as masked placeholders, never as replacement field
  values. A blank replacement preserves the saved value. Keep plaintext saved
  credentials out of the browser. Create or unlock a needed vault in a dialog
  at startup. Lock idle sessions on the server according to the project policy
  (20 minutes for the Android setup reference), reject new operation mutations
  until unlock, and resume the same step afterward. Do not ask for the vault
  passphrase again when starting each action while the session remains
  unlocked. An already-running operation may finish while locked. Revoke the
  session and clear typed/generated secret drafts on explicit Close; expire
  abandoned sessions. Require explicit approval for apply.
- If the project supports export, place it in the main menu rather than under
  an action card. A Bitwarden secure-note flow can collect filled and generated
  values from all target tabs, omit blanks and confirmation fields, verify the
  intended account before writing, and report an uncertain CLI result without
  blindly creating a duplicate. A copy-JSON flow should reauthenticate with
  the vault passphrase and combine authorized saved values with current form
  drafts under distinct target and field keys. Deduplicate shared values, keep
  the export out of logs and browser persistence, and explain what values are
  included. Preserve generated values across form transitions until lock or
  Close; export must not silently reset or regenerate them.
- After completion, failure, or stop, keep a reviewable result and a route
  back to Actions in the same session. Report a stop request as pending until
  local execution and cleanup finish, then release its locks. Show confirmed
  results, identify uncertain remote writes, and provide recovery steps.
  Offer copyable sanitized diagnostics and show confirmed outputs. The process
  log must not expose raw state, command arguments containing secrets, or
  provider sign-in transcripts.

## Show only evidence the operation can produce

| Capability | Presentation when supported |
| --- | --- |
| Preview and apply | Show the exact scope, proposed changes, and any decision still needed. Put deletes and replacements first. Show safe before/after values and replacement reasons when reported. Label fallback file diffs or deployment previews according to their actual guarantees. |
| Inventory and ownership | Distinguish recorded state, proposed artifacts, and live checks. Give each artifact a type, short purpose, stable identity and owner when known. Keep the owning component label distinct from the source repository. Where an importable component and its demo host share a repository, use labels such as `email-verification` and `host-app`. Owner filters apply to both the tree and proposed changes, with clearly scoped counts. |
| Dependencies and topology | Show **Depends on** and **Used by** from recorded links and navigate to those artifacts. Offer topology only when there are actual relationships to draw. Keep a usable list or tree without WebGL. |
| History and drift | Show verified receipts or reports for the selected target, including the latest success and failed attempt, revision, outcome and a safe report download. If the provider supports a read-only drift check, show its timestamp and which resources changed, are missing or could not be verified. A drift check never applies changes. |

For setup-only workflows, the artifacts may be generated files, configured
destinations or verified build outputs. Leave unsupported features out of the
workspace and label expected but unavailable evidence as unknown.

## Verify the browser interface

Exercise the browser from the normal local launcher. Check keyboard navigation,
narrow layouts, countdown pause, environment isolation, idle lock and unlock,
stale requests, report redaction, cancellation, and return to Actions. Confirm
the workspace behaves correctly when an operation has no inventory, topology,
history, or drift evidence. For discovered targets, check tab count against
the engine, per-tab action submission and artifacts, shared field defaults and
overrides, full-form submission without duplicate prompts, omitted empty
groups, saved-secret indicators, generated values and regeneration races, and
exports after changing tabs and completing a form. Verify startup loading and
that transient status appears only in the footer while details remain in
Process output.
