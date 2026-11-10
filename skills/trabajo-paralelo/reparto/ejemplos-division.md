# Ejemplos de División

## Ejemplo 1: App de Inventario

**Especificación:** App web con login, CRUD de productos, reporte de stock bajo.

**División:**

### Ronda 1 (paralelo)
- **Tarea 1: Autenticación**
  - Archivos: `src/components/Login.tsx`, `src/services/auth.ts`, `src/models/User.ts`
  - Entregable: Usuario puede registrarse y hacer login

- **Tarea 2: Modelo y API de Productos**
  - Archivos: `src/models/Product.ts`, `src/controllers/products.ts`, `src/routes/products.ts`
  - Entregable: API REST para CRUD de productos

### Ronda 2 (después de Ronda 1)
- **Tarea 3: UI de Productos**
  - Depende de: Tarea 1 (auth) y Tarea 2 (API)
  - Archivos: `src/components/ProductList.tsx`, `src/pages/Products.tsx`
  - Entregable: Usuario logueado puede gestionar productos desde UI

- **Tarea 4: Reporte**
  - Depende de: Tarea 2 (modelo)
  - Archivos: `src/components/LowStockReport.tsx`, `src/controllers/reports.ts`
  - Entregable: Reporte de productos con stock bajo

**Archivos compartidos:**
- `src/App.tsx` → Owner: Tarea 3
- `src/types/product.ts` → Owner: Tarea 2

## Ejemplo 2: Migración de Servicio

**Especificación:** Migrar servicio de pedidos con 20 reglas de negocio.

**División:**

### Ronda 1
- **Tarea 1: Validaciones (R-001 a R-010)**
  - Implementa reglas de validación de entrada

- **Tarea 2: Cálculos (R-011 a R-015)**
  - Implementa reglas de cálculos de negocio

- **Tarea 3: Estados (R-016 a R-020)**
  - Implementa reglas de transiciones de estado

**Integración:** Todas en paralelo, verificar que cada test de regla pasa.

## Ejemplo 3: Landing Page

**Especificación:** Landing page con 5 secciones.

**División:**

**NO dividir en paralelo:** Es pequeño, un solo agente es más rápido que coordinar.

Mejor: Implementa todo en una tarea.
