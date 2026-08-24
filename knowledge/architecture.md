# Arquitectura Canónica — Kotlin (Clean Architecture + MVVM / MVI)

Guía arquitectónica para aplicaciones Android y Kotlin Multiplatform estructuradas con **Clean Architecture** y arquitectura de presentación **MVVM / MVI**.

---

## 🏛️ 1. Diagrama de Capas de la Arquitectura

```mermaid
graph TD
    subgraph Presentation Layer (UI)
        A[Jetpack Compose UI] -->|Observa StateFlow| B[ViewModel]
        B -->|Emite eventos / Intent| A
    end

    subgraph Domain Layer (Business Logic)
        B -->|Ejecuta| C[UseCases / Interactors]
        C -->|Consulta interfaz| D[Repository Interface]
    end

    subgraph Data Layer (Data Sources)
        E[Repository Implementation] .->|Implementa| D
        E -->|Local| F[Room DAO / DataStore]
        E -->|Remote| G[Retrofit API / Ktor Client]
    end
```

---

## 📐 2. Responsabilidades por Capa

### A. Presentation Layer (Capa de Presentación)
* **Jetpack Compose**: Dibuja la UI basándose exclusivamente en el `UiState` emitido por el `ViewModel`. No realiza lógica de negocio ni llamadas directas a repositorios.
* **ViewModel**: Retiene y gestiona el estado de la UI frente a cambios de configuración. Captura eventos de usuario (`Intent`/`Event`), invoca Casos de Uso del Dominio y actualiza el `StateFlow`.
* **State Management**:
  ```kotlin
  // Definición de estado sellado
  sealed interface UserUiState {
      data object Loading : UserUiState
      data class Success(val user: User) : UserUiState
      data class Error(val message: String) : UserUiState
  }
  ```

### B. Domain Layer (Capa de Dominio)
* **Entidades de Dominio**: Data classes puras que representan los modelos centrales de negocio.
* **Interfaces de Repositorio**: Definen el contrato de obtención y modificación de datos sin depender de frameworks.
* **UseCases**: Clases de responsabilidad única (ej. `GetUserProfileUseCase`, `AuthenticateUserUseCase`). Contienen la regla de negocio pura y pueden combinar múltiples repositorios.

### C. Data Layer (Capa de Datos)
* **Repository Implementations**: Implementan las interfaces de dominio. Deciden si leen de caché local (Room/DataStore) o de la red (Retrofit/Ktor).
* **Mappers**: Transforman objetos de red (`DTO`) u objetos de base de datos (`Entity`) a objetos de dominio (`Model`).
* **Data Sources**: Interfaces y llamadas directas a clientes HTTP o bases de datos SQLite local.

---

## 💉 3. Inyección de Dependencias (Hilt / Koin)

* **Hilt**: Estándar recomendado por Google en Android nativo (`@HiltViewModel`, `@Inject`, `@Module`, `@InstallIn(SingletonComponent::class)`).
* **Koin**: Estándar recomendado en Kotlin Multiplatform (KMP) por su ligereza y ausencia de procesamiento de anotaciones (KSP/kapt).

---

## 🛡️ 4. Reglas de Inviolabilidad Arquitectónica para el Agente

1. **Flujo de Datos Unidireccional (UDF)**: La UI solo envía eventos hacia arriba; los datos solo fluyen hacia abajo vía `StateFlow`.
2. **Desacoplamiento Estricto**: La capa de datos NUNCA expone DTOs ni modelos de Room hacia la capa de presentación. Usar Mappers para convertir a entidades de dominio.
3. **Puntuación de Complejidad**: Si un ViewModel supera las 250 líneas, separar la lógica de negocio en UseCases independientes.
