# Architecture Plan: Daily Expense Tracker (Jetpack Compose & Room)

## 1. Architecture Overview
* **Pattern:** MVVM (Model-View-ViewModel) with Unidirectional Data Flow (UDF).
* **Layers:** UI (Jetpack Compose) -> ViewModel -> Repository -> Data Source (Room DB).
* **Tech Stack:** Jetpack Compose, Room, Kotlin Coroutines, Flow, Hilt (Dependency Injection), Material 3.

---

## 2. Screen & Navigation
**Single Activity Architecture (`MainActivity`) with Jetpack Navigation.**

1. **Home Screen (`Dashboard`)**
   * **Components:** Total balance card, recent transactions list, quick "Add Expense" FAB.
   * **State:** Total spent today/month, list of recent items.
2. **Add/Edit Transaction Screen (`AddEditExpense`)**
   * **Components:** Amount input, Category dropdown (Food, Transport, Bills, etc.), Date picker, Type toggle (Income/Expense), Save button.
3. **Analytics/Reports Screen (`Stats`)**
   * **Components:** Monthly summary, category-wise breakdown (simple lists or charts).
4. **Settings Screen (`Settings`)**
   * **Components:** Currency selection, Dark/Light theme toggle, Data backup/export.

---

## 3. Data Layer (Room Database)

### Entities
* **`TransactionEntity`**
  * `id`: Long (Primary Key, Auto-generate)
  * `title`: String
  * `amount`: Double
  * `type`: Enum (`INCOME`, `EXPENSE`)
  * `category`: String
  * `date`: Long (Timestamp)

### DAO
* **`TransactionDao`**
  * `getAllTransactions(): Flow<List<TransactionEntity>>`
  * `getTransactionById(id: Long): TransactionEntity`
  * `insertTransaction(transaction: TransactionEntity)`
  * `deleteTransaction(transaction: TransactionEntity)`
  * `getTotalBalance(): Flow<Double?>`

### Repository
* **`TransactionRepository`**
  * Abstracts DAO operations and exposes `Flow` streams to the ViewModel.

---

## 4. State Management

* **UI State (`UiState`):** Immutable data classes representing the screen state.
  ```kotlin
  data class HomeUiState(
      val transactions: List<Transaction> = emptyList(),
      val totalBalance: Double = 0.0,
      val totalExpense: Double = 0.0,
      val isLoading: Boolean = false
  )
  ```
* **ViewModel:**
  * Uses `StateFlow` to expose UI state.
  * Handles user intents (e.g., `onAddTransaction`, `onDeleteTransaction`).
  * Executes database operations using `viewModelScope.launch`.

---

## 5. Permissions & Device Features
* **Permissions:** None strictly required for local storage. (Optional: `WRITE_EXTERNAL_STORAGE` or Scoped Storage if implementing CSV export feature).
* **Localization:** Full RTL (Right-to-Left) support for Arabic language out-of-the-box using Material 3 and Compose layout directions. 