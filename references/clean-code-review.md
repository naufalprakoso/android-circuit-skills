# Clean Code and Review

## Clean Code

- Use clear `Screen`, `State`, `Event`, `Presenter`, and `Ui` names.
- Keep files, modules, and public APIs cohesive.
- Prefer constructor injection.
- Expose immutable data.
- Handle errors explicitly.
- Avoid hidden global mutable state.
- Avoid duplicated business rules in multiple presenters.
- Avoid repository calls from composables.
- Avoid network response types in UI state.
- Avoid generic `Any`, untyped maps, and stringly typed navigation when typed alternatives exist.
- Write comments that explain why, not what the code already says.
- Avoid premature abstraction.
- Avoid speculative use cases or interfaces with one trivial implementation unless they create a meaningful boundary.
- Avoid broad rewrites unrelated to the user request.

## Circuit-Specific Smells

- Presenter emits UI.
- Ui accesses repository, database, or network client.
- Screen carries excessive mutable data.
- Presenter is giant and unrelated concerns are interleaved.
- State uses unrelated booleans for mutually exclusive modes.
- Event sink mixes unrelated domains.
- Retention choice is incorrect.
- `Navigator`, `Context`, or views are stored in retained state.
- Coroutine scope is unbounded.
- Request repeats on recomposition.
- `SharedFlow` is used unnecessarily for UI-to-presenter events.
- State contains mutable collections.
- Raw exception is shown to users.
- Navigation triggers more than once.
- Cancellation is missing.
- Pagination can issue duplicate requests.
- Experimental API is used without acknowledgement.
- Android type leaks into `commonMain`.

## Review Approach

Start with evidence. Prioritize correctness, lifecycle, cancellation, security, data flow, accessibility, and user-visible impact over style. Trace data from source to repository/use case to presenter to state to UI. Provide concrete remediation and suggested verification.

Classify each item as defect, risk, opportunity, or style preference. Do not report style preferences as correctness defects.
