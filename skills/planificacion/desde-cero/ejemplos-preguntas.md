# Ejemplo de Diálogo Completo

**Usuario:** "Quiero crear una herramienta para trabajar en paralelo con agentes."

**Agente:**

**P1:** "¿Qué problema estás resolviendo?"

**Usuario:** "Cuando lanzo varios agentes en el mismo repo, se pisan."

**P2:** "¿Quién la usará? ¿Saben git worktrees?"

**Usuario:** "Desarrolladores que usan Cursor, conocen git básico pero no worktrees."

**P3:** "¿Cuál es la versión más pequeña que funciona?"

**Usuario:** "Que me ayude a crear un worktree por tarea, me diga dónde está cada uno, y al final los mergee."

**P4:** "¿Restricciones?"

**Usuario:** "Script simple, sin dependencias raras. Bash o Node."

**P5:** "¿Qué está fuera de alcance?"

**Usuario:** "No necesito UI gráfica, ni nada en la nube. Solo local."

**P6:** "¿Cómo sabemos que está terminado?"

**Usuario:** "Corro `parallel-work split tasks.txt`, crea 3 worktrees. Trabajo en cada uno. Corro `parallel-work merge` y todo queda integrado."

**Especificación producida:**

```markdown
# Especificación: parallel-work

## Problema
Cuando varios agentes trabajan en el mismo repo, se pisan porque modifican los mismos archivos. Causa conflictos y pérdida de trabajo.

## Usuario
Desarrolladores que usan Cursor y conocen git básico pero no worktrees. Trabajan en proyectos medianos y quieren paralelizar con agentes.

## MVP
- Comando `split`: crea worktree + branch por tarea
- Comando `status`: muestra estado de cada worktree
- Comando `merge`: integra todos en main
- Comando `clean`: elimina worktrees temporales

## Restricciones
- Script simple (Bash o Node.js)
- Sin dependencias complejas
- Solo local (no nube)

## Fuera de alcance
- UI gráfica
- Sincronización en tiempo real
- Servicios remotos
- CI/CD

## Definición de hecho
Usuario corre `parallel-work split tasks.txt` con 3 tareas. Crea 3 worktrees. Trabaja en cada uno con agente distinto. Corre `parallel-work merge` y todo se integra sin conflictos manuales (si no tocaron mismas líneas).

## Arquitectura

**Opción 1: Bash**
- Pros: cero dependencias, funciona en Unix
- Contras: difícil mantener, sin tests

**Opción 2: Node.js**
- Pros: mantenible, testeable, multiplataforma
- Contras: requiere Node

**Recomendación**: Node.js (usuario ya tiene Cursor que usa Node)
```
