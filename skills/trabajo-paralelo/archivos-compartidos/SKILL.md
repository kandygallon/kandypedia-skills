---
name: Archivos Compartidos
description: Usa esta skill para gestionar archivos que varios agentes necesitan modificar, evitando conflictos y sobrescrituras.
---

# Archivos Compartidos

## ¿Cuándo usar esta skill?

Úsala cuando:
- Dos o más tareas necesitan modificar el mismo archivo
- Hay archivos de configuración global que varias partes del código usan
- El plan de reparto identificó archivos compartidos
- Necesitas reglas claras de quién puede tocar qué

**No la uses** cuando:
- Cada tarea tiene archivos completamente independientes (ideal, no hay overlap)

## Filosofía

Los **archivos compartidos** son el mayor riesgo en trabajo paralelo. Si dos agentes modifican el mismo archivo al mismo tiempo, uno de dos:
1. **Git conflicto** al mergear (mejor caso: lo detectas)
2. **Sobrescritura silenciosa** (peor caso: un agente deshace el trabajo del otro sin darse cuenta)

Esta skill te da reglas para minimizar ese riesgo:
- **Owner claro**: un solo agente es dueño del archivo
- **Protocolo de cambios**: otros agentes piden cambios, no los hacen directamente
- **Integración temprana**: mergear archivos compartidos primero

## Tipos de archivos compartidos comunes

### 1. Configuración de rutas

**Ejemplos:**
- `routes.js`, `router.ts`, `urls.py`
- `App.jsx` (React Router), `app-routing.module.ts` (Angular)

**Por qué es compartido:**
- Cada tarea agrega sus propias rutas

**Solución:**
- Owner: la tarea que crea el archivo de rutas (usualmente backend o setup inicial)
- Otros: al terminar, piden al owner agregar su ruta

### 2. Configuración global

**Ejemplos:**
- `config.js`, `settings.py`, `.env`
- `package.json`, `requirements.txt`, `Gemfile`

**Por qué es compartido:**
- Cada tarea puede necesitar agregar dependencias o configuración

**Solución:**
- Owner: tarea de setup inicial
- Otros: documentan qué dependencia necesitan, el owner las agrega al final

### 3. Estilos globales

**Ejemplos:**
- `global.css`, `variables.scss`, `theme.js`
- `tailwind.config.js`

**Por qué es compartido:**
- Cada tarea puede agregar variables de color, tamaños, etc.

**Solución:**
- Owner: tarea de UI base
- Otros: usan las variables existentes; si necesitan nuevas, las piden

### 4. Types o interfaces compartidas

**Ejemplos:**
- `types.ts`, `models.ts`, `interfaces.py`

**Por qué es compartido:**
- Varias partes necesitan los mismos tipos

**Solución:**
- Owner: tarea que define los modelos de datos
- Otros: solo lectura; si necesitan cambios, los piden

### 5. Tests de integración

**Ejemplos:**
- `integration.test.js`, `e2e.spec.ts`

**Por qué es compartido:**
- Cada tarea puede agregar tests end-to-end

**Solución:**
- Owner: tarea de testing o integración final
- Otros: documentan qué tests faltan, el owner los escribe

## Protocolo de gestión

### Opción 1: Owner único (recomendado)

**Regla:**
- Un archivo compartido tiene **un solo owner** (una tarea/agente)
- Solo el owner puede modificarlo directamente
- Los demás piden cambios al owner

**Proceso:**

1. **En el plan de reparto**, identifica archivos compartidos y asigna un owner:

```markdown
## Archivos Compartidos

- `src/routes.js` → Owner: Tarea 1 (setup)
- `package.json` → Owner: Tarea 1 (setup)
- `src/types.ts` → Owner: Tarea 2 (modelos)
```

2. **Cada tarea no-owner** que necesite un cambio:
   - En su branch, crea un archivo `REQUESTS.md`:

```markdown
# Cambios necesarios en archivos compartidos

## src/routes.js
Agregar ruta:
```js
import ProductsPage from './pages/Products';
// ...
<Route path="/products" component={ProductsPage} />
```

## package.json
Agregar dependencia:
```json
"axios": "^1.6.0"
```
```

3. **El owner**, antes de mergear su branch:
   - Lee los `REQUESTS.md` de otras branches
   - Aplica los cambios en su branch
   - Commitea y pushea

4. **Al integrar**, la branch del owner se mergea primero (ver `/integrar`).

### Opción 2: Secciones delimitadas

Si el archivo es muy grande, puedes dividirlo en secciones:

**Ejemplo: `routes.js`**

```js
// ===== ROUTES: AUTH (owner: Task 1) =====
import Login from './pages/Login';
import Register from './pages/Register';

const authRoutes = [
  { path: '/login', component: Login },
  { path: '/register', component: Register },
];

// ===== ROUTES: PRODUCTS (owner: Task 2) =====
import ProductList from './pages/ProductList';
import ProductForm from './pages/ProductForm';

const productRoutes = [
  { path: '/products', component: ProductList },
  { path: '/products/:id', component: ProductForm },
];

// ===== ROUTES: REPORTS (owner: Task 3) =====
// (Task 3 agrega sus rutas aquí)

// ===== MAIN EXPORT =====
export default [...authRoutes, ...productRoutes];
```

**Regla:**
- Cada tarea solo toca su sección delimitada
- Al mergear, si no hubo cambios fuera de las secciones, git mergea limpio

**Riesgo:**
- Si alguien toca fuera de su sección, hay conflicto igual

