# Mapa canónico de rutas — kotlin-agent-rules

## Reglas globales: submódulo `.agents/`

| Recurso | Ruta | Carga |
|---|---|---|
| Comunicación | `.agents/core/communication.md` | Obligatoria — **leer primero** |
| Router | `.agents/AGENTS.md` | Obligatoria |
| Brain | `.agents/core/brain.md` | Obligatoria |
| Comandos | `.agents/core/commands.md` | Obligatoria |
| Adaptadores (Codex / Cursor / etc) | `.agents/adapters/` (`AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, `cursor-rule.mdc`, `README.md`) | Al instalar |
| Estructura Kotlin / Android | `.agents/knowledge/kotlin_structure.md` | Discovery |
| Estilo Kotlin / Lint | `.agents/knowledge/code_style.md` | Bajo demanda |
| Referencia Capas Arquitectura | `.agents/knowledge/architecture.md` | Bajo demanda |
| Release Checklist (Play Store / AAB) | `.agents/knowledge/release_checklist.md` | `$close` / Build |
| Plantillas `overview/` | `.agents/templates/` | `$boot` / Inicio |
| Skills específicas del proyecto | `.skills/<skill_name>/SKILL.md` (submódulo del repo huésped) | Bajo demanda — vinculadas via `.agents/skills.json` |

> **Gobernanza de Skills por Proyecto**: Skills específicas de un proyecto deben gestionarse como submódulo independiente en `.skills/<skill_name>/` (raíz del repo huésped, **fuera** de `.agents/`). Vincular en `.agents/skills.json` con `{ "path": "../.skills/<skill_name>" }`.

> **Repo oficial (`kotlin-agent-rules`)**: Al editar este repositorio directamente, las rutas `.agents/core/…` equivalen a `core/…`, `.agents/templates/` a `templates/`, etc. No existe el prefijo `.agents/` en la raíz de este repo.

## Estado local: raíz del proyecto

| Recurso | Ruta | Carga |
|---|---|---|
| Sesión | `overview/session.md` | Inicio/cierre |
| Trabajo (Índice Maestro) | `overview/work.md` | Inicio/cierre |
| Tarea Activa | `overview/work/tasks.md` | Inicio/en ejecución |
| Pendientes | `overview/work/pendientes.md` | Cierre/bajo demanda |
| Deuda Técnica | `overview/work/deuda_tecnica.md` | Inicio/bajo demanda |
| Protocolo Revisión Work | `overview/work_review.md` | Fin de `$boot` |
| Aprendizajes | `overview/learning.md` | Al cerrar |
| Arquitectura real | `overview/architecture.md` | Inicio / `$archi` / `$close` |
| Tracker Arquitectura | `overview/trackers/architecture.md` | Bajo demanda |
| Tracker Progreso | `overview/trackers/progress.md` | Inicio/cierre |
| Historial | `overview/history/` | Al resumir |
| Contexto de dominio | `overview/context/` | Inicio/bajo demanda |
| Flujos de dominio | `overview/workflows/` | Bajo demanda |

### Backlog canónico único y Prioridad de atención

- `overview/work.md` = **único** índice y tabla maestra de IDs (`tarea` / `bug` / `deuda`).
- **Orden de prioridad de atención en `$work`**:
  1. `overview/work/tasks.md` (tarea activa en ejecución)
  2. `overview/work/pendientes.md` (ítems de seguimiento identificados)
  3. `overview/work/deuda_tecnica.md` (deuda ordenada por prioridad **Alta**, **Media** y **Baja**)
- **Histórico de completados**: Al resolver cualquier ítem de trabajo (tarea, bug o deuda), retirarlo inmediatamente de las tablas activas y trasladarlo a `## ✅ Completados (Historial)` en `work.md`, `deuda_tecnica.md` y `pendientes.md` conservando su ID (`[w1]`, `[d2]`, `[p1]`).
