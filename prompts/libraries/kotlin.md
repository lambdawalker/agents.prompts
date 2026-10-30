# Kotlin libraries

Use this section with the selected primary documentation workflow and [shared library rules](shared.md). For Android targets or Android Compose UI, also apply the [Android section](android.md); do not impose Android tooling on JVM-only or other targets.

## Standalone or part of a larger project

- Standalone: document Gradle coordinates, plugin/compiler requirements, supported targets, complete examples, public contracts, testing, and compatibility locally.
- Within a larger project: retain module API/release ownership and link to host architecture and data flows. Distinguish Gradle project dependencies/composite builds from confirmed external coordinates and explain the integration boundary.

Identify JVM, Kotlin Multiplatform, Android, JS/Wasm, or native targets from the actual build. Document supported platforms and tested combinations without extrapolating one target's success to all others. For multiplatform libraries, separate common contracts from target-specific behavior and dependencies.

## Contracts and examples

Document coroutine scopes/dispatchers, cancellation, structured concurrency, Flow emission/replay/backpressure behavior, resource ownership, nullability, errors, and thread safety where present. Include Java interop or platform interop when exposed. Use executable examples, runnable samples, and public API verification appropriate to the repository. Preserve established versioning and binary/source compatibility practices.

## Previews, screenshots, and documentation

For every library/demo Compose composable, provide deterministic previews or preview wrappers supported by that target/toolchain. Record coverage through meaningful parent scenarios for private helpers. Compile previews and render useful UI states without network, device, or other runtime services. Android targets must implement the Android screenshot suite requirements; other UI targets should use their established compatible screenshot renderer and CI validation rather than pretending Android preview annotations work everywhere.

For headless libraries, prioritize code examples and real output; do not invent a UI or require screenshots without a useful visual subject. For UI libraries, reuse reviewed screenshots on the landing page and walkthroughs with truthful target/source provenance. Open the latest confirmed stable release by default and keep navigation coherent across platform/module/version/language switches while preserving target-specific and release-specific content.
