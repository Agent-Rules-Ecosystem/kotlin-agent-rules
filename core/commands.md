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
| `$learnskill [skill] [texto]` | Registrar propuesta de mejora para una skill en `overview/learning.md` |
| `$revlearnskill` | Clasificar propuestas `[nombre-skill]` en el governing repo (`*-agent-rules`) |
| `$laconico` | Activar o reforzar el Modo Lacónico (por defecto) |
| `$modo:detallado` | Activar explicaciones técnicas paso a paso sin relleno |
| `$close` | Protocolo de cierre de sesión con comprobación Gradle e inspección de lint |

---

## Definición de cada comando

### `$boot`

Dispara el bootstrap completo. Pasos que el agente debe ejecutar:
0. Verificar que exista `.git/` (en proyectos limpios o ZIP sin control de versiones, ejecutar `git init` antes de intentar vincular submódulos `.agents` o `.skill/*`) y correr `git submodule status` para verificar integridad de submódulos.
1. Leer `core/path_map.md`, `core/communication.md`, `core/brain.md`, `core/commands.md`.
2. **Scaffold incremental desde templates**: Comparar la estructura de `templates/` con `overview/`. Por cada archivo o subdirectorio presente en `templates/` que no exista en `overview/`, crearlo copiando el contenido de la plantilla correspondiente. Este paso aplica tanto a proyectos nuevos (sin `overview/`) como a proyectos existentes donde el ecosistema ha evolucionado y agregado nuevas estructuras canónicas. **No sobreescribir** archivos que ya existan en `overview/`.
3. Cargar archivos de control según el **Protocolo de Carga de Contexto en 3 Capas** (`core/brain.md`): primero **Capa 1** (`overview/session.md`, `overview/work.md`), y cargar **Capa 2** (`tasks.md`, `pendientes.md`, `deuda_tecnica.md`) en el Paso 8 (`work_review.md`) o bajo demanda por categoría.
4. Evaluar `Agente:` y `Modo:` en `overview/session.md`: si `Agente:` difiere del modelo actual → activar handoff si difiere. El modo por defecto es `laconico` (solo cambiar a `detallado` si `Modo: detallado` está explícito).
5. Auditoría de líneas: listar archivos Kotlin (`.kt`/`.kts`) >250L; en monorepos o bundles multi-app, filtrar y agrupar por módulo de negocio antes de sugerir IDs `deuda` en `overview/work/deuda_tecnica.md`.
6. Auditar `overview/learning.md` (Protocolo de 3 Vías — ver `core/learning_protocol.md`).
7. **Verificación y actualización de `overview/commands_project.md` y Salud de Skills**: Escanear comandos del Core y skills activas. Auditoría pasiva de salud de skills en `overview/work/skill/`.
8. **Revisión de Trabajo (`work_review.md`)**: Respetar prioridades (1º `tasks.md`, 2º `pendientes.md`, 3º `deuda_tecnica.md`).
9. Reportar en 5 líneas máximo: agente anterior, nodo activo, tareas pendientes, estado validación, flags (alias/session/líneas/conflicto/skills-pendientes), síntesis de `work_review` y próximo paso. Incluir línea `scaffold:` listando archivos/dirs creados desde templates (o `scaffold: ninguno` si `overview/` ya estaba al día).

---

### `$status`

Mostrar el estado actual del proyecto sin modificar ningún archivo.

---

### `$work [descripción]`

Registrar una nueva tarea o bug en el sistema de trabajo modular `overview/work/`. Al resolver un ítem proveniente de `overview/work/pendientes.md` o `overview/work/deuda_tecnica.md`, mover físicamente la fila correspondiente a `## ✅ Completados (Historial)` de ese archivo y actualizar `overview/work.md`, retirándola de la tabla activa para no dejar filas resueltas mezcladas en el backlog activo.

Ejemplo de uso:
```
$work bug: el StateFlow de autenticación no remite eventos al recomponer la pantalla en Jetpack Compose
```

---

