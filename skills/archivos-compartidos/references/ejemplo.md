# Ejemplo: un archivo de rutas con un solo dueño

## Contexto

Tres tareas en paralelo:

- **Tarea 1, base:** crea `src/routes.js` y `package.json`.
- **Tarea 2, productos:** necesita la ruta `/products`.
- **Tarea 3, reportes:** necesita la ruta `/reports`.

Archivos compartidos y dueños, tal como quedan en `docs/reparto.md`:

```markdown
## Archivos compartidos

- `src/routes.js` → Dueña: Tarea 1 (base). Protocolo: dueño único con REQUESTS.md
- `package.json` → Dueña: Tarea 1 (base). Protocolo: dueño único con REQUESTS.md
- `src/types.ts` → Dueña: Tarea 2 (productos). Los demás, solo lectura

## Integración final

1. Tarea 1 (dueña de rutas y dependencias)
2. Tarea 2 y Tarea 3, en cualquier orden
```

## Paso 1: la Tarea 1 crea el archivo base

```js
// src/routes.js
import Home from './pages/Home';

export default [
  { path: '/', component: Home },
];
```

```bash
git add src/routes.js
git commit -m "feat(base): crea el archivo de rutas"
git push origin tarea-1-base
```

## Paso 2: la Tarea 2 pide su ruta

No toca `src/routes.js`. Crea `REQUESTS.md` en la raíz de su rama:

````markdown
# Peticiones de cambios en archivos compartidos

## src/routes.js

**Motivo:** la pantalla de productos necesita su ruta.

**Cambio pedido:**
```js
import ProductList from './pages/ProductList';
{ path: '/products', component: ProductList }
```

**Dueña:** Tarea 1
````

```bash
git add REQUESTS.md
git commit -m "docs: pide la ruta /products a la tarea de base"
git push origin tarea-2-productos
```

## Paso 3: la Tarea 3 hace lo mismo

Su `REQUESTS.md` pide `{ path: '/reports', component: Reports }` y la dependencia que necesite:

````markdown
## package.json

**Motivo:** los reportes exportan a CSV.

**Cambio pedido:**
```json
"papaparse": "^5.4.0"
```

**Dueña:** Tarea 1
````

## Paso 4: la Tarea 1 aplica las peticiones

```bash
git fetch origin
git show origin/tarea-2-productos:REQUESTS.md
git show origin/tarea-3-reportes:REQUESTS.md
```

```js
// src/routes.js
import Home from './pages/Home';
import ProductList from './pages/ProductList';
import Reports from './pages/Reports';

export default [
  { path: '/', component: Home },
  { path: '/products', component: ProductList },
  { path: '/reports', component: Reports },
];
```

```bash
git add src/routes.js package.json
git commit -m "feat(rutas): añade productos y reportes"
git push origin tarea-1-base
```

Las tareas 2 y 3, cuando la dueña confirma, quitan su `REQUESTS.md`:

```bash
git rm REQUESTS.md
git commit -m "docs: quita peticiones ya aplicadas"
git push
```

## Paso 5: integración

Con `/integrar`, en la rama `integracion` y en este orden: primero `tarea-1-base`, que ya tiene el
archivo completo; después `tarea-2-productos` y `tarea-3-reportes`, que no tocan `routes.js`. No hay
conflictos porque ese archivo solo tuvo una dueña.

## Variante: secciones delimitadas

Si el archivo no se puede partir y el protocolo es de secciones:

```js
// ===== RUTAS: AUTENTICACIÓN (dueña: Tarea 1) =====
import Login from './pages/Login';
const authRoutes = [{ path: '/login', component: Login }];

// ===== RUTAS: PRODUCTOS (dueña: Tarea 2) =====
import ProductList from './pages/ProductList';
const productRoutes = [{ path: '/products', component: ProductList }];

// ===== RUTAS: REPORTES (dueña: Tarea 3) =====
const reportRoutes = []; // La Tarea 3 añade sus rutas aquí.

// ===== EXPORTACIÓN (dueña: Tarea 1) =====
export default [...authRoutes, ...productRoutes, ...reportRoutes];
```

La dueña deja creada la sección de cada tarea, aunque esté vacía, para que nadie tenga que tocar la
exportación.

Cada tarea escribe solo dentro de su bloque. Si las secciones no están separadas por varias líneas sin
tocar, git puede marcar conflicto igual: deja líneas en blanco entre bloques.

## Variante: partir el archivo

Lo que se hizo con `translations.js`: en vez de un archivo con todos los textos, uno por sección
(`i18n/inicio.js`, `i18n/precios.js`…) y un índice que los junta. Antes de repartir, comprueba que el
objeto resultante es idéntico al original, por ejemplo comparando la salida serializada de ambos.
