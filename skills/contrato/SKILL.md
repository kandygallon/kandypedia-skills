---
name: contrato
description: Fija en docs/contrato.md las entradas, salidas y errores de cada punto de unión entre tareas (endpoint, función, tipo, evento, tabla) para que todos los agentes trabajen con la misma especificación. Úsala después de /reparto y antes de crear worktrees.
disable-model-invocation: true
---

# Contrato

## Cuándo usarla

- Ya repartiste el trabajo con `/reparto` y `docs/reparto.md` está aprobado.
- Las tareas se comunican: una llama a una API, usa un tipo o lee una tabla que hace otra.
- Vas a crear worktrees y varios agentes trabajarán a la vez.

**No la uses** si aún no has repartido el trabajo (usa `/reparto`) o si las tareas no tienen ningún punto de unión.

## Pasos

1. Lee `docs/reparto.md` y la especificación. **No escribas código de implementación todavía.**
2. Lista los puntos de unión: todo lo que una tarea produce y otra consume (endpoint, función, tipo, evento,
   tabla). Para cada uno, anota quién lo implementa y quién lo usa.
3. Crea o actualiza `docs/contrato.md` con estas secciones:
   1. **Objetivo**, en 2 líneas.
   2. **Partes**: qué hace cada agente y en qué worktree, con la misma numeración que `docs/reparto.md`.
   3. **Puntos de unión.** Para cada uno:
      - Tipo y responsable.
      - **Entrada**: campos, tipos, obligatorios u opcionales.
      - **Salida**: estructura exacta, con un ejemplo real en JSON o código.
      - **Errores**: cada caso con su código o excepción (ID inexistente → 404) y un ejemplo de respuesta.
   4. **Decisiones tomadas** que afectan a todos (formato de errores, fechas, límites) y lo que queda
      **fuera del alcance**.
   5. **Preguntas abiertas** que debe decidir la persona.
4. Si algo es ambiguo, ponlo en «Preguntas abiertas». No lo supongas.
5. **DETENTE.** Presenta el contrato y las preguntas abiertas, y espera la aprobación. Las respuestas son
   decisiones de la persona, no tuyas.
6. Incorpora las respuestas: cada pregunta resuelta pasa a «Decisiones tomadas». No queda ninguna abierta.
7. Commitea el contrato en `main` **antes de crear worktrees**, para que todos los agentes partan de la misma
   versión:

   ```bash
   git switch main
   git add docs/contrato.md
   git commit -m "docs: contrato aprobado"
   ```

8. Si el contrato tiene que cambiar después, **DETENTE** y pide aprobación. El cambio se hace solo en `main`,
   con un commit nuevo, y se avisa a todos los agentes. Cada uno lo trae a su rama desde su worktree:

   ```bash
   git merge main
   ```

Reglas para los agentes de cada worktree:

- Leen `docs/contrato.md` antes de empezar y no lo modifican.
- Los ejemplos de entrada, salida y error son vinculantes: son los primeros tests que tienen que pasar.

## Qué produce

- `docs/contrato.md`, aprobado y commiteado en `main`, con:
  - objetivo y reparto de partes por agente y worktree;
  - cada punto de unión con entrada, salida, errores y ejemplos reales;
  - decisiones tomadas y lo que queda fuera del alcance;
  - ninguna pregunta abierta sin resolver.

## Siguiente

- `/worktrees` para crear un worktree y una rama por tarea.
- `/tests-primero` en cada worktree: los ejemplos del contrato se convierten en tests.
- `/archivos-compartidos`: `docs/contrato.md` es de solo lectura para los agentes.

Si necesitas un ejemplo completo, lee [references/ejemplo.md](references/ejemplo.md).
