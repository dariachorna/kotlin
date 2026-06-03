# Practice 2 — Online Store: Shopping Cart

## Context

You are building the core domain model of a simplified online store. The system handles product pricing, a shopping cart, order status tracking, and a stock quantity validator. Your task is to replace every `TODO()` with a correct implementation.

## Getting started

**Prerequisites:** JDK 21 and an internet connection (Gradle downloads dependencies automatically).

Clone the repository and open it in IntelliJ IDEA. The IDE will import the Gradle project automatically. Alternatively, build from the terminal:

```bash
./gradlew build
```

## Your task

There are four files to implement, each covering specific OOP concepts from the lecture.

Do not modify the test files or any build configuration.

## Files to implement

### `Catalog.kt` — inline value class, enum class, data class

#### `Price`

An inline value class wrapping a `Long` that represents a price in cents (e.g. `Price(2999)` = $29.99).

| Function | Behaviour |
|---|---|
| `plus(other)` | Returns the sum of two prices |
| `minus(other)` | Returns the difference of two prices |
| `times(multiplier: Double)` | Scales the price; round to the nearest cent using `(cents * multiplier).roundToLong()` from `kotlin.math` |
| `compareTo(other)` | Standard comparison by cents value |
| `format()` | Returns a string like `"$29.99"` — always two decimal places |

#### `Category`

An enum class. Each constant already has `displayName` and `taxRate` provided.

| Function | Behaviour |
|---|---|
| `applyTax(price)` | Returns `price + price * taxRate` |

#### `Product`

A data class with fields `id`, `name`, `price`, and `category`. The auto-generated `equals`, `hashCode`, and `copy` are provided for free.

| Function | Behaviour |
|---|---|
| `companion object.fromCsv(line)` | Parses a CSV line in the format `"P001,Kotlin in Action,2999,BOOKS"` and returns a `Product` |

---

### `Cart.kt` — operator overloading, extension functions, infix functions, lazy delegation

#### `Cart`

| What to implement | Behaviour |
|---|---|
| `plusAssign(product)` | Adds the product to the internal list (`cart += product`) |
| `get(index)` | Returns the product at the given index (`cart[0]`) |
| `contains(product)` | Returns `true` if the product is in the cart (`product in cart`) |

`checkout()` is already implemented — it creates an `OrderSummary` snapshot of the cart contents.

#### `infix fun Price.discountedBy(percent: Int): Price`

Returns the price reduced by the given percentage. `Price(1000) discountedBy 10` → `Price(900)`.

#### `fun Cart.applyDiscount(percent: Int): Price`

Returns the cart's total price after applying the discount. Use `discountedBy` internally.

#### `OrderSummary`

Holds a snapshot of the cart items. Use `lazy` for `total` and `receipt`.

| What to implement | Behaviour |
|---|---|
| `total` | Sum of all item prices. Must be implemented with `by lazy { }` |
| `receipt` | A formatted multi-line string (see format below). Must be implemented with `by lazy { }` |
| `component1()` | The total price |
| `component2()` | The number of items |
| `component3()` | The most expensive product (or `null` if empty) |

**Receipt format:**
```
=== Order Summary ===
1. Kotlin in Action — $29.99
2. Clean Code — $34.99
--------------------
Total: $64.98
Items: 2
```

---

### `OrderStatus.kt` — sealed classes, extension functions

The sealed class hierarchy is already defined. Implement the two extension functions.

| Function | Behaviour |
|---|---|
| `isFinal()` | Returns `true` for `Delivered` and `Cancelled`, `false` for all others |
| `displayMessage()` | Returns a human-readable message for each status (see below) |

**Expected messages:**

| Status | Message |
|---|---|
| `Pending` | `"Your order is pending confirmation"` |
| `Processing(3)` | `"Your order is being processed. Estimated delivery: 3 days"` |
| `Shipped("TRACK123")` | `"Your order has been shipped. Tracking number: TRACK123"` |
| `Delivered` | `"Your order has been delivered. Thank you!"` |
| `Cancelled("out of stock")` | `"Your order was cancelled. Reason: out of stock"` |

Use a `when` expression — the compiler will warn you if a case is missing.

---

### `QuantityDelegate.kt` — custom property delegate

Implement `PositiveQuantity`, a `ReadWriteProperty<Any?, Int>` that:
- Throws `IllegalArgumentException` if the initial value is `≤ 0`
- Throws `IllegalArgumentException` if an updated value is `≤ 0`
- Leaves the stored value unchanged if a setter call throws

Example usage:
```kotlin
class StockItem(quantity: Int) {
    var quantity: Int by PositiveQuantity(quantity)
}
```

---

## Running the tests

```bash
./gradlew test
```

Before you implement anything, almost all tests fail — that is expected. Your goal is to make **all 51 tests pass**.

```
> Task :test FAILED
51 tests completed, 50 failed   ← expected starting state
```

```
> Task :test
BUILD SUCCESSFUL                ← target state
```

You can also run tests from inside IntelliJ IDEA by clicking the green arrow next to the test class or individual test method.

## Rules

- Implement your solutions only inside the four source files.
- Do not modify the test files or any build configuration.
- Only language features from Lecture 2 are needed.
