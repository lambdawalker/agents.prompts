# Terminal setup and deployment wizard

Read [Setup and deployment wizards](setup-wizards.md) for the shared workflow,
vault, and execution rules. Apply this prompt to the terminal interface. Use
the [browser wizard prompt](setup-wizards-browser.md) for the local site.

## Make the next action obvious

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

## Verify the terminal interface

Exercise the normal launchers on each supported console. Check first and repeat
runs, editing and pasting during a countdown, long reviews on small terminals,
canceled prompts, plain redirected output, and terminal cleanup after
interruption. Include supported Windows console behavior, Linux terminals, and
macOS when running Xcode-specific steps.

Reference implementations include
[Android Repository Setup](https://github.com/lambdawalker/gradle.android-repo-setup)
and [Maven Key](https://github.com/lambdawalker/python.utilities.maven-key).
Use their patterns without copying project-specific coordinates or policy.
