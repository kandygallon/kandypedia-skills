---
name: antes-del-merge
description: Revisa una rama terminada antes de darla por lista para mergear: que cumple su tarea de docs/reparto.md, que la app abre y funciona, que pasan tests y linter y que no tocó archivos ajenos. Úsala al acabar tu tarea, antes del último push o de decir «mi parte está lista».
---

# Antes del merge

> **De dónde sale.** Le pedí a un agente que añadiera dos tipografías. Reescribió el archivo de entrada
> entero y se llevó por delante el punto donde se monta la aplicación. No hubo conflictos, porque no había
> con quién chocar. El build pasó y Vercel salió en verde. Y la web estaba caída. Ese día revisé después de
> subir, no antes. Esta lista es lo que reviso ahora, antes.

## Cuándo usarla
- Terminaste tu tarea y vas a hacer el último push de tu rama.
- Vas a avisar de que tu parte está lista para `/integrar`.
- Arreglaste algo que salió en la revisión y hay que volver a comprobar la rama.

**No la uses** si todavía estás a mitad de la tarea.

## Pasos
1. Lee tu tarea en `docs/reparto.md`: objetivo, entregable, verificación y «Archivos que posee».
   Comprueba punto por punto que hiciste todo lo que pide y ejecuta la verificación que indica.
2. **Abre la app y úsala.** Que pasen el build y los tests no basta:
   - Levanta el proyecto y abre la pantalla o la ruta que tocaste.
   - Comprueba que carga, que se ve y que hace lo que tiene que hacer.
   - Si es una web, mira la consola del navegador: un error ahí puede dejar la página en blanco con
     el build en verde.

   Si algo no funciona, no sigas: arréglalo primero.
3. Pasa los tests y el build del proyecto (`npm test`, `pytest`, `cargo test`…). Si uno falla, mira
   si ya fallaba en `main`. Si lo rompiste tú, arréglalo; no se mergea con un test roto por ti. Si ya
   estaba roto, anótalo para el informe.
4. Pasa el linter (`npm run lint`, `flake8`…). Tu código nuevo no puede tener errores. Puedes usar el
   autoarreglo del proyecto solo sobre tus archivos. No desactives reglas ni toques la configuración del
   linter: si crees que está mal, dilo en el informe.
5. Comprueba las fronteras. Lista lo que cambiaste y compáralo con tus archivos de `docs/reparto.md`:
   ```bash
   git fetch origin
   git diff --stat origin/main...HEAD
   ```
   - Archivo compartido con dueño: deshaz tu cambio y pídelo en `REQUESTS.md` (ver `/archivos-compartidos`).
   - Archivo de otra tarea: deshaz tu cambio con `git checkout origin/main -- <archivo>`.
   - Archivo nuevo que no era de nadie: anótalo para el informe.
   - `REQUESTS.md`: cuando el dueño confirme que lo aplicó, bórralo con `git rm REQUESTS.md`.

   Mira también el tamaño. Si un cambio pequeño reescribió un archivo entero (el de entrada, la
   configuración, un índice), revisa ese diff línea por línea:
   ```bash
   git diff origin/main...HEAD -- <archivo>
   ```
6. Comprueba que no queda nada sin commitear con `git status`. Añade los archivos por su ruta, nunca
   todo de golpe. Lo que no deba ir, descártalo con `git checkout -- <archivo>` o, si es generado,
   añádelo a `.gitignore`.
   ```bash
   git add src/products/list.js src/products/list.test.js
   git commit -m "feat(products): lista de productos con filtro"
   ```
7. Limpia lo temporal en los archivos que tocaste: `console.log`, `print`, `debugger`, TODOs sin
   explicar y archivos de prueba sueltos.
   ```bash
   git diff origin/main...HEAD | grep -nE "^\+.*(console\.log|debugger|TODO)"
   ```
8. Revisa los mensajes de los commits con `git log --oneline origin/main..HEAD`. Si hay mensajes como
   «wip», «fix» o «cambios», dile a la persona cuáles y por qué. **No reescribas la historia por tu
   cuenta**: nada de `git rebase -i` ni `git reset --soft`. Decide ella; si quiere juntarlos, puede
   hacer squash al mergear.
9. Trae lo nuevo de `main`, si lo hay:
   ```bash
   git log HEAD..origin/main --oneline
   git merge origin/main
   ```
   Si hay conflictos en archivos tuyos, resuélvelos. Si son en archivos ajenos, **DETENTE** y avisa.
   Después del merge, repite los pasos 2 a 4.
10. Documenta lo que no es obvio: decisiones («usamos X y no Y porque…»), configuración que hace falta
    para que funcione y cualquier API o comando nuevo. No comentes lo evidente.
11. Sube tu rama: `git push -u origin <rama>` (o `git push` si ya existe en el remoto).
12. Escribe el informe para la persona: qué hiciste, cómo lo comprobaste, qué falló antes y está
    arreglado, y las excepciones (tests que ya fallaban, archivos nuevos, commits con mal mensaje).

**DETENTE.** No mergees tú. Entrega el informe y espera: la integración la hace `/integrar` con la
aprobación de la persona.

## Qué produce
- Una rama subida que cumple su tarea, funciona al abrir la app, pasa tests y linter y solo toca sus
  archivos.
- Un informe corto con lo comprobado y las excepciones.

## Siguiente
- Si eres dueño de un archivo compartido, aplica los `REQUESTS.md` de las demás ramas (`/archivos-compartidos`).
- `/integrar` cuando todas las ramas hayan pasado esta lista.

Si necesitas un ejemplo completo, lee [references/ejemplo.md](references/ejemplo.md).
