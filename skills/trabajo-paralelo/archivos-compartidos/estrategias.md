# Estrategias Alternativas

## Opción 1: Owner único (recomendado)

Ver SKILL.md principal.

**Ventajas:** Simple, sin conflictos
**Desventajas:** Requiere coordinación

## Opción 2: Secciones delimitadas

Si el archivo es muy grande, divide en secciones:

```js
// ===== ROUTES: AUTH (owner: Task 1) =====
const authRoutes = [...]

// ===== ROUTES: PRODUCTS (owner: Task 2) =====
const productRoutes = [...]

// ===== MAIN EXPORT =====
export default [...authRoutes, ...productRoutes];
```

**Regla:** Cada tarea solo toca su sección

**Ventajas:** Menos coordinación
**Desventajas:** Si alguien toca fuera de su sección, hay conflicto

## Opción 3: Merge temprano y rebase

1. Tarea owner termina PRIMERO y mergea a main
2. Otras tareas hacen rebase sobre main
3. Cada una agrega su parte sobre base actualizada

```bash
# Owner termina y mergea
git merge task-1-setup

# Otras tareas rebase
cd ~/proyecto-task-2
git fetch origin
git rebase origin/main
git push --force-with-lease
```

**Ventajas:** Cada tarea trabaja sobre versión actualizada
**Desventajas:** Interrumpe paralelismo (hay que esperar)
