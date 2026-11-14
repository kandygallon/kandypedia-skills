---
name: archivos-compartidos
description: Da un solo dueño a cada archivo que varias tareas paralelas necesitan tocar (rutas, dependencias, estilos, tipos, traducciones) y fija cómo piden cambios las demás. Úsala al repartir el trabajo o cuando necesitas cambiar un archivo que no es tuyo.
---

# Archivos compartidos

> **De dónde sale.** En el rediseño de kandypedia.com, los cuatro agentes iban a tocar el mismo archivo:
> `translations.js`, con todos los textos del sitio en tres idiomas. Antes de lanzar a ninguno, lo partí en
> 15 archivos, uno por sección, y comprobé que el resultado era idéntico al original. Al juntar las cuatro
> ramas no hubo ni un conflicto. El trabajo de verdad no fue escribir prompts: fue conseguir que ningún
> archivo tuviera dos dueños.

## Cuándo usarla
- El reparto (`docs/reparto.md`) tiene archivos que aparecen en más de una tarea, o en ninguna.
- Hay archivos globales que todas las partes tocan: rutas, `package.json`, configuración, estilos, tipos.
- Estás trabajando en tu rama y necesitas cambiar un archivo que no es tuyo.

**No la uses** si cada tarea tiene archivos totalmente separados: no hay nada que coordinar.

## Pasos
1. Lee `docs/reparto.md` y cruza los «Archivos que posee» de cada tarea. Apunta los que salen en dos
   o más tareas y los que no son de nadie. Revisa también los sospechosos habituales:

   | Tipo | Ejemplos | Dueño típico |
   |---|---|---|
   | Rutas | `routes.js`, `urls.py`, `App.jsx` | Tarea de base o configuración |
   | Dependencias y configuración | `package.json`, `requirements.txt`, `config.js` | Tarea de base |
   | Estilos globales | `global.css`, `tailwind.config.js` | Tarea de interfaz base |
   | Tipos y modelos | `types.ts`, `models.ts` | Tarea de modelos (los demás, solo lectura) |
   | Textos y traducciones | `translations.js`, `i18n/` | Tarea de contenido |
   | Tests de punta a punta | `e2e.spec.ts` | Tarea de integración o tests |

2. Elimina todos los compartidos que puedas antes de repartir. Lo mejor es partir el archivo en uno
   por sección o por tarea, para que cada parte tenga un dueño. Comprueba que el resultado es idéntico
   al original (mismo build, misma salida, mismos tests) y commitea la partición antes de crear worktrees.
3. A cada compartido que quede, ponle **un solo dueño**: solo esa tarea lo modifica. Las demás lo leen
   y, si necesitan algo, lo piden.
4. Elige el protocolo para cada archivo:
   - **A. Dueño único con `REQUESTS.md` (recomendado).** Las demás tareas no lo tocan: anotan el cambio
     exacto en un `REQUESTS.md` en la raíz de su rama y el dueño lo aplica.
   - **B. Secciones delimitadas.** Si no se puede partir, marca en el archivo una sección por tarea
     (`// ===== RUTAS: PRODUCTOS (dueña: Tarea 2) =====`) y cada tarea escribe solo dentro de la suya.
     Si alguien toca fuera de su sección, habrá conflicto igual.
   - **C. Integración temprana.** El dueño termina primero, su rama se integra con `/integrar` y las demás
     traen esos cambios con `git merge origin/main`. Es lo más seguro, pero frena el paralelismo.
5. Anótalo en la sección «Archivos compartidos» de `docs/reparto.md`: archivo, dueño y protocolo. En
   la sección de integración, pon primero las ramas de los dueños.

   **DETENTE.** Presenta la lista de compartidos, sus dueños y el protocolo. La persona confirma antes
   de commitear el reparto en `main` y crear los worktrees.

6. Si estás trabajando y necesitas un archivo que no es tuyo, no lo edites. Añade a `REQUESTS.md` en
   tu rama el archivo, el motivo, el cambio exacto y la tarea dueña, y commitéalo por su ruta:
   ```bash
   git add REQUESTS.md
   git commit -m "docs: pide ruta /products a la tarea de base"
   git push
   ```
   Sigue con tu tarea contando con ese cambio, pero sin hacerlo tú.
7. Si eres la tarea dueña, antes de cerrar tu rama lee las peticiones de las demás:
   ```bash
   git fetch origin
   git show origin/<rama-de-otra-tarea>:REQUESTS.md
   ```
   Aplica las que encajan, commitea el archivo por su ruta y empuja. Si una petición choca con otra o
   cambia algo de negocio, no decidas tú: **DETENTE** y pregúntale a la persona.
8. Cuando el dueño confirme que aplicó tu petición, borra `REQUESTS.md` de tu rama (`git rm REQUESTS.md`)
   para que no llegue a la integración ni choque con el de otras ramas.

## Qué produce
- Sección «Archivos compartidos» en `docs/reparto.md`: cada archivo con un solo dueño y su protocolo.
- Orden de integración con las ramas de los dueños primero.
- `REQUESTS.md` en las ramas que necesitan cambios en archivos ajenos, hasta que el dueño los aplica.

## Siguiente
- `/worktrees` para crear un worktree por tarea con el reparto ya commiteado.
- `/antes-del-merge` para comprobar que cada rama solo tocó sus archivos.
- `/integrar` para juntar las ramas, dueños primero.

Si necesitas un ejemplo completo, lee [references/ejemplo.md](references/ejemplo.md).
