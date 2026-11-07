# Android libraries

Use this section with the selected primary documentation workflow and [shared library rules](shared.md). Apply it to Android libraries and their sample/demo applications. For non-Android Kotlin libraries, use the [Kotlin section](kotlin.md).

## Standalone or part of a larger project

- Standalone: document Android API/SDK requirements, installation, permissions, lifecycle, ownership, configuration, complete integrations, demo navigation, and source links locally.
- Within a larger project: retain those local contracts, and link to the host's onboarding/navigation, permission policy, backend boundaries, and integration architecture. Cover Gradle project dependencies for monorepos and confirmed published coordinates for external consumption separately. Keep independent module versions explicit.

Preserve established AGP, Gradle, Kotlin, Compose, JDK, and SDK choices. Check official compatibility requirements before changing tooling; never copy a screenshot plugin/version blindly from another repository. Use the [Android publishing supplement](../android-library-publishing.md) only for the requested publishing scope.

## Inherit the consuming application's style

Reusable UI must use the consuming application's active theme through its normal public entry points. Apply these rules to every screen, button, text field, label, icon, surface, dialog, sheet, and overlay, including nested library UI.

- Read colors, typography, and shapes from the host's `MaterialTheme` and relevant composition locals. Preserve the established Material version. Keep branded, dynamic-color, and light/dark theme setup at the host or demo root; reusable components must not install a private theme or force a theme mode.
- Use semantic color roles with their paired content colors for normal, selected, disabled, and error states. Avoid fixed palettes, success greens, white camera labels, and other literal UI colors. Back camera instructions and controls with a host-themed surface where needed for contrast. Preserve explicitly supplied host overrides.
- Derive text styles, font families, weights, sizes, and letter spacing from host typography. Avoid overriding individual font attributes or forcing text transformations. Respect locale, accessibility font scaling, wrapping, and multiline labels.
- Give equivalent actions the same host-derived shape across libraries. In the established Material 3 convention, primary, outlined, and text actions use `MaterialTheme.shapes.small`; apply that convention consistently so a default pill-shaped button cannot appear beside a fixed-radius host action. Use host shape tokens for cards, fields, and surfaces as appropriate.
- Remove arbitrary fixed button heights, widths, padding, and corner radii that impose a separate visual style or clip scaled text. Prefer Material component defaults and content-driven sizing, while preserving minimum accessible touch targets. MaterialTheme has no universal spacing/dimension scale; use the host's established dimension tokens when available and provide focused sizing parameters or modifiers where consumers need control.
- Keep intrinsic geometry such as camera frame mapping, image aspect ratios, and functional control bounds correct. Document those constraints and expose suitable customization where appropriate. Forward caller modifiers and configuration through public wrappers so host customization reaches the rendered component.
- Audit all production composables and integration paths, including review/retake screens, loading/error states, permission screens, and accessibility content. Demo theme choices must not leak into the reusable module. Keep customization APIs source-compatible where possible and document any necessary migration.

Verify integration under a deliberately distinct host palette, typography, and shapes, in light and dark modes and with larger text. Check nested library screens beside host controls; visual consistency must hold through the consuming application's normal entry point. Add focused regression coverage for demonstrated theme propagation failures.

## Always provide composable previews

Every library-owned and demo/sample composable must have preview coverage, including new or changed composables. Place an actual preview beside the component or in a discoverable dedicated preview source set. Parameterized components and composables requiring slots, scopes, callbacks, owners, or injected services must have a preview wrapper with deterministic fixture inputs. A meaningful parent preview can cover a private helper when the coverage mapping records that helper and its visible state; do not pretend that one screen preview demonstrates every state of every component.

Every composable that represents a screen must have previews for its most important supported states. Include the default state and applicable loading, empty, populated/success, validation/error, retry/recovery, disabled/busy, and front/back or multi-step states. Select meaningful states based on the actual screen contract and document that selection in the scenario mapping.

Every required screen state must have both light-mode and dark-mode previews. Make theme mode explicit using multipreview annotations or deterministic parameter/scenario providers; a preview that follows the developer machine's current theme does not establish both modes. Add representative locale, larger-font, and narrow-layout variants where text or layout can change. Use shared fixtures/providers to avoid unnecessary duplication.

Separate the visual composable from runtime wiring when necessary so previews never start a camera, inference engine, network client, permission request, or real navigation service. Supply synthetic data, explicit coordinate mappings, stable clock/random inputs, and controlled animation states. Ensure overlays receive the actual fixture events/state needed to draw. The preview must remain faithful to production UI, not become an unrelated manually drawn imitation.

Document the composable-to-preview/scenario mapping, compile previews in the appropriate source set, and update coverage when composables change. A preview annotation alone is not a screenshot test.

## Generate and maintain a screenshot test suite

For every Android project with library/demo UI, implement or repair an executable screenshot test suite; a plan or a few preview annotations is insufficient. For a genuinely headless Android library with no composable/UI surface, explicitly record that there is nothing to capture instead of fabricating screens. When UI is introduced, add previews and the suite with that change.

Reuse the established renderer or select a compatible Compose preview screenshot/Robolectric/device approach based on the actual toolchain and scenarios. Apply and configure the plugin/source sets, enable required options deliberately, and verify the exact Gradle task names against that project. Do not assume the app's setup also applies to the library module.

The suite must:
- Discover and render required library and demo scenarios; fail on missing required scenarios or zero tests. Cover each UI module.
- Build deterministic images with documented viewport, density, theme, locale, font scale, fonts, clock, synthetic data, and animation state.
- Keep candidate generation, intentional baseline acceptance, and comparison/validation as separate operations. Normal validation must never silently overwrite accepted baselines.
- Validate accepted baselines in CI. If no approved baseline exists, label initial output as candidates and check the required scenario inventory; a second identical render establishes repeatability only, not regression coverage.
- Offer a manual rendering workflow and upload candidate images, comparison diffs/reports, and a source/hash manifest. Document exact local commands.
- Classify CI screenshot candidates against the verified approved archive. When every PNG matches byte for byte but render inputs changed, publish a provenance-only patch for the baseline and documentation manifests. Keep image files unchanged and require a maintainer to review and apply the patch.
- When a PNG differs or a variant is added or removed, publish a before-and-after gallery that identifies affected scenarios and marks missing sides. Require explicit baseline acceptance. Report an unchanged result when both PNGs and render inputs match.
- Run appropriate compile/lint/tests for the actual screenshot source sets and module dependencies. Do not remove lint enforcement or hardware tests to hide incompatible renderer setup.

Inspect rendered images for blank screens, missing guides, misaligned coordinates, clipping, unreadable labels, incorrect states, and theme/localization issues before accepting them. Host previews do not establish camera, permissions, navigation, focus, inference, lifecycle, or device correctness; verify those separately when relevant.

## Use the reviewed images in documentation

Explain the primary flow on the landing page using the most useful reviewed screenshots, then link to full guides and demos. Include additional screenshots where they explain overlays, review/retake states, controls, or localization. Keep baseline files and curated documentation assets distinct but traceable through a manifest and intentional acceptance step. Apply the shared latest-release entry and coherent-navigation rules to both standalone and integrated documentation.

For Android library publishing, use provider-scoped immutable tags with a plain semantic version input. JitPack may expose an explicit opt-in bypass for older pending attempts. Apply the validation, migration, and documentation rules in the [publishing supplement](../android-library-publishing.md).
