# Codegen and Dependency Injection

Use this reference before adding or changing `@CircuitInject`, `@SubCircuitInject`, generated factories, KSP args, assisted injection, or DI wiring. Inspect the target project first; do not migrate DI frameworks incidentally.

## Detect Existing Mode

Search Gradle and convention plugins for:

- `com.slack.circuit:circuit-codegen`
- `com.slack.circuit:circuit-codegen-annotations`
- `com.slack.circuit:circuitx-subcircuit-codegen`
- `circuit.codegen.mode`
- `circuit.codegen.lenient`
- `subcircuit.codegen.mode`
- `anvil-ksp-extraContributingAnnotations`
- `kotlin-inject-anvil-contributing-annotations`

Then search source for `@CircuitInject`, `@SubCircuitInject`, `Presenter.Factory`, `Ui.Factory`, `AssistedInject`, `AssistedFactory`, `@Inject`, `@ContributesMultibinding`, `@ContributesIntoSet`, Hilt modules, kotlin-inject components, and Metro annotations.

## Mode Matrix

| Mode | Signals | Notes |
| --- | --- | --- |
| Manual factories | handwritten `Presenter.Factory` and `Ui.Factory` | Prefer when the project already avoids codegen or needs custom matching logic |
| Anvil or Anvil KSP | default codegen mode, Anvil multibindings | `@CircuitInject` generates contributed factories; Anvil KSP needs extra contributing annotation arg |
| Hilt | `circuit.codegen.mode=hilt` | Match existing Hilt components and scopes |
| kotlin-inject-anvil | `circuit.codegen.mode=kotlin_inject_anvil` | Assisted injection shape differs from Dagger; may need contributing annotation arg |
| Metro | `circuit.codegen.mode=metro` | Check Metro function-provider requirements and current Circuit changelog |
| SubCircuit Anvil | `circuitx-subcircuit-codegen` default | Experimental; requires SubCircuit artifacts and composition provisioning |
| SubCircuit Metro | `subcircuit.codegen.mode=metro` | Check latest source or changelog because fixes may be unreleased |

## `@CircuitInject` Rules

- Annotate the class or function that should generate a `Presenter.Factory` or `Ui.Factory`.
- Class-based presenters and UIs must be injectable according to the selected DI framework.
- Function-based presenters must be `@Composable`, return `CircuitUiState`, and end with `Presenter`.
- Function-based UIs must be `@Composable`, return `Unit`, and accept state when needed.
- Circuit-provided parameters include screen, navigator, and Circuit context APIs supported by the installed version.
- Non-Circuit function parameters are treated as injected dependencies in current codegen; check version before relying on provider-hoisting behavior.
- Do not add codegen annotations without verifying the generated factories are collected by the app graph.

## Assisted Injection

- Use assisted injection when the presenter needs the specific `Screen`, `Navigator`, or Circuit context at factory creation time.
- In Dagger/Hilt/Anvil, the `@AssistedFactory` interface is commonly annotated with `@CircuitInject` for assisted class presenters.
- In kotlin-inject mode, inspect local patterns because assisted construction uses different annotations and generated factory behavior.
- Keep screen casts narrow and type-safe. Prefer typed factory signatures where local patterns support them.

## SubCircuit Wiring

- Use SubCircuit for nested reusable presenter/UI pairs that delegate parent-owned events through `outerEventSink`.
- Do not use SubCircuit for ordinary composable extraction.
- Add `circuitx-subcircuit`, codegen, and test artifacts only when the project already uses SubCircuit or the feature clearly needs it.
- Ensure `SubCircuit` is built from generated `SubPresenterFactory` and `SubUiFactory` sets and provided through `LocalSubCircuit`.
- Mark experimental API use explicitly in code comments or implementation notes when the project policy requires it.

## Verification

- Run the narrowest compile task for the affected module.
- Run KSP/codegen tasks if they are separate in the project.
- Add or update presenter tests to prove generated factory wiring is not just compiling but behaviorally correct.
- For DI graph changes, run the smallest graph/component compile target available.
- For SubCircuit, add a SubPresenter test and one parent delegation test when feasible.
