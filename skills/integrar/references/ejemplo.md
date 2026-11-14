# Ejemplo: integrar cuatro ramas

## Las ramas

- `tarea-1-base`: configuración y rutas. Dueña de `src/routes.js` y `package.json`.
- `tarea-2-productos`: API de productos.
- `tarea-3-ui`: pantallas de productos. Depende de la Tarea 2.
- `tarea-4-reportes`: reportes. Independiente.

## Orden propuesto a la persona

```
1. tarea-1-base       las demás dependen de ella y es dueña de archivos compartidos
2. tarea-2-productos  la interfaz depende de la API
3. tarea-3-ui
4. tarea-4-reportes   independiente; puede ir en cualquier punto después de la 1
```

La persona lo confirma antes de empezar.

## Merge uno a uno

```bash
git checkout main
git pull origin main
git checkout -b integracion

git fetch origin
git show origin/tarea-2-productos:REQUESTS.md   # ¿ya aplicado en tarea-1-base?

git merge --no-ff origin/tarea-1-base
npm install && npm test && npm run build
# levantar la app y abrir lo que aporta la Tarea 1

git merge --no-ff origin/tarea-2-productos
npm test && npm run build
# levantar la app y probar la API de productos
```

Y así con cada rama.

## Un conflicto

```js
<<<<<<< HEAD
const port = 3000;
=======
const port = 5000;
>>>>>>> origin/tarea-3-ui
```

`HEAD` es lo ya integrado; lo de abajo, la rama que entra. Aquí ninguna versión vale sola: la
resolución es leer el puerto de la configuración.

```js
const port = process.env.PORT || 3000;
```

```bash
git add src/server.js
git commit
npm test
```

Otros conflictos habituales:

- **«both modified»**: las dos ramas cambiaron las mismas líneas. Se resuelve a mano como arriba.
- **«both added»**: las dos crearon un archivo con el mismo nombre. Si deben ser uno, se combinan; si
  son cosas distintas, se renombra uno en su rama.
- **Tests que pasan por separado y fallan juntos**: conflicto lógico (mismo puerto, misma variable
  global, mismos datos de prueba). Se cambia una de las dos ramas para que no choquen.

Para entender de dónde viene cada lado:

```bash
git log --oneline --graph origin/tarea-1-base origin/tarea-3-ui
```

## Un merge que rompe algo sin conflicto

```bash
git log --oneline --merges -5
# a1b2c3d Merge remote-tracking branch 'origin/tarea-4-reportes' into integracion
git revert -m 1 a1b2c3d
```

Se arregla en `tarea-4-reportes`. Para volver a integrarla:

```bash
git revert <sha-del-revert>     # revierte el revert
git merge --no-ff origin/tarea-4-reportes
```

## Estrategias de merge

- **Merge con commit (`--no-ff`), la recomendada.** Conserva el historial y deja claro qué aportó
  cada rama. Permite deshacer una rama entera con `git revert -m 1`.
- **Squash.** Un commit por tarea; historial más limpio, se pierde el detalle. Lo decide la persona,
  normalmente al llevar `integracion` a `main` o al aprobar el pull request.
- **Rebase.** Historial lineal, pero reescribe la historia y multiplica los conflictos. No se usa para
  integrar ramas paralelas.

## Reporte de integración

```markdown
# Reporte de integración

## Ramas integradas, en orden
1. `tarea-1-base`: sin conflictos
2. `tarea-2-productos`: sin conflictos
3. `tarea-3-ui`: conflicto en `src/server.js` (puerto). Resuelto leyendo `PORT` de la configuración
4. `tarea-4-reportes`: rompía el listado de productos; revertido, arreglado en su rama y reintegrado

## Verificación
- Tests: 23 pasan, 0 fallan. Tests de punta a punta: pasan
- Linter y build: sin errores
- App: entré con un usuario, creé un producto, lo vi en la lista y salió en el reporte.
  Consola sin errores

## Pendiente
- Nada bloqueante. `auth.test.js` ya fallaba en main antes de integrar

## Para decidir
- ¿Llevo `integracion` a `main` o abro un pull request?
- ¿Merge con commit o squash?
```

## Cierre, con aprobación

```bash
git checkout main
git merge --no-ff integracion
git push origin main
```

Limpieza, preguntando antes:

```bash
git worktree remove ../proyecto-tarea-1
git worktree remove ../proyecto-tarea-2
git branch -d tarea-1-base tarea-2-productos
git push origin --delete tarea-1-base    # solo si la persona lo confirma
```

## Lista rápida

```markdown
- [ ] Todas las ramas pasaron /antes-del-merge y están subidas
- [ ] Rama `integracion` creada desde main actualizado
- [ ] La persona confirmó el orden de merge
- [ ] Una rama cada vez, verificando después de cada una
- [ ] Conflictos resueltos; los de negocio, preguntados
- [ ] App levantada y usada con todo junto
- [ ] Reporte presentado
- [ ] La persona aprobó llevarlo a main (o el pull request)
- [ ] Limpieza hecha, preguntando antes
```
