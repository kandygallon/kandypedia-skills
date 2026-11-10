---
name: integrar
description: Usa esta skill para mergear todas las branches paralelas en el orden correcto, resolver conflictos y verificar que el resultado funciona end-to-end.
---

# Integrar

## ¿Cuándo usar esta skill?

Úsala cuando:
- Todas las tareas paralelas terminaron (pasaron `/antes-del-merge`)
- Tienes múltiples branches listas para mergear
- Necesitas verificar que el sistema completo funciona junto

**No la uses** cuando solo hay una branch o las tareas siguen en progreso.

## Filosofía

El código que funcionaba por separado debe funcionar junto. Pueden surgir conflictos de git, conflictos lógicos o dependencias faltantes.

## Proceso paso a paso

### 1. Preparación

Verifica:
1. Todas las tareas pasaron `/antes-del-merge`
2. Todas las branches están pusheadas
3. Tienes el plan de reparto (orden de merge)

```bash
cd ~/proyecto  # Worktree principal
git checkout main
git pull origin main
```

### 2. Determina el orden de merge

**Regla:**
1. Primero: dependencias (setup, modelos)
2. Segundo: owners de archivos compartidos
3. Tercero: tareas independientes
4. Último: tests e2e

Consulta el plan de reparto para el orden específico.

### 3. Mergea una branch a la vez

```bash
git merge task-1-setup
npm test  # Verifica después de cada merge
```

**Si hay conflictos**, usa la [guía de resolución](resolucion-conflictos.md).

Repite para cada branch en orden:

```bash
git merge task-2-products
npm test
git merge task-3-ui
npm test
```

### 4. Verificación completa

Después de mergear todo:

1. **Tests**: `npm test` y `npm run test:e2e`
2. **Linter**: `npm run lint`
3. **Ejecuta**: `npm start` y verifica manualmente
4. **Prueba funcionalidades** de cada tarea integrada

Ver [checklist de verificación](checklist.md) completo.

### 5. Pushea la integración

```bash
git push origin main
```

Antes de pushear, considera CI/CD, code review, o staging.

### 6. Limpia worktrees y branches

```bash
git worktree remove ../proyecto-task-1
git worktree remove ../proyecto-task-2
git branch -d task-1-setup task-2-products
git push origin --delete task-1-setup task-2-products
```

### 7. Reporte de integración

Documenta qué se integró:

```markdown
# Reporte de Integración

## Branches integradas
- task-1-setup: [descripción]
- task-2-products: [descripción]

## Conflictos resueltos
[Si hubo]

## Verificación
- ✅ Tests pasan
- ✅ Linter pasa
- ✅ App funciona end-to-end
```

## Entregables

Al terminar:

1. ✅ Todas las branches integradas en main
2. ✅ Conflictos resueltos
3. ✅ Tests pasando
4. ✅ Proyecto funcional end-to-end
5. ✅ Reporte de integración
6. ✅ Worktrees y branches limpios

## Transición

Después de integrar:
- Deploy a staging/producción
- Code review del resultado
- Retrospectiva del proceso

## Referencias

- [resolucion-conflictos.md](resolucion-conflictos.md): Guía detallada para resolver conflictos
- [estrategias.md](estrategias.md): Estrategias de merge (merge commits, squash, rebase)
- [checklist.md](checklist.md): Checklist completo de verificación
- [ejemplos.md](ejemplos.md): Ejemplos de órdenes de merge y casos comunes

## Notas importantes

- **No integres si algo no funciona**: mejor resolver en la branch individual
- **Mergea una branch a la vez**: detecta problemas temprano
- **Verifica después de cada merge**: tests, linter, ejecución
- **Documenta resoluciones**: ayuda al próximo que integre
