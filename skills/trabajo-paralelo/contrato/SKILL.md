---
name: contrato
description: Usa esta skill después de /reparto y antes de crear worktrees. Define el contrato exacto (entradas, salidas, errores) de cada punto de unión para que todos los agentes trabajen con la misma especificación.
---

# Contrato antes de paralelizar

## ¿Cuándo usar esta skill?

Úsala cuando:
- ✅ Ya dividiste el trabajo con `/reparto` en tareas paralelas
- ✅ Necesitas definir los contratos exactos entre las partes
- ✅ Vas a crear worktrees y múltiples agentes trabajarán en paralelo

**No la uses** cuando:
- ❌ Todavía no dividiste el trabajo (usa `/reparto` primero)
- ❌ Es una tarea individual sin puntos de unión con otras partes

## Filosofía

Vamos a dividir esta tarea entre varios agentes. Antes de escribir código, crea o actualiza el archivo `docs/contrato.md` con:

1. **Objetivo de la tarea** en 2 líneas.

2. **Partes en que se divide** y qué agente/worktree hace cada una.

3. **Para cada punto de unión** (endpoint, función, tipo, evento, tabla):
   - **Entrada**: campos, tipos, obligatorios u opcionales.
   - **Salida**: estructura exacta, con un ejemplo real en JSON o código.
   - **Errores**: cada caso de error con su código o excepción (ej. ID inexistente → 404) y un ejemplo de respuesta.

4. **Decisiones tomadas** y cosas que quedan **FUERA** del alcance.

5. **Preguntas abiertas** que yo debo decidir.

## Reglas

- **No escribas código de implementación todavía.**
- Si algo es ambiguo, ponlo en "Preguntas abiertas" en vez de asumirlo.
- **DETENTE** cuando termines y espera mi aprobación.

## Después de aprobado

1. **Commitea `docs/contrato.md` a la branch main** ANTES de crear worktrees:
   ```bash
   git add docs/contrato.md
   git commit -m "docs: agregar contrato para trabajo paralelo"
   git push origin main
   ```

2. Cada agente debe **leer `docs/contrato.md`** antes de empezar y **no puede cambiarlo** sin avisarme.

3. Si necesitas cambiar el contrato después:
   - Haz un **nuevo commit en main**
   - **Anuncia el cambio a todos los agentes** trabajando en worktrees
   - Cada agente debe hacer `git pull` en su worktree para ver el cambio

## Plantilla de contrato

```markdown
# Contrato: [Nombre de la tarea]

## Objetivo
[Describe en 2 líneas qué se va a construir y para qué]

## División de trabajo

### Agente 1 (worktree: ../proyecto-task-1)
[Qué implementa este agente]

### Agente 2 (worktree: ../proyecto-task-2)
[Qué implementa este agente]

### Agente 3 (worktree: ../proyecto-task-3)
[Qué implementa este agente]

## Puntos de unión

### 1. [Nombre del punto de unión]

**Tipo**: [Endpoint HTTP / Función / Tipo TypeScript / Evento / Tabla de BD]

**Responsable**: [Qué agente lo implementa]

**Entrada:**
```typescript
{
  campo1: string;           // obligatorio, descripción
  campo2?: number;          // opcional, descripción
  campo3: "A" | "B" | "C";  // obligatorio, valores posibles
}
```

**Salida exitosa:**
```json
{
  "id": 123,
  "status": "created",
  "data": {
    "campo1": "valor",
    "campo2": 42
  }
}
```

**Errores:**

- **400 Bad Request**: campo obligatorio faltante
  ```json
  {
    "error": "MISSING_FIELD",
    "message": "campo1 es obligatorio",
    "field": "campo1"
  }
  ```

- **404 Not Found**: recurso no existe
  ```json
  {
    "error": "NOT_FOUND",
    "message": "Recurso con id 123 no encontrado"
  }
  ```

- **500 Internal Server Error**: error inesperado
  ```json
  {
    "error": "INTERNAL_ERROR",
    "message": "Error interno del servidor"
  }
  ```

**Ejemplo completo:**
```bash
curl -X POST /api/recurso \
  -H "Content-Type: application/json" \
  -d '{"campo1": "test", "campo3": "A"}'

