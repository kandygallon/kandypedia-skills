---
name: contrato
description: Usa esta skill después de /reparto y antes de crear worktrees. Define el contrato exacto (entradas, salidas, errores) de cada punto de unión para que todos los agentes trabajen con la misma especificación.
---

# Contrato antes de paralelizar

## ¿Cuándo usar esta skill?

Úsala cuando:
- ✅ Ya dividiste el trabajo con `/reparto` en tareas paralelas
- ✅ Necesitas definir contratos exactos entre las partes
- ✅ Vas a crear worktrees con múltiples agentes

**No la uses** cuando no dividiste el trabajo o es una tarea individual.

## Filosofía

Vamos a dividir esta tarea entre varios agentes. Antes de escribir código, crea o actualiza `docs/contrato.md` con:

1. Objetivo de la tarea en 2 líneas
2. Partes en que se divide y qué agente/worktree hace cada una
3. Para cada punto de unión (endpoint, función, tipo, evento, tabla):
   - Entrada: campos, tipos, obligatorios u opcionales
   - Salida: estructura exacta con ejemplo en JSON o código
   - Errores: cada caso con código/excepción y ejemplo
4. Decisiones tomadas y cosas FUERA del alcance
5. Preguntas abiertas para decidir

## Reglas

- **No escribas código** todavía
- Si algo es ambiguo, ponlo en "Preguntas abiertas"
- **DETENTE** cuando termines y espera aprobación

## Después de aprobado

1. **Commitea `docs/contrato.md` a main** ANTES de crear worktrees:

```bash
git add docs/contrato.md
git commit -m "docs: agregar contrato para trabajo paralelo"
git push origin main
```

2. Cada agente lee `docs/contrato.md` antes de empezar
3. No puede cambiarlo sin avisarte
4. Si necesitas cambiar: nuevo commit en main + anuncio a todos

## Plantilla

Ver [plantilla-contrato.md](plantilla-contrato.md) con estructura completa.

Ver [ejemplos de puntos de unión](ejemplos-puntos-union.md) para endpoints, funciones, tipos, etc.

## Entregables

Al terminar:

1. ✅ `docs/contrato.md` completo con puntos de unión
2. ✅ Aprobación del usuario
3. ✅ Commit en `main` ANTES de worktrees
4. ✅ Preguntas abiertas resueltas

## Transición

Después de aprobar:
1. Commitea `docs/contrato.md` a main
2. Usa `/worktrees` para crear espacios de trabajo
3. Cada agente lee `@docs/contrato.md` antes de empezar
4. Usa `/tests-primero` con ejemplos del contrato

## Relación con otras skills

- **Después de `/reparto`**: ese skill divide, este define contratos
- **Antes de `/worktrees`**: el contrato debe estar commiteado primero
- **Referenciado en `/archivos-compartidos`**: contrato.md es especial (read-only)

## Referencias

- [plantilla-contrato.md](plantilla-contrato.md): Estructura completa del documento
- [ejemplos-puntos-union.md](ejemplos-puntos-union.md): Ejemplos por tipo (endpoint, función, tipo, evento)

## Notas importantes

- **Commitea a main antes de worktrees**: garantiza misma versión para todos
- **Cambios van por main**: nunca en branch, siempre anunciar
- **Ejemplos son vinculantes**: son tests que deben pasar
- **DETENTE y espera**: no sigas sin aprobación
