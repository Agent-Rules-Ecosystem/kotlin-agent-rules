# 📏 Reglas de Importación por Nivel y Capa

> Pertenece a: `overview/architecture/core/import_rules.md`  
> Referenciado desde: [`overview/architecture.md`](../../architecture.md)

## 1. Directivas de Importación de Capas

1. **Presentation / Entry**: Puede importar `Domain` y `Core`. **Nunca** importa directamente `Infrastructure`.
2. **Domain**: Pura. **No importa** capas externas (`Presentation`, `Infrastructure`).
3. **Infrastructure**: Implementa contratos de `Domain`. Puede importar `Core`.
4. **Core**: Compartido y transversal. No importa `Presentation` ni `Infrastructure`.
