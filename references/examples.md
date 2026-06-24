# Examples

## Table of Contents

- [Basic Screen, State, Event, Presenter, and Ui](#basic-screen-state-event-presenter-and-ui)
- [Sealed Loading, Content, and Error State](#sealed-loading-content-and-error-state)
- [Repository-Backed Presenter](#repository-backed-presenter)
- [Retained Flow Observation](#retained-flow-observation)
- [Event Suspend Work](#event-suspend-work)
- [Navigator Usage](#navigator-usage)
- [Pop Result](#pop-result)
- [Overlay Handling](#overlay-handling)
- [Presenter Decomposition](#presenter-decomposition)
- [State Producer](#state-producer)
- [SubCircuit](#subcircuit)
- [StaticScreen](#staticscreen)
- [Presenter Test](#presenter-test)
- [Ui Test](#ui-test)
- [Coroutine Retry Test](#coroutine-retry-test)
- [KMP Boundary](#kmp-boundary)
- [Before and After](#before-and-after)

Use the target project's resolved Circuit version and imports. These examples show shape, not dependency declarations.

## Basic Screen, State, Event, Presenter, and Ui

```kotlin
data class ProductDetailsScreen(val productId: String) : Screen {
  data class State(
    val title: String,
    val eventSink: (Event) -> Unit,
  ) : CircuitUiState

  sealed interface Event : CircuitUiEvent {
    data object BackClicked : Event
  }
}

class ProductDetailsPresenter(
  private val screen: ProductDetailsScreen,
  private val navigator: Navigator,
) : Presenter<ProductDetailsScreen.State> {
  @Composable
  override fun present(): ProductDetailsScreen.State {
    return ProductDetailsScreen.State(
      title = "Product ${screen.productId}",
      eventSink = { event ->
        when (event) {
          ProductDetailsScreen.Event.BackClicked -> navigator.pop()
        }
      },
    )
  }
}

@Composable
fun ProductDetails(state: ProductDetailsScreen.State, modifier: Modifier = Modifier) {
  Column(modifier) {
    Text(state.title)
    Button(onClick = { state.eventSink(ProductDetailsScreen.Event.BackClicked) }) {
      Text("Back")
    }
  }
}
```

## Sealed Loading, Content, and Error State

```kotlin
sealed interface ProductState : CircuitUiState {
  data object Loading : ProductState
  data class Content(val product: ProductUiModel, val eventSink: (ProductEvent) -> Unit) : ProductState
  data class Error(val message: String, val eventSink: (ProductEvent) -> Unit) : ProductState
}
```

## Repository-Backed Presenter

```kotlin
class ProductPresenter(
  private val screen: ProductDetailsScreen,
  private val repository: ProductRepository,
) : Presenter<ProductState> {
  @Composable
  override fun present(): ProductState {
    var reloadKey by rememberRetained { mutableIntStateOf(0) }
    val loadState by produceRetainedState<ProductLoadState>(
      ProductLoadState.Loading,
      screen.productId,
      reloadKey,
    ) {
      value = repository.loadProduct(screen.productId).toProductLoadState()
    }

    return when (val state = loadState) {
      ProductLoadState.Loading -> ProductState.Loading
      is ProductLoadState.Loaded -> ProductState.Content(state.product.toUiModel()) { event ->
        when (event) {
          ProductEvent.Retry -> Unit
        }
      }
      is ProductLoadState.Failed -> ProductState.Error(state.userMessage) { event ->
        when (event) {
          ProductEvent.Retry -> reloadKey++
        }
      }
    }
  }
}
```

## Retained Flow Observation

```kotlin
val order by produceRetainedState<Order?>(null, screen.orderId) {
  repository.observeOrder(screen.orderId).collect { value = it }
}
```

Check installed Circuit APIs before choosing `produceRetainedState`, `rememberRetained`, or project-specific retained helpers.

## Event Suspend Work

```kotlin
var submitState by rememberRetained { mutableStateOf<SubmitState>(SubmitState.Idle) }
val scope = rememberCoroutineScope()

return CheckoutState(submitState) { event ->
  when (event) {
    CheckoutEvent.Submit -> {
      if (submitState != SubmitState.Submitting) {
        scope.launch {
          submitState = SubmitState.Submitting
          submitState = runCatching { submitOrder() }
            .fold(onSuccess = { SubmitState.Complete }, onFailure = { SubmitState.Failed })
        }
      }
    }
  }
}
```

## Navigator Usage

```kotlin
eventSink = { event ->
  when (event) {
    is ProductEvent.SellerClicked -> navigator.goTo(SellerScreen(event.sellerId))
    ProductEvent.BackClicked -> navigator.pop()
  }
}
```

At the root:

```kotlin
val navStack = rememberSaveableNavStack(root = HomeScreen)
val navigator = rememberCircuitNavigator(navStack, onRootPop = { finish() })
NavigableCircuitContent(navigator = navigator, backStack = navStack)
```

Use the target version's navigation API names.

## Pop Result

```kotlin
data object FilterScreen : Screen {
  data class Result(val query: String) : PopResult
}

val filterNavigator = rememberAnsweringNavigator<FilterScreen.Result>(navigator) { result ->
  query = result.query
}
```

## Overlay Handling

```kotlin
sealed interface DeleteState : CircuitUiState {
  data class Content(val showConfirm: Boolean, val eventSink: (DeleteEvent) -> Unit) : DeleteState
}
```

Use the installed overlay artifact and project conventions for dialog rendering. Prefer an overlay for transient confirmation over a navigated screen unless the flow should return a typed result.

## Presenter Decomposition

```kotlin
@Composable
override fun present(): OrderState {
  val order = observeOrder()
  val payment = observePayment()
  return buildState(order, payment)
}
```

Extract observation and mapping before introducing child presenters.

## State Producer

```kotlin
class AvailabilityStateProducer(private val repository: InventoryRepository) {
  @Composable
  fun produce(productId: String): AvailabilityState {
    val inventory by produceRetainedState<Inventory?>(null, productId) {
      value = repository.loadInventory(productId)
    }
    return inventory.toAvailabilityState()
  }
}
```

## SubCircuit

```kotlin
sealed interface PriceCardEvent {
  data class OpenTerms(val productId: String) : PriceCardEvent
}

data class PriceCardScreen(val productId: String) : SubScreen<PriceCardEvent>

data class PriceCardState(
  val price: String,
  val eventSink: (PriceCardUiEvent) -> Unit,
) : SubCircuitUiState

sealed interface PriceCardUiEvent {
  data object TermsClicked : PriceCardUiEvent
}

class PriceCardPresenter(
  private val screen: PriceCardScreen,
) : SubPresenter<PriceCardEvent, PriceCardState> {
  @Composable
  override fun present(outerEventSink: (PriceCardEvent) -> Unit): PriceCardState {
    return PriceCardState(price = "...") {
      outerEventSink(PriceCardEvent.OpenTerms(screen.productId))
    }
  }
}
```

Use SubCircuit only when the installed version includes it and the child component should delegate navigation or cross-cutting events to the parent.

## StaticScreen

```kotlin
data object AboutScreen : StaticScreen

@Composable
fun About(modifier: Modifier = Modifier) {
  Text("About", modifier)
}
```

Use only when no presenter-managed state is needed.

## Presenter Test

```kotlin
@Test
fun opensSeller() = runTest {
  val navigator = FakeNavigator(ProductDetailsScreen("p1"))
  val presenter = ProductPresenter(ProductDetailsScreen("p1"), navigator, repository = FakeProductRepository())

  presenter.test {
    val state = awaitItem() as ProductState.Content
    state.eventSink(ProductEvent.SellerClicked("s1"))
    assertThat(navigator.awaitNextScreen()).isEqualTo(SellerScreen("s1"))
  }
}
```

Use assertions supported by the project's test stack.

## Ui Test

```kotlin
@Test
fun retryClickEmitsEvent() {
  val sink = TestEventSink<ProductEvent>()
  composeRule.setContent {
    ProductError(message = "Offline", eventSink = sink)
  }
  composeRule.onNodeWithText("Retry").performClick()
  sink.assertEvent(ProductEvent.Retry)
}
```

## Coroutine Retry Test

```kotlin
@Test
fun retryStopsAfterBound() = runTest {
  val attempts = AtomicInteger(0)
  val result = runCatching {
    retryTransient(maxAttempts = 3) {
      attempts.incrementAndGet()
      throw TimeoutCancellationException("timeout")
    }
  }
  assertThat(attempts.get()).isEqualTo(3)
  assertThat(result.isFailure).isTrue()
}
```

## KMP Boundary

```kotlin
interface SecureTokenStore {
  suspend fun readToken(): String?
  suspend fun writeToken(token: String)
}

class AuthRepository(private val tokenStore: SecureTokenStore) {
  suspend fun currentToken(): String? = tokenStore.readToken()
}
```

Provide Android and iOS implementations at platform entry points. Prefer this over `expect`/`actual` when tests need fakes or behavior may vary.

## Before and After

Boolean soup to sealed state:

```kotlin
// Before
data class State(val isLoading: Boolean, val error: Throwable?, val data: Product?)

// After
sealed interface State : CircuitUiState {
  data object Loading : State
  data class Content(val product: ProductUiModel) : State
  data class Error(val message: String, val eventSink: (Event) -> Unit) : State
}
```

Repository call in Ui to presenter flow:

```kotlin
// Before: composable launches repository call directly.
// After: presenter observes repository and emits render-ready state.
```

`GlobalScope` to structured concurrency:

```kotlin
val scope = rememberCoroutineScope()
scope.launch { repository.submit() }
```

Raw DTO state to presentation model:

```kotlin
data class ProductUiModel(val title: String, val priceText: String)
```
