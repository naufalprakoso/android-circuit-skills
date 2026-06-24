# Testing

## Test Layers

1. Domain and use-case tests
2. Repository and data tests
3. Presenter tests
4. UI tests
5. Navigation and overlay tests
6. Networking tests
7. Integration tests
8. Snapshot tests when already supported
9. Accessibility tests

## Presenter Tests

Use current Circuit testing APIs supported by the installed version, such as `Presenter.test()`, `presenterTestOf()`, `FakeNavigator`, `TestEventSink`, and SubPresenter utilities when available.

Test initial state, loading to content, loading to error, empty state, event handling, retry, navigation, pop or result behavior, duplicate events, cancellation, refresh, mutation success/failure, and retained-state behavior where important.

Prefer fakes over mocking Circuit core components. Do not compare whole state objects blindly when event-sink lambda equality makes that misleading. Assert meaningful properties and behavior. Invoke events from the current emitted state when the architecture requires it.

## UI Tests

Construct `UiState` directly and test content rendering, loading, empty, error, event emission, semantics, enabled/disabled states, long text, scroll behavior, accessibility labels, touch actions, and responsive variants. Use `TestEventSink` when appropriate.

## Networking Tests

Use the project's established tool: MockWebServer, Ktor MockEngine, fake transport, contract fixture, or equivalent.

Cover success, serialization failure, timeout, offline behavior, authentication refresh, rate limiting, retry boundaries, cancellation, redaction, and pagination.

## Coroutine Tests

- Use virtual time.
- Avoid real delays.
- Verify cancellation.
- Verify latest-query behavior.
- Verify retries terminate.
- Verify duplicate operations do not race.

## Snapshot Tests

Use snapshot tests for visual regression when the project already supports them. Do not introduce snapshot infrastructure for a small change without justification.

## Validation Commands

Discover commands from the repository. Prefer narrow module tasks first, such as a single presenter test or module compile, then run broader checks if the change touches shared contracts. Do not invent Gradle paths; inspect `settings.gradle`, included builds, convention plugins, and CI scripts.
