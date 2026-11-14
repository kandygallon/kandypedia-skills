# Ejemplo de contrato

## Plantilla

````markdown
# Contrato: <nombre de la tarea>

## Objetivo
<qué se construye y para qué, en 2 líneas>

## Partes
- Agente 1 (worktree `../proyecto-task-1`): <qué implementa>
- Agente 2 (worktree `../proyecto-task-2`): <qué implementa>

## Puntos de unión

### 1. <nombre>
- Tipo: <endpoint HTTP / función / tipo / evento / tabla>
- Responsable: <quién lo implementa> · Lo usa: <quién lo consume>

**Entrada**
```typescript
{
  campo1: string;           // obligatorio
  campo2?: number;          // opcional
  campo3: "A" | "B" | "C";  // obligatorio, valores posibles
}
```

**Salida**
```json
{ "id": 123, "status": "created", "data": { "campo1": "valor", "campo2": 42 } }
```

**Errores**
- 400: falta un campo obligatorio
  ```json
  { "error": "MISSING_FIELD", "message": "campo1 es obligatorio", "field": "campo1" }
  ```
- 404: el recurso no existe
  ```json
  { "error": "NOT_FOUND", "message": "No existe el recurso 123" }
  ```

## Decisiones tomadas
1. ...

## Fuera del alcance
1. ...

## Preguntas abiertas
1. ¿...?
````

## Ejemplo: app de inventario

Entrada: el trabajo ya se repartió en tres tareas (API, interfaz y reportes) en `docs/reparto.md`.

````markdown
# Contrato: app de inventario

## Objetivo
Gestión de inventario con alta de productos, listado y reporte de stock bajo.

## Partes
- Agente 1 (worktree `../inventario-task-1`): API REST de productos.
- Agente 2 (worktree `../inventario-task-2`): interfaz de alta y listado.
- Agente 3 (worktree `../inventario-task-3`): reporte de stock bajo con exportación a CSV.

## Puntos de unión

### 1. POST /api/products
- Tipo: endpoint HTTP
- Responsable: Agente 1 · Lo usa: Agente 2

**Entrada**
```typescript
{
  name: string;        // obligatorio
  sku: string;         // obligatorio, único
  quantity: number;    // obligatorio, >= 0
  threshold?: number;  // opcional, umbral de stock bajo (por defecto, 10)
}
```

**Salida** (201)
```json
{
  "id": 456,
  "name": "Producto A",
  "sku": "SKU-001",
  "quantity": 100,
  "threshold": 10,
  "createdAt": "2026-11-10T14:30:00Z"
}
```

**Errores**
- 400: falla la validación
  ```json
  { "error": "VALIDATION_ERROR", "message": "quantity debe ser mayor o igual a 0", "field": "quantity" }
  ```
- 409: SKU repetido
  ```json
  { "error": "DUPLICATE_SKU", "message": "Ya existe un producto con SKU SKU-001" }
  ```

### 2. GET /api/products
- Tipo: endpoint HTTP
- Responsable: Agente 1 · Lo usa: Agente 2

**Entrada** (parámetros de consulta)
```typescript
{
  page?: number;    // por defecto, 1
  limit?: number;   // por defecto, 20; máximo, 100
  search?: string;  // busca en name o sku
}
```

**Salida** (200)
```json
{
  "data": [{ "id": 456, "name": "Producto A", "sku": "SKU-001", "quantity": 100, "threshold": 10 }],
  "pagination": { "page": 1, "limit": 20, "total": 1, "pages": 1 }
}
```

**Errores**
- 400: parámetros inválidos
  ```json
  { "error": "INVALID_PARAMS", "message": "limit no puede ser mayor que 100" }
  ```

### 3. Tipo compartido `Product`
- Tipo: interfaz TypeScript en `src/types/product.ts`
- Responsable: Agente 1 · Lo usan: Agentes 2 y 3, solo lectura

```typescript
export interface Product {
  id: number;
  name: string;
  sku: string;
  quantity: number;
  threshold: number;
  createdAt: string;   // ISO 8601
  updatedAt?: string;  // ISO 8601
}
```

### 4. GET /api/reports/low-stock
- Tipo: endpoint HTTP
- Responsable: Agente 3

**Entrada** (parámetros de consulta)
```typescript
{ format?: "json" | "csv" }  // por defecto, "json"
```

**Salida** (200, JSON)
```json
{
  "products": [{ "id": 456, "name": "Producto A", "sku": "SKU-001", "quantity": 8, "threshold": 10, "deficit": 2 }],
  "generatedAt": "2026-11-10T14:30:00Z"
}
```

**Salida** (200, CSV)
```csv
id,name,sku,quantity,threshold,deficit
456,Producto A,SKU-001,8,10,2
```

**Errores**
- 400: formato inválido
  ```json
  { "error": "INVALID_FORMAT", "message": "format debe ser 'json' o 'csv'" }
  ```

## Decisiones tomadas
1. Todos los errores devuelven `error`, `message` y, si aplica, `field`.
2. Las fechas van en ISO 8601, en UTC.
3. El umbral por defecto es 10 unidades.
4. La paginación admite como máximo 100 resultados.

## Fuera del alcance
1. Autenticación y permisos.
2. Imágenes de productos.
3. Historial de cambios de stock.

## Preguntas abiertas
1. ¿`quantity` puede ser negativa para representar stock comprometido?
2. ¿El reporte de stock bajo se envía por correo o solo se genera cuando se pide?
````

## Aprobación y commit

El agente se detiene:

> **DETENTE.** He escrito `docs/contrato.md`. Revísalo y responde las dos preguntas abiertas.

La persona responde: `quantity` no puede ser negativa y el reporte solo se genera cuando se pide. El agente
pasa las dos respuestas a «Decisiones tomadas», vacía «Preguntas abiertas» y commitea en `main`:

```bash
git switch main
git add docs/contrato.md
git commit -m "docs: contrato aprobado para la app de inventario"
```

Después, `/worktrees`. Cada agente lee `docs/contrato.md` antes de empezar.
