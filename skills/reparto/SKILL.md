---
name: reparto
description: Divide un proyecto en tareas que varios agentes pueden hacer en paralelo, cada una con sus propios archivos, y guarda el plan en docs/reparto.md. Úsala cuando ya tienes la especificación o el diagnóstico y el trabajo es demasiado grande para un solo agente.
disable-model-invocation: true
---

# Reparto

> **De dónde sale.** Cuando rediseñé kandypedia.com con cuatro agentes, antes de escribir los encargos le
> pasé el plan a un agente orquestador. Lo primero que hizo fue preguntarme qué hacer con las secciones que
> no tenían dueño claro. Encontró cinco huecos, entre ellos una página de gracias que no era de nadie. Desde
> entonces, un reparto no está listo hasta que cada archivo tiene un dueño, y solo uno.

## Cuándo usarla

- Ya tienes `docs/especificacion.md` (de `/desde-cero`) o `docs/diagnostico.md` (de `/analizar-existente`).
- El trabajo es demasiado grande para un solo agente.
- Hay partes que pueden hacerse a la vez sin tocar los mismos archivos.

**No la uses** si el proyecto es pequeño o todas sus partes dependen unas de otras: un solo agente lo hace mejor.
Tampoco si aún no tienes especificación: haz eso primero.

## Pasos

1. Lee la especificación o el diagnóstico. Anota las funcionalidades principales, las capas o módulos y las
   dependencias obvias (qué tiene que existir antes que otra cosa).
2. Busca fronteras naturales:
   - Por tipo de código: frontend y backend; interfaz, lógica y datos.
   - Por funcionalidad: autenticación, panel, reportes.
   - Por carpeta: `components/`, `pages/`, `services/`.

   Prefiere pocas tareas grandes a muchas pequeñas: coordinar muchos agentes cuesta más de lo que ahorra.
3. Define cada tarea con:
   - **Nombre y objetivo**: qué produce.
   - **Archivos que posee**: solo esta tarea los crea o modifica.
   - **Archivos que lee**: los consulta, pero no los cambia. Si necesita cambiarlos, usa `/archivos-compartidos`.
   - **Dependencias**: qué tareas tienen que terminar antes.
   - **Entregable y verificación**: qué funciona al terminar y cómo se prueba sin juntar nada.

   Cada tarea es independiente, completa, limitada y verificable. Si no se puede probar sola, la división está mal.
4. Comprueba que **cada archivo tiene un dueño, y solo uno**. Los que necesitan varias tareas (rutas, estilos
   globales, configuración) van en una lista aparte, con un único dueño. Que sean los menos posibles.
5. Ordena las tareas en rondas. Las que no dependen de nada van en la ronda 1, en paralelo. Las que dependen de
   otras, en la ronda siguiente. Indica también el orden en que se integrarán las ramas.
6. Escribe el plan en `docs/reparto.md` con esta estructura:

   ```markdown
   # Reparto: <proyecto>

   ## Resumen
   <cómo se dividió y por qué>

   ## Ronda 1 (sin dependencias)
   ### Tarea 1: <nombre>
   - Objetivo:
   - Archivos que posee:
   - Archivos que lee:
   - Dependencias: ninguna
   - Entregable:
   - Verificación:

   ## Ronda 2 (dependen de la ronda 1)
   ### Tarea 3: <nombre>
   ...

   ## Archivos compartidos
   - `<archivo>` → dueño: Tarea N

   ## Orden de integración
   1. ...

   ## Preguntas para la persona
   - ...
   ```

7. **DETENTE y pide aprobación.** Presenta el plan y pregunta en concreto lo que el plan no puede decidir solo:
   - Archivos o secciones **sin dueño claro**: ¿de qué tarea son?
   - Textos o piezas **repetidos en dos sitios**: ¿cuál se queda?
   - Partes que **quizá ya no tienen sentido**: ¿se hacen o se quitan?

   Son decisiones de negocio, no técnicas. No las tomes tú.
8. Cuando la persona apruebe, incorpora sus respuestas y commitea el plan en `main` antes de pasar a `/worktrees`.
   Un worktree solo ve lo que está commiteado:

   ```bash
   git switch main
   git add docs/reparto.md
   git commit -m "docs: plan de reparto aprobado"
   ```

## Qué produce

- `docs/reparto.md`, aprobado y commiteado en `main`, con:
  - tareas numeradas, cada una con sus archivos y sin solapes;
  - dependencias explícitas y rondas de ejecución;
  - entregable y verificación por tarea;
  - archivos compartidos con un único dueño y el orden de integración.

## Siguiente

- `/contrato` para fijar las entradas, salidas y errores de cada punto de unión entre tareas.
- `/worktrees` para crear un worktree y una rama por tarea.
- `/archivos-compartidos` si algún archivo lo necesitan varias tareas.

Si necesitas un ejemplo completo, lee [references/ejemplo.md](references/ejemplo.md).
