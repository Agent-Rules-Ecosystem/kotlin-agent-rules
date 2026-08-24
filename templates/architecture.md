# Arquitectura del proyecto

Reemplazar con arquitectura real del proyecto al bootstrap.

```mermaid
graph LR
    UI[Jetpack Compose / Screens] --> State[ViewModel / StateFlow]
    State --> Service[API / Backend]
    Service --> Model[Models / Entities]
```

| Capa | Implementación real |
|---|---|
| Presentation | |
| Domain | |
| Data | |
