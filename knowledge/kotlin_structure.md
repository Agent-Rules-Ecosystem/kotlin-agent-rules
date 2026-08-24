# Estructura Canónica de Proyecto — Kotlin (Android Nativo / KMP)

Este documento establece la organización estándar de archivos y paquetes para proyectos nativos Android y proyectos Kotlin Multiplatform (KMP).

---

## 📱 1. Estructura Estándar Android Nativo (Gradle Multi-Módulo o Monolítico)

En un proyecto Android nativo con Jetpack Compose y Clean Architecture:

```text
root/
├── app/                          # Módulo principal de la aplicación Android
│   ├── build.gradle.kts          # Scripts de construcción Gradle KTS del módulo app
│   ├── proguard-rules.pro        # Reglas de ofuscación R8 / ProGuard
│   └── src/
│       ├── main/
│       │   ├── AndroidManifest.xml   # Manifest principal (permisos, actividades, servicios)
│       │   ├── java/                 # (Opcional) Código Kotlin/Java heredado
│       │   ├── kotlin/ com/domain/app/
│       │   │   ├── di/               # Módulos de Inyección de Dependencias (Hilt / Koin)
│       │   │   ├── data/             # Capa de Datos (Data Sources, DAOs, DTOs, Repositories Impl)
│       │   │   │   ├── local/        # Room Database, SharedPrefs/DataStore
│       │   │   │   ├── remote/       # Retrofit Services, Ktor client, DTOs
│       │   │   │   └── repository/   # Implementaciones de Repositorios
│       │   │   ├── domain/           # Capa de Dominio (Modelos de Entidad, UseCases, Interfaces Repo)
│       │   │   │   ├── model/        # Entidades puras de Kotlin (sin dependencias Android)
│       │   │   │   ├── repository/   # Interfaces de repositorios
│       │   │   │   └── usecase/      # Kasos de uso / Interactors
│       │   │   └── ui/               # Capa de Presentación (Jetpack Compose)
│       │   │       ├── components/   # Composables reutilizables de UI
│       │   │       ├── screens/      # Pantallas principales y sus ViewModels
│       │   │       ├── navigation/   # NavHost, Routes y Navigation Graphs
│       │   │       └── theme/        # Color, Type, Theme (Material 3 tokens)
│       │   └── res/                  # Recurso nativos (Drawables, Values, Mipmap, XML)
│       └── test/                     # Unit tests (JUnit5, Mockk, Coroutines Test)
│       └── androidTest/              # Instrumented tests (Espresso, Compose Test Rule)
├── gradle/                       # Wrappers y catalogos de dependencias (libs.versions.toml)
├── build.gradle.kts              # Root Gradle build script
└── settings.gradle.kts           # Configuración de módulos e inclusión de dependencias
```

---

## 🌐 2. Estructura Kotlin Multiplatform (KMP)

Cuando el proyecto comparte código entre Android, iOS y Desktop:

```text
root/
├── shared/                       # Módulo KMP compartido
│   ├── build.gradle.kts
│   └── src/
│       ├── commonMain/kotlin/    # Código puro compartido (Dominio, Data, ViewModels)
│       ├── androidMain/kotlin/   # Implementaciones específicas de Android (expect/actual)
│       └── iosMain/kotlin/       # Implementaciones específicas de iOS
├── composeApp/                   # App Compose Multiplatform (si usa UI compartida)
├── androidApp/                   # Punto de entrada nativo Android
└── iosApp/                       # Proyecto Xcode nativo iOS (Swift / SwiftUI)
```

---

## ⚙️ Reglas de Organización para el Agente

1. **Paquetes por Capa / Feature**: Organizar por capas (`data`, `domain`, `ui`) o por feature (`ui/auth/`, `ui/home/`). Mantener la separación de responsabilidades estricta.
2. **Sin importaciones Android en `domain`**: El paquete `domain/` debe ser Kotlin puro (`kotlin.*`). No debe importar `android.*` ni `androidx.*`.
3. **Control de Versionado de Dependencias**: Utilizar siempre Version Catalogs (`gradle/libs.versions.toml`) en lugar de strings harcodeadas en los archivos `build.gradle.kts`.
