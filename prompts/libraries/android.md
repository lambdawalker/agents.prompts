# Android libraries

Use this section with the selected primary documentation workflow and [shared library rules](shared.md). Apply it to Android libraries and their sample/demo applications. For non-Android Kotlin libraries, use the [Kotlin section](kotlin.md).

## Standalone or part of a larger project

- Standalone: document Android API/SDK requirements, installation, permissions, lifecycle, ownership, configuration, complete integrations, demo navigation, and source links locally.
- Within a larger project: retain those local contracts, and link to the host's onboarding/navigation, permission policy, backend boundaries, and integration architecture. Cover Gradle project dependencies for monorepos and confirmed published coordinates for external consumption separately. Keep independent module versions explicit.

Preserve established AGP, Gradle, Kotlin, Compose, JDK, and SDK choices. Check official compatibility requirements before changing tooling; never copy a screenshot plugin/version blindly from another repository. Use the [Android publishing supplement](../android-library-publishing.md) only for the requested publishing scope.

## Always provide composable previews

Every library-owned and demo/sample composable must have preview coverage, including new or changed composables. Place an actual preview beside the component or in a discoverable dedicated preview source set. Parameterized components and composables requiring slots, scopes, callbacks, owners, or injected services must have a preview wrapper with deterministic fixture inputs. A meaningful parent preview can cover a private helper when the coverage mapping records that helper and its visible state; do not pretend that one screen preview demonstrates every state of every component.

Provide representative defaults and important states: front/back or multi-step flow where present, empty/loading/error/recovery, disabled/selected controls, and useful light/dark, locale, font-scale, or narrow-layout variants. Use scenario/parameter providers to avoid unnecessary duplication.

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
- Run appropriate compile/lint/tests for the actual screenshot source sets and module dependencies. Do not remove lint enforcement or hardware tests to hide incompatible renderer setup.

Inspect rendered images for blank screens, missing guides, misaligned coordinates, clipping, unreadable labels, incorrect states, and theme/localization issues before accepting them. Host previews do not establish camera, permissions, navigation, focus, inference, lifecycle, or device correctness; verify those separately when relevant.

## Use the reviewed images in documentation

Explain the primary flow on the landing page using the most useful reviewed screenshots, then link to full guides and demos. Include additional screenshots where they explain overlays, review/retake states, controls, or localization. Keep baseline files and curated documentation assets distinct but traceable through a manifest and intentional acceptance step. Apply the shared latest-release entry and coherent-navigation rules to both standalone and integrated documentation.