**Reglas operativas en `$work`**:
- **Feature-Stream / Code-to-Spec Hook**: Si el proyecto mantiene especificaciones o contratos (`specs/`, `overview/architecture/core/contracts.md`), propagar los cambios funcionales o de interfaz en el mismo ciclo.
- **Traslado físico al resolver**: Al resolver un ítem de `pendientes.md` o `deuda_tecnica.md`, mover físicamente su fila a `## ✅ Completados (Historial)` y actualizar `work.md`.
- **Ejecución continua**: El registro preventivo en `$work` es una salvaguarda anti-desconexión; continuar de inmediato con la ejecución técnica en el mismo turno sin detenerse.

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
4. **Auditoría de Regla Atómica de Escala**: Por cada `overview/architecture/modules/<modulo>.md` existente, verificar si cubre más de un dominio de decisión (señal: el agente necesita leerlo completo para responder algo específico). Si se detecta, convertirlo en carpeta `overview/architecture/modules/<modulo>/` aplicando la Plantilla Canónica 3 de `ARCHITECTURE_STANDARD.md`: crear `<modulo>.md` como índice (< 150L) y subdocumentos atómicos por cada dominio identificado. **No anticipar subdivisiones futuras**: solo actuar cuando la señal es evidente en el módulo actual.
5. Confirmar: `Arquitectura viva actualizada conforme a ARCHITECTURE_STANDARD.md (Índice Raíz overview/architecture.md + Subdocumentos en overview/architecture/).`

---

### `$learn [texto]` y `$learnagnostico [texto]`

Registrar un aprendizaje en `overview/learning.md` aplicando el **Filtro Agnóstico**.

---

### `$learnskill [skill] [texto]`

Registrar una mejora candidata para una skill específica en `overview/learning.md` bajo `## 📌 Propuestas de mejora` con la etiqueta `[nombre-skill]`, aplicando el Filtro Agnóstico. **NUNCA** modificar `.skill/` ni `.agents/` directamente. Ver detalle en `core/learning_protocol.md`.

---

### `$revlearnskill`

En el repositorio oficial de una skill (o del ecosistema), auditar `overview/learning.md` buscando propuestas etiquetadas `[nombre-skill]`, evaluarlas con la Evaluación de 3 Vías (`core/learning_protocol.md`) y promover o descartar según corresponda.

---

### `$laconico`

Activa o refuerza el **Modo Lacónico** (estilo operativo por defecto en `core/communication.md`): respuestas breves, directas, sin prosa ni clichés, usando flechas (`A -> B`) y referencias `[archivo#L10-L15]`. Actualiza `Modo: laconico` en `overview/session.md`.

---

### `$modo:detallado`

Conmuta temporalmente a **Modo Detallado**: habilita explicaciones técnicas paso a paso cuando la complejidad lo requiera, manteniendo tono sobrio, analítico, cero clichés y cero adulación. Actualiza `Modo: detallado` en `overview/session.md`. Para volver al modo breve, ejecutar `$laconico`.

---

### `$close`

Protocolo de cierre de sesión.

El agente debe:
1. Ejecutar `./gradlew lint` o `./gradlew test` si aplica. Suite de tests ausente → `no aplica`. Registrar explícitamente como **omitidas** (con motivo) las comprobaciones no ejecutables, sin contarlas como aprobadas. Si la tarea implica build o release → consultar `.agents/knowledge/release_checklist.md`.
2. Sincronizar simultáneamente todos los archivos de control en `overview/` (`session.md`, `work.md`, `tasks.md`, `pendientes.md`, `deuda_tecnica.md`, `work_review.md` y `architecture.md`).
3. **Traslado obligatorio a Historial de Completados**: Al finalizar la sesión, cualquier ítem resuelto durante el ciclo que provenga de `pendientes.md` o `deuda_tecnica.md` debe trasladarse físicamente a su respectiva tabla `## ✅ Completados (Historial)` (y actualizarse en `work.md`), eliminándolo de la tabla activa para mantener limpio el backlog.
4. Registrar `Agente:` con firma propia en `session.md`, actualizar `## Cambios` y `## Reanudar`.
5. Reportar: `Sesión cerrada con sincronización automática de rastreadores. Próximo: [nodo]. Estado: [verificado/no verificado/no aplica].`
