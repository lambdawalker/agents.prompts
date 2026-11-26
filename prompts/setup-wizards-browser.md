# Browser setup and deployment wizard

Read [Setup and deployment wizards](setup-wizards.md) for the shared workflow,
vault, and execution rules. Apply this prompt to the local browser site. Use
the [terminal wizard prompt](setup-wizards-terminal.md) for the console.

## Workspace and session

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
- Render saved credentials as masked placeholders and preserve them when a
  replacement field is left blank. Keep plaintext saved credentials out of
  the browser. Lock idle sessions according to the project policy and
  resume the same step after unlock. Require explicit approval for apply.
- After completion, failure, or stop, keep a reviewable result and a route
  back to Actions in the same session. Distinguish a request to stop from
  confirmed completion or cancellation. Offer copyable sanitized diagnostics
  and show confirmed outputs. The process log must not expose raw state,
  command arguments containing secrets, or provider sign-in transcripts.

## Show only evidence the operation can produce

| Capability | Presentation when supported |
| --- | --- |
| Preview and apply | Show the exact scope, proposed changes, and any decision still needed. Put deletes and replacements first. Show safe before/after values and replacement reasons when reported. Label fallback file diffs or deployment previews according to their actual guarantees. |
| Inventory and ownership | Distinguish recorded state, proposed artifacts, and live checks. Give each artifact a type, short purpose, stable identity and owner when known. Keep the owning component label distinct from the source repository. Where an importable component and its demo host share a repository, use labels such as `email-verification` and `host-app`. Owner filters apply to both the tree and proposed changes, with clearly scoped counts. |
| Dependencies and topology | Show **Depends on** and **Used by** from recorded links and navigate to those artifacts. Offer topology only when there are actual relationships to draw. Keep a usable list or tree without WebGL. |
| History and drift | Show verified receipts or reports for the selected target, including the latest success and failed attempt, revision, outcome and a safe report download. If the provider supports a read-only drift check, show its timestamp and which resources changed, are missing or could not be verified. A drift check never applies changes. |

For setup-only workflows, the artifacts may be generated files, configured
destinations or verified build outputs. Leave unsupported features out of the
workspace and mark missing evidence honestly.

## Verify the browser interface

Exercise the browser from the normal local launcher. Check keyboard navigation,
narrow layouts, countdown pause, environment isolation, idle lock and unlock,
stale requests, report redaction, cancellation, and return to Actions. Confirm
the workspace behaves correctly when an operation has no inventory, topology,
history, or drift evidence.
