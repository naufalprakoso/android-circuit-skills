# Compose UI and Performance

## Contents

- [UI API Design](#ui-api-design)
- [State Hoisting](#state-hoisting)
- [Recomposition and Stability](#recomposition-and-stability)
- [Lazy Layouts](#lazy-layouts)
- [Effects](#effects)
- [Adaptive Layouts](#adaptive-layouts)
- [Performance Investigation](#performance-investigation)
- [Preview and Visual Quality](#preview-and-visual-quality)

## UI API Design

- Prefer state-first composable APIs: `fun Feature(state: FeatureState, modifier: Modifier = Modifier)`.
- Keep `modifier` as the first optional parameter with default `Modifier`.
- Apply the passed `modifier` to the root layout node exactly once.
- Use event callbacks or typed Circuit events instead of passing mutable state holders into UI.
- Keep screen composables stateless and previewable; hoist business state to presenters.
- Provide a stateful convenience wrapper only for reusable components with simple UI element state. Keep screen-level Circuit UIs stateless.
- Use slot APIs for variable child content instead of boolean flags or many optional composable parameters.
- Keep parameters stable and meaningful. Avoid passing a large object when the component only needs a small render model.
- Reuse Material or the project's design system before building custom components.
- Prefer built-in Compose, Material, or design-system components because they usually include semantics, focus, interaction, and minimum-size behavior.
- Create small components around real UI boundaries, not arbitrary visual fragments.
- Support adaptive layouts when the feature needs multiple sizes, foldables, desktop, or tablets.
- Do not expose internal layout implementation details through public composable parameters unless callers need that control.

## State Hoisting

- Hoist state to the lowest common owner that reads and writes it.
- Keep screen UI state in the presenter or screen state holder when business rules, repositories, navigation, or cross-component coordination are involved.
- Keep UI element state in the composable when it is local, simple, and only affects that element.
- Use `rememberSaveable` only for small transient UI element state that should survive recreation, such as text input, selected tab, scroll key, or expanded item ID.
- Do not save large objects, DTOs, lists, or domain graphs in saveable state. Save IDs or keys and restore larger data from the data layer.
- Use plain state holder classes only when UI logic is complex enough to justify extraction and the project already has a compatible pattern.
- In Circuit, prefer typed events from UI to presenter over composables directly mutating business state.

## Recomposition and Stability

- Move expensive computation out of composition or memoize it with correct keys.
- Use `remember` for calculation or object retention that actually needs composition memory.
- Use `derivedStateOf` only when it prevents meaningful unnecessary updates.
- Key `remember` with every value that affects the calculated result.
- Avoid backwards state writes and state writes during composition.
- Defer rapidly changing state reads when appropriate.
- Use immutable or stable public state models when they are truthful.
- Prefer immutable collection exposure when it gives real stability or API safety, but do not blindly copy large lists on every presenter emission.
- Avoid passing mutable collections, mutable DTOs, or objects with unstable equality into frequently recomposed UI.
- Avoid allocating new lambdas, formatters, painters, or modifier chains in hot item composition when they can be stable, remembered, or moved out.
- Treat `@Stable` and `@Immutable` as contracts. Do not add them to silence compiler reports unless the type satisfies the contract.
- Check Compose compiler stability reports before broad stability work, then fix the smallest unstable boundary that affects measured behavior.
- Use Compose compiler reports, tracing, benchmarks, or profiling before broad stability refactors.
- Do not claim performance wins without measurement or a concrete eliminated defect.

## Lazy Layouts

- Provide stable item keys for data with identity.
- Use content types when item layout classes differ meaningfully.
- Avoid index-only identity for reorderable or mutable lists.
- Keep heavy item computation out of item composition.
- Preserve item-local state intentionally with stable keys.
- Model paging, loading rows, retry rows, end-of-list, and empty state explicitly.
- Keep item lambdas small and delegate row rendering to focused composables.
- Use `remember` inside items only when the key is stable and the retained value belongs to that item identity.
- Do not mutate backing lists during composition. Emit new list state from the owner.
- Use lazy layouts only for genuinely scrollable or large content. For small fixed content, simple `Column` or `Row` is easier and cheaper.

## Effects

- Use `LaunchedEffect` for suspend work tied to composition keys.
- Use `DisposableEffect` for subscriptions that need cleanup.
- Use `SideEffect` for publishing committed state to non-Compose objects.
- Use `rememberUpdatedState` when an effect must use the latest lambda or value without restarting.
- Use `snapshotFlow` to convert Compose state reads into Flow streams.
- Use `produceState` or Circuit retained producers when an async source should produce state for rendering.
- Use `rememberCoroutineScope` for event-triggered work owned by the current composition.

Choose keys carefully. Wrong keys cause repeated requests, stale work, or missed cancellation. Do not use effects to hide unclear state ownership.

Treat `LaunchedEffect(Unit)` and `LaunchedEffect(true)` as suspicious. They are valid only for work that should start once for that call site and cancel when the call site leaves composition.

## Adaptive Layouts

- Design for window size and posture, not device names.
- Use the project's adaptive layout primitives or Compose Material 3 Adaptive when available.
- Prefer layout replacement over simply stretching phone UI on wide screens.
- Validate compact, medium, and expanded widths when the feature can appear on tablets, foldables, desktop, ChromeOS, or resizable windows.
- Account for mouse, keyboard, focus, hover, and pointer interactions on non-phone targets.
- Avoid hardcoded dimensions that break with font scale, density changes, split screen, or fold posture.
- Keep navigation and pane behavior explicit: single-pane, list-detail, supporting pane, modal, or overlay.

## Performance Investigation

1. Reproduce the issue.
2. Measure in a release or profileable build when relevant.
3. Inspect recomposition, frame timing, allocations, startup, and network/data work.
4. Inspect Compose compiler metrics or stability reports when recomposition is suspected.
5. Use Layout Inspector, system traces, recomposition counters, or app-specific logging to locate the hotspot.
6. Use Macrobenchmark or Baseline Profiles for startup, scrolling, animation, or whole-flow performance only when the scope justifies it.
7. Make one targeted change and remeasure.

Do not rewrite state models, add stability annotations, or introduce caching before identifying the unstable parameter, repeated read, expensive composition, or layout hotspot.

## Preview and Visual Quality

Preview meaningful variants: loading, empty, error, content, long text, large font, RTL where applicable, narrow layouts, wide layouts, and dark theme when supported. For Circuit UIs, construct state directly and use a recording event sink.

Use previews to expose layout and text problems, not as a substitute for tests. Add screenshot or visual tests only when the project already supports them or the UI risk justifies the maintenance cost.
