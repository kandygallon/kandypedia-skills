# Ejemplo: revisar una rama antes del merge

## La tarea en `docs/reparto.md`

```markdown
#### Tarea 2: API de productos
- **Entregable**: API REST para crear, listar, editar y borrar productos
- **Verificación**: `curl` a los endpoints devuelve datos correctos
- **Archivos que posee**:
  - `src/models/Product.js`
  - `src/controllers/products.js`
  - `src/routes/products.js`
```

## 1. Verificación de la tarea

```bash
curl http://localhost:3000/api/products
# Debe devolver una lista en JSON

curl -X POST http://localhost:3000/api/products \
  -H "Content-Type: application/json" \
  -d '{"name":"Prueba"}'
# Debe crear el producto y devolverlo con su id
```

Y además: abrir en el navegador la pantalla que usa esa API, crear un producto desde ella y mirar la
consola.

## 2. Fronteras

```bash
git fetch origin
git diff --stat origin/main...HEAD
```

```
 src/controllers/products.js | 84 ++++++++++
 src/models/Product.js       | 31 ++++
 src/routes/products.js      | 22 +++
 src/main.js                 | 47 +-----   ← no es de esta tarea
```

`src/main.js` no estaba en la lista y el diff es mucho más grande de lo que haría falta para registrar
una ruta. Se revisa:

```bash
git diff origin/main...HEAD -- src/main.js
```

Si el agente lo reescribió, se deshace y el cambio que hacía falta se pide al dueño en `REQUESTS.md`:

```bash
git checkout origin/main -- src/main.js
git commit -m "fix: deshace cambios en src/main.js, que no es de esta tarea" -- src/main.js
```

## 3. Commits

```bash
git log --oneline origin/main..HEAD
```

Buenos:

```
feat(products): modelo Product
feat(products): CRUD en el controlador
test(products): tests de la API
```

Malos, para avisar a la persona (no se reescriben por cuenta propia):

```
wip
fix
asdf
cambios
```

## 4. Script de comprobación opcional

Si el proyecto quiere automatizar parte de la lista, un script como este, adaptado a sus comandos.
No sustituye abrir la app ni revisar las fronteras.

```bash
#!/bin/bash
set -u

echo "Tests..."
npm test || { echo "Fallan los tests"; exit 1; }

echo "Linter..."
npm run lint || { echo "Falla el linter"; exit 1; }

if [[ -n $(git status --porcelain) ]]; then
  echo "Hay cambios sin commitear:"
  git status --short
  exit 1
fi

git fetch origin
if [[ -n $(git log HEAD..origin/main --oneline) ]]; then
  echo "Aviso: main tiene commits nuevos; trae los cambios con git merge origin/main"
fi

if git diff origin/main...HEAD | grep -qE "^\+.*console\.log"; then
  echo "Aviso: hay console.log nuevos"
fi

echo "Archivos cambiados (compáralos con docs/reparto.md):"
git diff --name-only origin/main...HEAD

echo "Ahora abre la app y usa la pantalla que tocaste."
```

## 5. Informe

```markdown
## Tarea 2: API de productos — lista para integrar

- Verificación de reparto: los cuatro endpoints responden bien con curl.
- App: creé, edité y borré un producto desde /productos. Consola sin errores.
- Tests: 18 pasan. `auth.test.js` ya fallaba en main (no es de esta tarea).
- Linter: sin errores en mis archivos.
- Fronteras: solo mis tres archivos. Deshice un cambio en `src/main.js`; la ruta la pedí en REQUESTS.md
  y la dueña ya la aplicó.
- Commits: dos se llaman «wip». No los he tocado; si quieres, squash al mergear.
- Rama subida: `tarea-2-productos`.
```
