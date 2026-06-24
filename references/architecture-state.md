# Architecture and State

## Screen Boundaries

- Use `Screen` as the identity and input for a navigable feature.
- Keep screen inputs minimal and serializable according to the project's navigation setup.
- Pass stable identifiers rather than large mutable domain objects where practical.
- Avoid using `Screen` as a service locator or arbitrary state container.
- Use `StaticScreen` only when the UI truly needs no presenter and the installed Circuit version supports it.

## Presenter Responsibilities

- Compute render-ready `CircuitUiState`.
- Translate repositories and use cases into presentation state.
- Handle typed `CircuitUiEvent` values.
- Coordinate navigation and overlays when the feature owns that decision.
- Do not emit Compose UI.
- Do not directly depend on Android views.
- Do not become a repository, networking client, or database facade.
- Do not wrap every Circuit presenter in a ViewModel by default; use a ViewModel only for a documented integration or lifecycle requirement.

## UI Responsibilities

- Render `CircuitUiState`.
- Emit typed events through the state event sink.
- Avoid direct calls to repositories, network clients, databases, and navigation side effects that belong to the presenter.
- Keep top-level composables stateless, previewable, and testable with constructed state.
- Keep UI-local ephemeral state only when it is purely visual and not business state.

## States and Events

- State types implement `CircuitUiState`; event types implement `CircuitUiEvent`.
- Prefer sealed state variants for loading, content, empty, error, and other mutually exclusive modes.
- Avoid unrelated boolean flags for mutually exclusive screen states.
- Keep state render-ready and free of raw DTOs, cursors, HTTP responses, exceptions, secrets, and mutable transport models.
- Use presentation/domain models at UI boundaries.
- Use event sinks for ordinary UI-to-presenter events instead of ViewModel-style one-off `SharedFlow` plumbing.
- Account for state equality when an event-sink lambda is included; tests should assert meaningful fields and behavior.
- Apply `@Stable` and `@Immutable` only when the type satisfies the annotation contract.

## State Ownership and Retention

Choose retention deliberately:

| API | Lifetime | Use |
| --- | --- | --- |
| `remember` | recomposition only | transient calculated or interaction state |
| `rememberRetained` | recomposition, back stack, configuration changes | recoverable screen state that need not survive process death |
| `rememberSaveable` | recomposition, back stack, configuration changes, process death | minimal saveable state with primitive, parcelable, serializable, or saver support |
| retained-saveable combinations | version-dependent | UI state such as scroll position when supported and justified |

Never retain `Context`, `Activity`, `Fragment`, `Navigator`, view objects, coroutine scopes without clear ownership, or platform handles that can leak. Save only minimal recoverable state; derive the rest from repositories or use cases.

## Presenter Scaling

Use the lightest extraction that solves real complexity:

- Extract private composable helpers inside a presenter for observation or state derivation.
- Use non-composable helper functions for event handling and mapping.
- Use composite presenters when child areas are independently meaningful and testable.
- Use StateProducer-style classes for reusable state logic that is not a standalone screen.
- Use use cases/interactors when business operations are shared, complex, or need isolated tests.
- Use SubCircuit when an embedded reusable component needs presenter/UI pairing but delegates cross-cutting events to the parent.

Avoid overengineering for a small presenter. Watch for giant presenters, event-sink explosion, boolean state soup, event-handler spaghetti, repeated repository coordination, data-layer work in presenters, and state domains with unrelated responsibilities.

## Navigation and Overlays

- Use typed `Screen` destinations and the project's current navigation stack API.
- Keep navigation decisions in presenters or parent coordinators, not low-level UI components.
- Use `rememberAnsweringNavigator` and `PopResult` only when the installed version supports the desired result behavior.
- Build deep-link stacks explicitly and preserve root behavior.
- Use overlays for dialogs and sheets when they are transient UI over the current screen; use screen results when the flow is a navigable destination with data returned to the previous screen.
- For nested components, prefer parent delegation or SubCircuit where low-level components should not own navigation.

## Factories and Dependency Injection

- Respect the project's current DI framework and Circuit wiring strategy.
- Support manual factories, `Presenter.Factory`, `Ui.Factory`, `@CircuitInject`, KSP codegen, and assisted injection according to installed Circuit support.
- Do not migrate DI frameworks as an incidental change.
- For codegen, inspect mode-specific requirements and generated factory patterns before adding annotations.
