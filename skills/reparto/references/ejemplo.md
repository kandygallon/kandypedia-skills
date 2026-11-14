# Ejemplo de reparto: app de inventario

## Especificación de entrada

> Una app web de gestión de inventario para un almacén. Debe tener inicio de sesión, listado de productos,
> alta y edición de productos, y un reporte de stock bajo.

## Análisis

Fronteras naturales:

- Autenticación: independiente.
- API de productos: independiente de la autenticación.
- Interfaz de productos: depende de la API y de la autenticación.
- Reporte de stock bajo: depende del modelo de productos.

## `docs/reparto.md` resultante

```markdown
# Reparto: app de inventario

## Resumen
Cuatro tareas: dos en la ronda 1 (autenticación y API de productos) y dos en la ronda 2
(interfaz de productos y reporte), que necesitan lo que produce la ronda 1.

## Ronda 1 (sin dependencias)

### Tarea 1: Autenticación
- Objetivo: registro e inicio de sesión de usuarios.
- Archivos que posee:
  - `src/components/Login.jsx`
  - `src/components/Register.jsx`
  - `src/services/auth.js`
  - `src/models/User.js`
- Archivos que lee:
  - `src/config/db.js`
- Dependencias: ninguna.
- Entregable: un usuario puede registrarse, iniciar sesión y ver su sesión activa.
- Verificación: `npm test -- auth.test.js` pasa y el inicio de sesión funciona en el navegador.

### Tarea 2: Modelo y API de productos
- Objetivo: crear, listar, editar y eliminar productos desde la API, sin interfaz.
- Archivos que posee:
  - `src/models/Product.js`
  - `src/controllers/products.js`
  - `src/routes/products.js`
  - `src/migrations/create-products.js`
- Archivos que lee:
  - `src/config/db.js`
- Dependencias: ninguna.
- Entregable: API REST de productos funcionando.
- Verificación: `curl` a cada endpoint devuelve los datos esperados.

## Ronda 2 (dependen de la ronda 1)

### Tarea 3: Interfaz de productos
- Objetivo: pantallas para gestionar productos.
- Archivos que posee:
  - `src/components/ProductList.jsx`
  - `src/components/ProductForm.jsx`
  - `src/pages/Products.jsx`
  - `src/styles/products.css`
- Archivos que lee:
  - `src/services/auth.js` (de la Tarea 1, para proteger rutas)
  - `src/models/Product.js` (de la Tarea 2)
- Dependencias: Tareas 1 y 2.
- Entregable: un usuario con sesión iniciada ve, añade, edita y elimina productos.
- Verificación: `http://localhost:3000/products` muestra la lista y el formulario guarda.

### Tarea 4: Reporte de stock bajo
- Objetivo: vista de los productos por debajo de un umbral.
- Archivos que posee:
  - `src/components/LowStockReport.jsx`
  - `src/pages/Reports.jsx`
  - `src/controllers/reports.js`
  - `src/routes/reports.js`
- Archivos que lee:
  - `src/models/Product.js` (de la Tarea 2)
  - `src/services/auth.js` (de la Tarea 1)
- Dependencias: Tarea 2.
- Entregable: un reporte con los productos de menos de 10 unidades.
- Verificación: `GET /api/reports/low-stock` devuelve la lista correcta.

## Archivos compartidos
- `src/config/routes.js` → dueño: Tarea 1. Las demás le piden añadir sus rutas.
- `src/App.jsx` → dueño: Tarea 3.

## Orden de integración
1. Tareas 1 y 2 (no se esperan conflictos).
2. Tarea 3 (necesita las dos anteriores).
3. Tarea 4 (puede ir en paralelo con la 3).
4. Prueba completa en la app: iniciar sesión, añadir productos, ver el reporte.

## Preguntas para la persona
- `src/config/db.js` lo leen las Tareas 1 y 2, pero no es de nadie. ¿Quién lo crea?
```

## Cómo se presenta para aprobar

> **DETENTE.** He escrito `docs/reparto.md`. Antes de seguir necesito que decidas:
> - `src/config/db.js` no tiene dueño. ¿Lo crea la Tarea 1 o la Tarea 2?
> - ¿Hay alguna parte de la especificación que ya no quieras hacer?

Cuando la persona responde, se actualiza el plan y se commitea en `main`:

```bash
git switch main
git add docs/reparto.md
git commit -m "docs: plan de reparto aprobado"
```
