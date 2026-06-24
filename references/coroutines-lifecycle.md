# Coroutines and Lifecycle

## Structured Concurrency

- Do not use `GlobalScope`.
- Do not create arbitrary unmanaged `CoroutineScope` instances.
- Do not use `runBlocking` in production UI or presenter code.
- Tie work to an explicit owner.
- Allow cancellation to propagate.
- Do not swallow `CancellationException`.
- Use supervisor behavior only when sibling failure isolation is intentional and documented.

## Dispatcher Policy

- Make suspend functions main-safe.
- Move blocking operations to an appropriate dispatcher in the data layer or platform boundary.
- Inject dispatchers when tests or platform boundaries benefit.
- Avoid scattering `withContext(Dispatchers.IO)` throughout presentation code.
- Let repository/data APIs own blocking concerns.

## Circuit Presenter Work

Choose the owner based on lifetime:

- Use `LaunchedEffect` for composition-driven work keyed to screen inputs.
- Use a remembered composition scope for event-triggered suspend work that should cancel with the presenter.
- Use retained-state producers available in the installed Circuit version for observable screen state.
- Use repositories for shared long-running streams and caching.
- Use WorkManager or another durable scheduler for work that must survive UI lifetime.

Long-running or durable operations must not depend solely on a presenter remaining composed.

## Flow

- Distinguish cold flows from hot shared state.
- Avoid duplicate collection of expensive upstream flows.
- Use `stateIn` or `shareIn` in the appropriate owner.
- Use `distinctUntilChanged` only where equality reflects meaningful UI changes.
- Use `debounce` for user input and `flatMapLatest` for replacing outdated searches.
- Use `combine` for derived presentation state.
- Place `catch` where it handles the intended upstream failures and preserves cancellation.
- Use retry operators with bounded and typed policies.
- Avoid turning every value into a global `StateFlow`.

## Event Handling

- Prevent duplicate submissions when operations are not idempotent.
- Disable, serialize, or conflate actions intentionally.
- Read latest state when launching event work.
- Handle races between refresh and mutation.
- For optimistic updates, define rollback and reconciliation.
- Navigate after successful completion when correctness requires it.

## Testing Coroutines

- Use `runTest`.
- Use `TestDispatcher` and the test scheduler where needed.
- Use virtual time for debounce, delay, retry, and timeout.
- Verify cancellation paths.
- Avoid real delays.
- Avoid reliance on `Dispatchers.Main` in local tests unless explicitly configured.