# Respuesta: 201 Created
{
  "id": 123,
  "status": "created",
  "data": {"campo1": "test", "campo2": null}
}
```

---

### 2. [Otro punto de unión]
[...mismo formato...]

## Decisiones tomadas

1. [Decisión técnica o de diseño que afecta a todos los agentes]
2. [Otra decisión]

## Fuera de alcance

1. [Cosa que NO se implementará en esta tarea]
2. [Otra cosa fuera de alcance]

## Preguntas abiertas

1. ¿[Pregunta que necesito que el usuario decida]?
2. ¿[Otra pregunta]?
```

## Ejemplo completo

**Usuario dice:**
> "Dividimos la app de inventario en 3 tareas: backend API, frontend UI y reportes."

**Agente (usando esta skill):**

Crea `docs/contrato.md`:

```markdown
# Contrato: App de Inventario

## Objetivo
Crear una aplicación de gestión de inventario con recepción de productos, listado y reporte de stock bajo.

## División de trabajo

### Agente 1 (worktree: ../inventario-task-1-api)
Implementa la API REST para productos: crear, listar, actualizar stock.

### Agente 2 (worktree: ../inventario-task-2-ui)
Implementa la interfaz de usuario: formularios y listados de productos.

### Agente 3 (worktree: ../inventario-task-3-reports)
Implementa el reporte de stock bajo con exportación a CSV.

## Puntos de unión

### 1. POST /api/products

**Tipo**: Endpoint HTTP REST

**Responsable**: Agente 1 (API)

**Entrada:**
```typescript
{
  name: string;           // obligatorio, nombre del producto
  sku: string;            // obligatorio, código único
  quantity: number;       // obligatorio, debe ser >= 0
  threshold?: number;     // opcional, umbral de stock bajo (default: 10)
}
```

**Salida exitosa:**
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

**Errores:**

- **400 Bad Request**: validación falla
  ```json
  {
    "error": "VALIDATION_ERROR",
    "message": "quantity debe ser mayor o igual a 0",
    "field": "quantity"
  }
  ```

- **409 Conflict**: SKU duplicado
  ```json
  {
    "error": "DUPLICATE_SKU",
    "message": "Ya existe un producto con SKU SKU-001"
  }
  ```

**Ejemplo completo:**
```bash
curl -X POST /api/products \
  -H "Content-Type: application/json" \
  -d '{"name": "Producto A", "sku": "SKU-001", "quantity": 100}'

