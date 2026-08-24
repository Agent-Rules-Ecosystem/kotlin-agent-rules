# Guía de Estilo y Buenas Prácticas — Kotlin

Reglas de formateo, modismos de lenguaje y verificación estática mediante **ktlint** y **detekt** para proyectos Kotlin.

---

## 🎨 1. Convenciones de Nomenclatura y Idiomas de Kotlin

### Nomenclatura
* **Clases, Interfaces, Objetos y Composables**: `PascalCase` (`UserViewModel`, `HomeScreen`, `UserRepository`).
* **Funciones no Composable y Métodos**: `camelCase` (`getUserData()`, `calculateTotal()`).
* **Variables y Propiedades**: `camelCase` (`userId`, `isLoading`).
* **Constantes (`const val`)**: `SNAKE_CASE_UPPER` (`const val BASE_URL = "https://..."`).

### Idiomas Fundamentales de Kotlin
1. **Inmutabilidad por Defecto**:
   * Usar siempre `val` sobre `var`. Solo usar `var` en estados mutables locales estrictamente justificados.
   * Preferir colecciones inmutables (`listOf()`, `mapOf()`) sobre mutables (`mutableListOf()`).
2. **Null Safety**:
   * Evitar el operador de aserción nula `!!`. Usar el operador Elvis `?:`, `safe calls` (`?.`) o `let`/`run`.
3. **Data Classes & Sealed Interfaces**:
   * Usar `data class` para DTOs, modelos de dominio y estados de UI (`UiState`).
   * Usar `sealed interface` o `sealed class` para representar jerarquías de estados finitos (ej. `ScreenState.Loading`, `ScreenState.Success`, `ScreenState.Error`).
4. **Expresiones `when` exhaustivas**:
   * Al hacer `when` sobre una `sealed interface`, no usar la rama `else` a menos que sea inevitable. Esto garantiza comprobación en tiempo de compilación si se agrega un nuevo estado.

---

## ⚡ 2. Estilo en Jetpack Compose

1. **Naming de Composables**:
   * Si la función Composable emite elementos visuales en pantalla, su nombre DEBE ser un sustantivo en `PascalCase` (`UserCard()`, `PrimaryButton()`).
   * Si la función es un modifier o helper que no emite UI directamente, usar `camelCase`.
2. **Parámetro `modifier`**:
   * Todo Composable reutilizable DEBE aceptar un parámetro `modifier: Modifier = Modifier` como **primer parámetro opcional** después de los parámetros obligatorios.
3. **Hoisting de Estado (State Hoisting)**:
   * Los Composables de UI deben ser "tontos" (stateless) en la medida de lo posible. Pasar los datos vía parámetros y exponer eventos mediante lambdas (`onClick: () -> Unit`).

---

## 🧪 3. Concurrencia con Coroutines & Flow

* **Dispatchers**: Nunca usar `Dispatchers.IO` o `Dispatchers.Default` harcodeados dentro de clases o ViewModel. Inyectar `CoroutineDispatcher` para facilitar unit testing.
* **StateFlow en ViewModel**: Expone el estado a Compose como `StateFlow` inmutable mediante `_uiState.asStateFlow()`.
* **Scope**: Usar `viewModelScope` dentro de ViewModels y `lifecycleScope` en Android Components.

---

## 🛠️ 4. Formateo y Linters

* Usar **ktlint** (`./gradlew ktlintCheck`) para verificar formateo de código.
* Usar **detekt** (`./gradlew detekt`) para análisis estático de complejidad y code smells.