### Opción 3: Merge temprano y rebase

**Proceso:**

1. La tarea owner del archivo compartido termina **primero** y mergea a `main`
2. Las otras tareas hacen **rebase** sobre `main` para tener los cambios
3. Cada una agrega su parte sobre la base actualizada

**Comandos:**

```bash
# Tarea owner termina y mergea
cd ~/proyecto
git checkout main
git merge task-1-setup
git push origin main

# Tarea 2 hace rebase
cd ~/proyecto-task-2
git fetch origin
git rebase origin/main
# Resuelve conflictos si hay
git push --force-with-lease origin task-2-products
```

**Ventaja:**
- Cada tarea trabaja sobre la versión actualizada

**Desventaja:**
- Interrumpe el paralelismo (hay que esperar al owner)

## Ejemplo completo

**Contexto:**

Tienes 3 tareas:
- Tarea 1: Setup (crea `routes.js`, `package.json`)
- Tarea 2: Productos (necesita agregar ruta `/products`)
- Tarea 3: Reportes (necesita agregar ruta `/reports`)

**Archivos compartidos identificados:**
- `src/routes.js` → Owner: Tarea 1
- `package.json` → Owner: Tarea 1

**Proceso:**

### Paso 1: Tarea 1 crea los archivos

En `proyecto-task-1/`:

```js
// src/routes.js
import Home from './pages/Home';

export default [
  { path: '/', component: Home },
];
```

Commitea y pushea:

```bash
git add .
git commit -m "feat(setup): crea archivo de rutas base"
git push origin task-1-setup
```

### Paso 2: Tarea 2 documenta su necesidad

En `proyecto-task-2/`, crea `REQUESTS.md`:

```markdown
# Cambios en archivos compartidos

## src/routes.js
Agregar:
```js
import ProductList from './pages/ProductList';
{ path: '/products', component: ProductList }
```
```

Commitea:

```bash
git add REQUESTS.md
git commit -m "docs: solicita ruta de productos"
git push origin task-2-products
```

### Paso 3: Tarea 3 hace lo mismo

En `proyecto-task-3/`, crea `REQUESTS.md`:

```markdown
# Cambios en archivos compartidos

## src/routes.js
Agregar:
```js
import Reports from './pages/Reports';
{ path: '/reports', component: Reports }
```
```

Commitea y pushea.

### Paso 4: Tarea 1 revisa las solicitudes

Antes de mergear, Tarea 1 revisa las otras branches:

```bash
cd ~/proyecto-task-1
git fetch origin

# Ver REQUESTS.md de Tarea 2
git show origin/task-2-products:REQUESTS.md

# Ver REQUESTS.md de Tarea 3
git show origin/task-3-reports:REQUESTS.md
```

Aplica los cambios en su `routes.js`:

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

Commitea:

```bash
git add src/routes.js
git commit -m "feat(routes): agrega rutas de productos y reportes"
git push origin task-1-setup
```

### Paso 5: Integración

Ahora al mergear:

```bash
cd ~/proyecto
git checkout main

# Merge Tarea 1 primero (tiene el archivo completo)
git merge task-1-setup

# Merge Tarea 2 (ya no toca routes.js, solo sus componentes)
git merge task-2-products

# Merge Tarea 3 (igual)
git merge task-3-reports

git push origin main
```

Sin conflictos, porque solo Tarea 1 tocó `routes.js`.

## Plantilla de REQUESTS.md

Cada tarea no-owner crea este archivo en su branch:

```markdown
# Solicitudes de cambios en archivos compartidos

## [nombre del archivo]

**Razón:**
[Por qué necesitas este cambio]

**Cambio solicitado:**
```[lenguaje]
[código a agregar o modificar]
```

**Owner:**
[Tarea X]

---

## [otro archivo si aplica]

...
```

## Estrategias por tipo de archivo

| Tipo de archivo | Estrategia recomendada |
|-----------------|------------------------|
| Rutas/Router | Owner único (setup) + REQUESTS.md |
| package.json / requirements.txt | Owner único (setup) + REQUESTS.md |
| Estilos globales | Owner único (UI base) + REQUESTS.md |
| Types/Interfaces | Owner único (modelos) + solo lectura |
| Config global | Owner único (setup) + REQUESTS.md |
| Tests e2e | Owner único (testing) + merge al final |

## Entregables

Al terminar de usar esta skill, debes tener:

1. ✅ **Lista de archivos compartidos** con owner asignado
2. ✅ **Protocolo claro**: qué hace el owner, qué hacen los demás
3. ✅ Archivo `REQUESTS.md` en cada branch no-owner (si aplica)
4. ✅ **Orden de merge** que prioriza owners de archivos compartidos

## Transición a las siguientes skills

Antes de mergear:
1. Cada agente usa `/antes-del-merge` para verificar que no tocó archivos que no debía
2. Owners de archivos compartidos revisan `REQUESTS.md` de otras branches y aplican cambios
3. Usa `/integrar` para mergear en orden (owners primero)

## Notas importantes

- **Menos archivos compartidos = mejor**: si puedes evitarlos con mejor diseño, hazlo
- **Owner no significa monopolio**: el owner solo coordina, no decide arbitrariamente
- **Documenta siempre**: un `REQUESTS.md` claro evita malentendidos
- **Mergear el owner primero** es clave para evitar conflictos
- **Si hay conflicto igual**: resuélvelo manual, no hay magia (ver `/integrar`)
