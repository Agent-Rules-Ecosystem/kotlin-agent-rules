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
3. Cargar archivos de control de `overview/`: `session.md`, `work.md`, `work/tasks.md`, `work/deuda_tecnica.md`, `work/pendientes.md`, `work_review.md`, `ARCHITECTURE.md` (o `overview/architecture/`) y `trackers/progress.md`.
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

Protocolo dedicado exclusivamente a auditar, modularizar y mantener la arquitectura del proyecto conforme al **Agent Architecture Standard (`ARCHITECTURE_STANDARD.md`)**.

El agente debe:
0. **Detección de archivo plano legado**: Si existe `overview/architecture.md` como archivo único (no carpeta), leerlo íntegramente como fuente de referencia. Usarlo para extraer diagramas, capas y módulos ya documentados antes de generar los subdocumentos. **No eliminarlo** hasta que la migración esté verificada.
1. Escanear exhaustivamente la estructura completa del proyecto, módulos y rutas.
2. Mantener `overview/architecture.md` como **Índice Raíz Hub & Spoke** (< 200L): diagrama Mermaid de alto nivel de capas, tabla de capas del sistema e hipervínculos a los subdocumentos en `overview/architecture/`. Si proviene de migración del archivo plano legado, extraer solo el contenido de alto nivel y mover los detalles técnicos a los subdocumentos.
3. Crear o actualizar los subdocumentos en `overview/architecture/` extrayendo o refinando el contenido del archivo plano legado (si existía):
   - `overview/architecture/routes_map.md` (Mapa global de enrutamiento: Navigation Compose).
   - `overview/architecture/core/data_flow.md` (Estado global, StateFlow/SharedFlow, Room/SQLite).
   - `overview/architecture/core/import_rules.md` (Reglas de importación por nivel de capa).
   - `overview/architecture/modules/<modulo>.md` (Subdocumento por cada módulo que supere 2 diagramas Mermaid o 5 componentes/pantallas).
4. Confirmar: `Arquitectura viva actualizada conforme a ARCHITECTURE_STANDARD.md (Índice Raíz overview/architecture.md + Subdocumentos en overview/architecture/).`
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