# Respuesta: 201 Created
{
  "id": 456,
  "name": "Producto A",
  "sku": "SKU-001",
  "quantity": 100,
  "threshold": 10,
  "createdAt": "2026-11-10T14:30:00Z"
}
```

---

### 2. GET /api/products

**Tipo**: Endpoint HTTP REST

**Responsable**: Agente 1 (API)

**Entrada:**
```typescript
// Query params
{
  page?: number;      // opcional, default: 1
  limit?: number;     // opcional, default: 20, max: 100
  search?: string;    // opcional, busca en name o sku
}
```

**Salida exitosa:**
```json
{
  "data": [
    {
      "id": 456,
      "name": "Producto A",
      "sku": "SKU-001",
      "quantity": 100,
      "threshold": 10
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 1,
    "pages": 1
  }
}
```

**Errores:**

- **400 Bad Request**: parámetros inválidos
  ```json
  {
    "error": "INVALID_PARAMS",
    "message": "limit no puede ser mayor a 100"
  }
  ```

---

### 3. Tipo TypeScript compartido: Product

**Tipo**: TypeScript interface

**Responsable**: Agente 1 (API) crea, Agente 2 (UI) consume

**Definición:**
```typescript
// src/types/product.ts
export interface Product {
  id: number;
  name: string;
  sku: string;
  quantity: number;
  threshold: number;
  createdAt: string;  // ISO 8601
  updatedAt?: string; // ISO 8601
}
```

**Regla**: Agente 2 NO puede modificar este archivo, solo leerlo.

---

### 4. GET /api/reports/low-stock

**Tipo**: Endpoint HTTP REST

**Responsable**: Agente 3 (Reports)

**Entrada:**
```typescript
// Query params
{
  format?: "json" | "csv";  // opcional, default: "json"
}
```

**Salida exitosa (JSON):**
```json
{
  "products": [
    {
      "id": 456,
      "name": "Producto A",
      "sku": "SKU-001",
      "quantity": 8,
      "threshold": 10,
      "deficit": 2
    }
  ],
  "generatedAt": "2026-11-10T14:30:00Z"
}
```

**Salida exitosa (CSV):**
```csv
id,name,sku,quantity,threshold,deficit
456,Producto A,SKU-001,8,10,2
```

**Errores:**

- **400 Bad Request**: format inválido
  ```json
  {
    "error": "INVALID_FORMAT",
    "message": "format debe ser 'json' o 'csv'"
  }
  ```

## Decisiones tomadas

1. Los errores siempre devuelven un objeto con `error`, `message` y opcionalmente `field`.
2. Las fechas usan formato ISO 8601 (UTC).
3. El threshold por defecto es 10 unidades.
4. El límite máximo de paginación es 100.

## Fuera de alcance

1. Autenticación y permisos (se agrega en v2).
2. Imágenes de productos.
3. Historial de cambios de stock.

## Preguntas abiertas

1. ¿El campo `quantity` puede ser negativo para representar deuda de stock?
2. ¿El reporte de stock bajo debe enviar email automático o solo generarse on-demand?
```

**Agente dice:**
> **DETENTE**: he creado `docs/contrato.md`. Revísalo y dime si apruebas o hay que cambiar algo.

**Usuario aprueba:**
> "Perfecto, aprobado. Sobre las preguntas: quantity no puede ser negativo, y el reporte es solo on-demand."

**Agente:**
> Actualizo el contrato y lo commiteo a main:

```bash
git add docs/contrato.md
git commit -m "docs: contrato aprobado para app de inventario"
git push origin main
```

> ✅ Contrato commiteado. Ahora puedes crear los worktrees con `/worktrees` y cada agente leerá `docs/contrato.md` antes de empezar.

## Entregables

Al terminar de usar esta skill, debes tener:

1. ✅ Archivo `docs/contrato.md` completo con todos los puntos de unión
2. ✅ Aprobación del usuario
3. ✅ Commit en `main` con el contrato **antes de crear worktrees**
4. ✅ Preguntas abiertas resueltas

## Transición a las siguientes skills

Después de aprobar el contrato:
1. **Commitea `docs/contrato.md` a main** (no a una branch)
2. Usa `/worktrees` para crear espacios de trabajo
3. Cada agente en su worktree lee `@docs/contrato.md` antes de empezar
4. Usa `/tests-primero` en cada worktree para implementar con tests

## Relación con otras skills

- **Después de `/reparto`**: ese skill divide el trabajo, este skill define los contratos
- **Antes de `/worktrees`**: el contrato debe estar commiteado antes de crear espacios de trabajo
- **Referenciado en `/archivos-compartidos`**: `docs/contrato.md` es un archivo compartido especial (solo lectura para todos excepto coordinador)

## Notas importantes

- **Commitea a main antes de crear worktrees**: esto garantiza que todos los agentes parten de la misma versión
- **Cualquier cambio al contrato va por main**: no lo modifiques en una branch, siempre en main con anuncio a todos
- **Los ejemplos son vinculantes**: los ejemplos de entrada/salida en el contrato son tests que deben pasar
- **DETENTE y espera aprobación**: no sigas sin que el usuario revise el contrato
- **Preguntas abiertas antes de código**: si algo es ambiguo, pregunta, no asumas
