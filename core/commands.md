# Comandos del Core ($-commands) — kotlin-agent-rules

Cuando el usuario escribe un comando con prefijo `$`, el agente lo reconoce como instrucción explícita y ejecuta el protocolo correspondiente **inmediatamente**, sin esperar bootstrap automático.

---

## Referencia rápida

| Comando | Acción |
|---|---|
| `$boot` | Bootstrap completo del proyecto Kotlin / Android |
| `$status` | Mostrar estado actual en resumen |
| `$work [descripción]` | Registrar nueva tarea/bug en el módulo Kotlin |
| `$archi` | Actualizar arquitectura viva (diagramas Mermaid de ViewModels, Repositories, Screens) |
| `$learn [texto]` | Registrar aprendizaje general candidato en `overview/learning.md` |
| `$learnagnostico [texto]` | Abstraer a términos genéricos antes de registrar |
| `$close` | Protocolo de cierre de sesión con comprobación Gradle e inspección de lint |

---

## Definición de cada comando

### `$boot`

Dispara el bootstrap completo. Pasos que el agente debe ejecutar:
0. Ejecutar `git submodule status` para verificar integridad de submódulos.
1. Leer `core/path_map.md`, `core/communication.md`, `core/brain.md`, `core/commands.md`.
2. Verificar si existe `overview/` — si no, crear desde `templates/`.
3. Cargar archivos de control de `overview/`: `session.md`, `work.md`, `work/tasks.md`, `work/deuda_tecnica.md`, `work/pendientes.md`, `work_review.md`, `architecture.md` y `trackers/progress.md`.
4. Detectar si el `Agente:` en `session.md` difiere del modelo actual → activar handoff si difiere.
5. Auditoría de líneas: listar archivos Kotlin (`.kt`/`.kts`) >250L; sugerir IDs `deuda` en `overview/work/deuda_tecnica.md`.
6. Auditar `overview/learning.md` (Protocolo de 3 Vías — ver `core/learning_protocol.md`).
7. **Verificación y actualización de `overview/commands_project.md`**: Escanear comandos del Core y skills activas.
8. **Revisión de Trabajo (`work_review.md`)**: Respetar prioridades (1º `tasks.md`, 2º `pendientes.md`, 3º `deuda_tecnica.md`).
9. Reportar en 5 líneas máximo: agente anterior, nodo activo, tareas pendientes, estado validación y próximo paso.

---

### `$status`

Mostrar el estado actual del proyecto sin modificar ningún archivo.

---

### `$work [descripción]`

Registrar una nueva tarea o bug en el sistema de trabajo modular `overview/work/`.

Ejemplo de uso:
```
$work bug: el StateFlow de autenticación no remite eventos al recomponer la pantalla en Jetpack Compose
```

---

### `$archi`

Protocolo dedicado a actualizar `overview/architecture.md` mediante diagramas Mermaid (`graph LR` / `graph TD`) mapeando la relación entre Composables, ViewModels, UseCases, Repositories, Room DAOs y servicios Retrofit/Ktor.

---

### `$learn [texto]` y `$learnagnostico [texto]`

Registrar un aprendizaje en `overview/learning.md` aplicando el **Filtro Agnóstico**.

---

### `$close`

Protocolo de cierre de sesión.

El agente debe:
1. Ejecutar `./gradlew lint` o `./gradlew test` si aplica. Suite de tests ausente → `no aplica`. Si la tarea implica build o release → consultar `.agents/knowledge/release_checklist.md`.
2. Sincronizar simultáneamente todos los archivos de control en `overview/` (`session.md`, `work.md`, `tasks.md`, `pendientes.md`, `deuda_tecnica.md`, `work_review.md` y `architecture.md`).
3. Registrar `Agente:` con firma propia en `session.md`, actualizar `## Cambios` y `## Reanudar`.
4. Reportar: `Sesión cerrada con sincronización automática de rastreadores. Próximo: [nodo]. Estado: [verificado/no verificado/no aplica].`
