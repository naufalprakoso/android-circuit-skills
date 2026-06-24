# Compose UI and Performance

## UI API Design

- Prefer state-first composable APIs: `fun Feature(state: FeatureState, modifier: Modifier = Modifier)`.
- Keep `modifier` as the first optional parameter with default `Modifier`.
- Keep screen composables stateless and previewable; hoist business state to presenters.
- Reuse Material or the project's design system before building custom components.
- Create small components around real UI boundaries, not arbitrary visual fragments.
- Support adaptive layouts when the feature needs multiple sizes, foldables, desktop, or tablets.

## Recomposition and Stability

- Move expensive computation out of composition or memoize it with correct keys.
- Use `remember` for calculation or object retention that actually needs composition memory.
- Use `derivedStateOf` only when it prevents meaningful unnecessary updates.
- Avoid backwards state writes and state writes during composition.
- Defer rapidly changing state reads when appropriate.
- Use immutable or stable public state models when they are truthful.
- Prefer immutable collection exposure when it gives real stability or API safety, but do not blindly copy large lists on every presenter emission.
- Use Compose compiler reports, tracing, benchmarks, or profiling before broad stability refactors.
- Do not claim performance wins without measurement or a concrete eliminated defect.

## Lazy Layouts

- Provide stable item keys for data with identity.
- Use content types when item layout classes differ meaningfully.
- Avoid index-only identity for reorderable or mutable lists.
- Keep heavy item computation out of item composition.
- Preserve item-local state intentionally with stable keys.
- Model paging, loading rows, retry rows, end-of-list, and empty state explicitly.

## Effects

- Use `LaunchedEffect` for suspend work tied to composition keys.
- Use `DisposableEffect` for subscriptions that need cleanup.
- Use `SideEffect` for publishing committed state to non-Compose objects.
- Use `rememberUpdatedState` when an effect must use the latest lambda or value without restarting.
- Use `rememberCoroutineScope` for event-triggered work owned by the current composition.

Choose keys carefully. Wrong keys cause repeated requests, stale work, or missed cancellation. Do not use effects to hide unclear state ownership.

## Performance Investigation

1. Reproduce the issue.
2. Measure in a release or profileable build when relevant.
3. Inspect recomposition, frame timing, allocations, startup, and network/data work.
4. Use macrobenchmarks or baseline profiles only when justified by scope.
5. Make one targeted change and remeasure.

## Preview and Visual Quality

Preview meaningful variants: loading, empty, error, content, long text, large font, RTL where applicable, narrow layouts, wide layouts, and dark theme when supported. For Circuit UIs, construct state directly and use a recording event sink.
