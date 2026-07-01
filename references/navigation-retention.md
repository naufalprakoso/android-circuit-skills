# Navigation and Retention

Use this reference when work touches Circuit navigation, `NavStack`, root behavior, results, overlays, nested navigation, retained state, or record lifecycle. Check the installed Circuit version first because navigation and retained-state APIs have changed across releases.

## Version Checks

- Search for `circuit-foundation`, `circuit-runtime-navigation`, `circuitx-navigation`, `circuit-overlay`, `circuitx-overlay`, and `circuit-retained`.
- Check whether the project uses `SaveableNavStack`, legacy `SaveableBackStack`, custom back stacks, or a wrapper navigator.
- Check whether the project uses forward/backward navigation, navigation interception, gesture navigation, or custom `NavDecoration`.
- Check retained APIs in use: `rememberRetained`, `rememberRetainedSaveable`, `produceRetainedState`, or project wrappers.
- Check if tests already use `FakeNavigator`, `Presenter.test`, or navigation integration tests.

## NavStack and Navigator

- Treat `Screen` as a typed destination and `NavStack` as the navigation state holder.
- Use `Navigator` for `goTo`, `pop`, root reset, and version-supported forward/backward operations.
- Keep root-pop behavior explicit through the project's `rememberCircuitNavigator` setup.
- Ensure navigable stacks always have at least one root screen.
- Use typed screen inputs and minimal identifiers. Avoid passing large mutable objects through screens.
- For deep links, construct the intended stack explicitly instead of navigating through intermediate screens only to reach a destination.

## Results and Overlays

- Use `PopResult` when a navigated destination returns typed data to the previous screen.
- Use overlays for transient dialogs, sheets, and confirmations that conceptually sit over the current screen.
- Do not use a boolean alone for complex dialog flows with async work, nested decisions, or result data. Model a small modal state and typed events.
- Keep result handling in the presenter or parent owner. Low-level UI components should not decide cross-feature navigation.
- Test both result delivery and no-result/back behavior when the result affects correctness.

## Nested Navigation

- If nested Circuit content emits `NavEvent`, forward it to the parent event sink and let the parent presenter decide how to apply it.
- For reusable embedded components that should not own navigation, consider SubCircuit and delegate cross-cutting actions through `outerEventSink`.
- Do not use SubCircuit just to split a small composable or avoid passing a callback. Use it when there is a real nested presenter/UI contract.

## Retention Decision

| API | Lifetime | Use |
| --- | --- | --- |
| `remember` | recomposition only | temporary calculation or purely local interaction state |
| `rememberRetained` | recomposition, back stack, configuration changes | recoverable screen state that does not need process-death restore |
| `rememberSaveable` | recomposition, back stack, configuration changes, process death | minimal saveable state with a valid saver |
| `rememberRetainedSaveable` or project equivalent | version-dependent | state such as scroll position when the project supports it |

Do not retain or save `Context`, `Activity`, `Fragment`, `View`, `Navigator`, platform handles, closeable resources, or coroutine scopes without explicit ownership. Save minimal IDs or primitive state and reconstruct the rest from repositories or use cases.

## Record Lifecycle

- Check whether the installed Circuit version pauses records that are not currently active.
- Do not assume all back-stack presenters are actively collecting flows.
- For background work that must continue off-screen, move ownership to a repository, use case, service, or platform scheduler.
- For analytics or one-shot effects, verify behavior across navigation away, back navigation, configuration change, and process death where relevant.

## Review Checklist

Trace navigation and retention with this path:

```text
Ui event -> Presenter branch -> Navigator or overlay/result action -> stack/result state -> restored state -> test evidence
```

Flag defects when navigation can fire repeatedly, result callbacks are registered with unstable keys, retained state stores leak-prone objects, root behavior is implicit, off-screen records keep expensive work alive unintentionally, or process-death restore is claimed without saveable state.
