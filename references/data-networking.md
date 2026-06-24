# Data and Networking

## Layer Responsibilities

- Network client handles transport.
- Data source handles provider-specific integration when useful.
- Repository exposes domain-oriented data access and source-of-truth policy.
- Use case coordinates business operations when the operation is shared, complex, or transactional.
- Presenter maps domain results into UI state and handles user events.
- Ui renders state and emits events only.

## Models

Separate models when the boundary earns it:

- Network DTO for API shape and serialization.
- Persistence entity for database shape.
- Domain model for business meaning.
- Presentation model for render-ready state.

Do not require four models for every trivial feature. Separate when nullability, naming, lifecycle, security, formatting, caching, or API churn would leak across layers. Never expose raw HTTP responses, database cursors, transport exceptions, authentication tokens, or mutable network models directly in Circuit UI state.

## Source of Truth

Make the data strategy explicit:

- Network-only: define load, refresh, retry, and empty/error behavior.
- Cache-backed: define freshness, invalidation, stale display, and background refresh.
- Local-first or offline-first: define optimistic updates, conflict resolution, reconciliation, and failure rollback.

The presenter should not have to guess whether repository data is authoritative, stale, or speculative.

## Loading and Error State

Model initial load, refresh, pagination, empty data, recoverable error, terminal error, partial content with error, retry, and duplicate-request prevention. Avoid reducing all asynchronous work to one `isLoading` boolean when multiple operations can overlap.

## Pagination

- Use stable page keys, cursors, or offsets according to the API.
- Prevent concurrent duplicate loads.
- Cancel or ignore outdated loads when the query changes.
- Preserve already loaded content during recoverable errors.
- Model end-of-list and retry behavior.
- Invalidate pages correctly on refresh.

## Library Neutrality

Respect the project's existing networking stack: Retrofit/OkHttp, Ktor Client, Apollo, custom clients, or another established implementation. Do not migrate libraries unless the user explicitly requests it or the current library cannot satisfy the requirement.
