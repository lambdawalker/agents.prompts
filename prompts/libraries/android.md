# Android libraries

Use this section with the selected primary documentation workflow and [shared library rules](shared.md). Apply it to Android libraries and their sample/demo applications. For non-Android Kotlin libraries, use the [Kotlin section](kotlin.md). Load [Shared Android repository automation](../android-repository-automation.md) for CLI ownership, declarative configuration, shared Astro documentation, migration checks and mixed Android/JVM builds. Its ecosystem-specific rules take precedence over generic local-script and deployment-vault suggestions.

## Standalone or part of a larger project

- Standalone: document Android API/SDK requirements, installation, permissions, lifecycle, ownership, configuration, complete integrations, demo navigation, and source links locally.
- Within a larger project: retain those local contracts, and link to the host's onboarding/navigation, permission policy, backend boundaries, and integration architecture. Cover Gradle project dependencies for monorepos and confirmed published coordinates for external consumption separately. Keep independent module versions explicit.

Preserve established AGP, Gradle, Kotlin, Compose, JDK, and SDK choices. Check official compatibility requirements before changing tooling; never copy a screenshot plugin/version blindly from another repository. Use the [Android publishing supplement](../android-library-publishing.md) only for the requested publishing scope.

## Every component repository needs a runnable demo app

Every Android component/library repository must contain and maintain an installable demo application, normally `:app`, alongside its reusable library module(s). This applies to standalone and system-integrated components, including libraries without their own UI. A parent product app, screenshots, Compose previews, tests, or documentation snippets do not replace the component's demo. Reuse an existing host; in a multi-module repository one demo app may exercise several components when each has a clear entry and coverage mapping. For a headless component, provide a small host that invokes its real API and displays useful results without adding UI dependencies to the library itself.

Inspect these implementation examples before adapting the pattern. They illustrate ownership and composition; they are not proof that every requirement is already satisfied:

