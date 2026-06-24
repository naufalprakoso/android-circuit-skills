# Cross-Platform

## Shared Boundaries

Prefer common code for screen contracts when supported, Circuit state and events, presenters, state producers, use cases, domain models, repository interfaces, shared networking, serialization, business rules, and common tests.

Keep platform code for Android `Context` APIs, iOS frameworks, notifications, secure storage implementations, permissions, lifecycle integrations, platform navigation bridges, OS background work, and platform-specific UI behavior.

## Interfaces Versus Expect/Actual

Prefer ordinary Kotlin interfaces and dependency injection when multiple implementations may exist, tests need fakes, the implementation has meaningful behavior, or the dependency can be supplied at a platform entry point.

Use `expect`/`actual` when the abstraction is small, platform-defined, direct, and simpler than a DI boundary. Do not create `expect`/`actual` classes automatically when a regular interface is sufficient.

## Platform-Safe State

Do not expose Android types in common Circuit state. Avoid `Context`, `Activity`, `Fragment`, Android `Uri`, `Bitmap`, and Parcelable-only assumptions in common business state. Use common representations such as strings, byte arrays, value classes, or project-specific common types.

## Compose Multiplatform

- Decide whether UI is shared Compose Multiplatform or native per platform.
- Account for platform-specific accessibility behavior.
- Account for input differences: touch, mouse, keyboard, focus, and pointer behavior.
- Account for window size, back behavior, resource handling, and preview limitations.
- Test per supported UI platform; Android-only tests do not prove platform parity.

## Cross-Platform Networking

Respect the current client choice. In Ktor projects, keep engines platform-specific while request interfaces, serialization, and mapping may remain shared.

## Testing

- Put shared business, presenter, and mapping tests in `commonTest` where practical.
- Add platform tests for platform implementations.
- Add UI tests for each supported UI platform when behavior differs.
- Do not claim iOS, desktop, or web parity based only on Android execution.
