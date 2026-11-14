---
name: integrar
description: Junta las ramas de las tareas paralelas en una rama `integracion`, una a una y en el orden que confirma la persona, resuelve conflictos y comprueba que todo funciona junto. Úsala cuando todas las ramas pasaron /antes-del-merge. main solo se toca con aprobación.
disable-model-invocation: true
---

# Integrar

## Cuándo usarla
- Todas las tareas paralelas terminaron y cada rama pasó `/antes-del-merge`.
- Tienes varias ramas listas y hay que juntarlas y comprobar que funcionan juntas.
- Hay que integrar primero la rama dueña de un archivo compartido (integración temprana).

**No la uses** si aún hay tareas a medias, o si solo hay una rama: en ese caso basta un merge normal
con aprobación.

## Pasos
1. Comprueba lo previo: cada rama pasó `/antes-del-merge` y está subida al remoto. Lee en
   `docs/reparto.md` las secciones de archivos compartidos e integración final.
2. Crea la rama `integracion` desde `main` actualizado, en el repositorio principal (no en un worktree
   de tarea). Todo se junta aquí; `main` no se toca.
   ```bash
   git checkout main
   git pull origin main
   git checkout -b integracion
   ```
3. Propón el orden de merge:
   1. Ramas de las que dependen otras (base, modelos).
   2. Ramas dueñas de archivos compartidos, con los `REQUESTS.md` ya aplicados.
   3. Ramas independientes.
   4. Al final, tests de punta a punta o tareas de integración.

   **DETENTE.** Presenta el orden y el porqué. No mergees nada hasta que la persona lo confirme.
4. Antes de mergear una rama dueña, comprueba que aplicó las peticiones de las demás:
   ```bash
   git fetch origin
   git show origin/<otra-rama>:REQUESTS.md
   ```
   Si falta alguna, se arregla en la rama dueña, no aquí.
5. Mergea **una rama cada vez** y verifica antes de pasar a la siguiente:
   ```bash
   git merge --no-ff origin/<rama>
   ```
   Después de cada merge: instala dependencias si cambiaron, pasa tests y build, levanta la app y abre
   lo que aportó esa rama. Si algo falla, sabes qué rama lo causó. Nunca mergees varias de golpe.
6. Si hay conflicto, mira qué hace cada versión (`HEAD` es lo ya integrado; la otra, la rama que
   entra). Quédate con una, combínalas o escribe algo nuevo; quita los marcadores y cierra el merge:
   ```bash
   git add <archivo-resuelto>
   git commit
   ```
   Si resolverlo implica elegir un comportamiento o una regla de negocio, **DETENTE** y pregunta.
   Ojo también a los conflictos lógicos sin marcas: dos ramas que usan el mismo puerto, la misma
   variable global o el mismo nombre de archivo.
7. Si un merge rompe algo, deshazlo **en `integracion`**:
   ```bash
   git log --oneline --merges -10
   git revert -m 1 <sha-del-merge>
   ```
   Arregla el fallo en la rama original y vuelve a mergearla. Antes, revierte el revert
   (`git revert <sha-del-revert>`); si no, git solo traerá los commits nuevos.
   Si es más fácil empezar de cero, **pregunta antes** y borra solo la rama de integración; `main`
   sigue intacto:
   ```bash
   git checkout main
   git branch -D integracion
   ```
   Nunca uses `git reset --hard` sobre `main`.
8. Con todo mergeado, verifica el conjunto: tests (también los de punta a punta), linter y build.
   Después levanta la app y úsala: recorre lo que aportó cada tarea y, sobre todo, lo que cruza tareas
   (por ejemplo, entrar con un usuario, crear un producto y verlo en el reporte). En web, mira la consola.
9. Escribe el reporte de integración: ramas integradas y en qué orden, conflictos y cómo se
   resolvieron, reverts, qué verificaste y qué quedó pendiente.

   **DETENTE.** Presenta el reporte. Solo con la aprobación explícita de la persona lleva el resultado a
   `main`, o abre un pull request de `integracion` hacia `main` si el proyecto los usa:
   ```bash
   git checkout main
   git merge --no-ff integracion
   git push origin main
   ```
   Si hay CI, espera a que pase. Si la persona prefiere un commit por tarea, puede hacer squash al
   mergear; no lo decidas tú.
10. Limpieza, solo después de un push correcto y **preguntando antes**:
    ```bash
    git worktree remove ../proyecto-tarea-1
    git branch -d tarea-1-base
    ```
    Borrar ramas remotas (`git push origin --delete <rama>`) no tiene vuelta atrás: pregunta antes de
    cada una.

## Qué produce
- La rama `integracion` con todas las ramas mergeadas una a una y verificadas.
- Un reporte de integración: orden, conflictos, reverts y verificación.
- Con aprobación, `main` actualizado (o un pull request abierto) y worktrees y ramas limpios.

## Siguiente
- `/revision-independiente` si la persona quiere otra mirada sobre el resultado antes de llevarlo a `main`.
- `/reparto` para la siguiente ronda, con lo que hayas aprendido sobre conflictos y archivos compartidos.

Si necesitas un ejemplo completo, lee [references/ejemplo.md](references/ejemplo.md).