| Reference | Inspect and preserve the pattern |
| --- | --- |
| [Android Auth](https://github.com/lambdawalker/android.apexfission.auth) | [`app/build.gradle.kts`](https://github.com/lambdawalker/android.apexfission.auth/blob/main/app/build.gradle.kts) consumes `project(":auth")`; [`MainActivity`](https://github.com/lambdawalker/android.apexfission.auth/blob/main/app/src/main/java/com/apexfission/android/auth/MainActivity.kt) owns navigation and runtime wiring. The test menu separates live onboarding, a UI catalog, and a local card-capture playground. |
| [Android Card Detector](https://github.com/lambdawalker/android.apexfission.carddetector) | [`app/build.gradle.kts`](https://github.com/lambdawalker/android.apexfission.carddetector/blob/main/app/build.gradle.kts) consumes the local detector/model modules; [`WelcomeActivity`](https://github.com/lambdawalker/android.apexfission.carddetector/blob/main/app/src/main/java/com/apexfission/android/carddetector/demo/WelcomeActivity.kt) provides a shared catalog and the top App settings action, with focused capture/simulation/backend entries. |
| [Go/AWS Card Capture demo backend](https://github.com/lambdawalker/go.appexfission.aws.card-capture/tree/main/infrastructure/demo-invitations) | Separate demo infrastructure supplies the Card Detector app's authenticated Backend OCR example. Demo authentication, endpoints and orchestration remain separate from the reusable capture library. This Go repository is a backend reference, not the owner of the Android APK or a requirement to copy its provider configuration. |

### Keep the demo a real consumer

- Use an Android application module with its own manifest, launcher and application ID. Depend on the repository's current library source through Gradle project dependencies for normal development; exercise public APIs instead of copying their implementation into the app or silently testing an older published artifact. Document published-artifact integration separately, and preserve explicit published sibling pins required by library publication.
- Keep demo navigation, host theme, demo-only authentication/network adapters, diagnostics, configuration and reset controls in the app or an appropriate shared host dependency. The library must not depend on the demo. Services that are part of the library's public contract remain library-owned. Exclude the app and demo-only dependencies from library publications.
- Provide focused, working examples for the primary public flows and meaningful variants, with observable success, cancellation, failure/retry and return-to-menu behavior. Use actual library callbacks; report local completion separately from backend confirmation. Release owned cameras, clients and image buffers according to the library's contract.
- Keep local component demos and deterministic UI catalogs usable without unrelated sign-in or backend setup. Request permissions or start hardware when entering the demo that needs them, not merely when opening the launcher catalog. Label simulations and local-only results accurately. Offer separately labeled live integration entries where the component requires a backend; show configuration/loading/error/retry states without blocking the other entries.
- Document public configuration inputs, setup prerequisites, supported variants and exact build/install/launch commands. Keep secrets and administrative credentials out of the APK and source. Use synthetic fixtures for sensitive flows. Preserve the established shared application/signing conventions and toolchain; a demo APK version does not establish a library release.

### Use the shared demo catalog

Use [Android UI Common](https://github.com/lambdawalker/android.ui.common)'s `CatalogScreen`, `ContentCard`, and `SectionHeading` for the established Compose demo shell. Present the component title and short purpose, then focused entries with a title, concise description and clear launch action. Reuse its layout, insets and host-themed controls instead of copying a private catalog framework into each repository. Keep scenario content and runtime callbacks in the owning demo.

Check UI Common's current `IMPORT.md` and the API available at the target's confirmed dependency pin before using it. Do not invent a release or assume an API on main is published. Preserve a compatible established non-Compose host when applicable, with the same catalog and settings behavior; do not migrate toolchains solely to copy a reference screen.

### Put App settings at the top of every demo

Every demo app must expose a visible **App settings** button in its launcher/catalog header, above the demo entries. Use `CatalogScreen.headerActions` in the shared Compose shell, following Card Detector's outlined, full-width host-themed button. Keep it available before permissions or backend configuration succeed; do not hide it inside an individual scenario or an overflow menu. Use localized string resources and an accessible text label, preserving touch targets, contrast and larger-font layouts.

The action opens Android's application-details settings for the **currently installed demo package**, where testers can manage permissions and supported links. It is not an in-app preferences page or a generic device-settings screen. Pass an `onOpenSettings` callback into the visual screen and launch the intent from the demo Activity, for example:

```kotlin
private fun openAppSettings() {
    startActivity(
        android.content.Intent(
            android.provider.Settings.ACTION_APPLICATION_DETAILS_SETTINGS,
            android.net.Uri.fromParts("package", packageName, null),
        ),
    )
}
```

Use the runtime package name so debug, signed-debug and other application-ID variants target themselves. Keep preview callbacks inert. Returning with system Back must restore a usable demo screen; re-evaluate applicable permissions when resuming or recreating the app after settings changes instead of trusting stale permission state. Keep any permission-denied recovery links consistent with this destination.

### Verify and document the demo contract

- Build the demo against the local library using the repository's supported Gradle tasks in CI; compile relevant preview/test source sets. Keep demo application discovery/build checks separate from library publication selection through the [automation supplement](../android-repository-automation.md).
- Verify catalog navigation and primary outcomes, including cancel/back/retry. Add a focused UI check that the header button is present and invokes its callback, plus an intent check for `ACTION_APPLICATION_DETAILS_SETTINGS` and the installed package URI. Exercise settings return and permission changes on a device/emulator; report unavailable device checks explicitly.
- Include the catalog and settings button in light/dark previews and screenshot scenarios, with representative larger-text and locale coverage under the rules below. Screenshots verify the app header; they do not establish that Android settings opened or permissions refreshed.
- Link the demo from README and human/agent guides. Map each entry to its source, prerequisites, exact launch route, actions, expected result and recovery. Explain local versus live behavior, backend setup where needed, and the purpose of App settings. Record checks actually run; a compiled APK alone does not prove a working camera, network or system-settings round trip.

## Maven Central, JitPack and publishing setup

When asked to add deployment/publication scripts for Maven Central or JitPack, load and implement the [Android publishing supplement](../android-library-publishing.md). It covers both standalone libraries and libraries inside a larger application. Implement the release Gradle configuration, manual workflows, immutable release tracking, verification/recovery helpers and runbook; do not stop at a workflow skeleton or a list of secrets. Publish only the intended library release variant, excluding demo applications and internal/mock modules.

Use the pinned shared `android-repo-setup` wizard and generated cross-platform launchers to configure GitHub environments, variables and encrypted secrets after a concrete review. Do not recreate a Python setup wizard. Public JitPack's environment is secret-free; Central also needs its propagation-delay environment. Preserve existing values, protection rules and the shared vault/signing conventions. Creating the tooling does not authorize live settings changes or publishing a package.

Use CardDetector as the implementation reference identified in the supplement, adapting its approach to the target repository rather than copying its license, coordinates, module identifiers or toolchain. The supplement remains authoritative for detailed release, setup and recovery requirements.

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

For every Android project with library/demo UI, implement or repair an executable screenshot test suite; a plan or a few preview annotations is insufficient. A headless Android library has no library-owned UI to capture; record that boundary while still covering its required demo app's catalog, controls and results. When library UI is introduced, add its previews and scenarios with that change.

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

For Android library publishing, use provider-scoped immutable tags with a plain semantic version input. Use the pinned CLI’s explicit JitPack override only under its documented version/provenance guards; preserve older pending attempts. Apply the validation, migration, and documentation rules in the [publishing supplement](../android-library-publishing.md).
