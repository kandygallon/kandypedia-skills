# Ejemplos de Puntos de Unión

## 1. Endpoint HTTP REST

### POST /api/products

**Tipo**: Endpoint HTTP

**Responsable**: Agente 1 (API)

**Entrada:**
```typescript
{
  name: string;
  sku: string;
  quantity: number;
  threshold?: number;  // default: 10
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
  "createdAt": "2026-11-10T20:00:00Z"
}
```

**Errores:**
- **400**: validación falla
- **409**: SKU duplicado

---

## 2. Función TypeScript compartida

### calculateDiscount()

**Tipo**: Función

**Responsable**: Agente 1 (crea), Agente 2 (usa)

**Definición:**
```typescript
// src/utils/pricing.ts
export function calculateDiscount(
  quantity: number,
  unitPrice: number
): number {
  if (quantity >= 100) {
    return unitPrice * 0.95; // 5% descuento
  }
  return unitPrice;
}
```

**Entrada:** `quantity: 150, unitPrice: 10.00`
**Salida:** `9.50`

**Regla**: Agente 2 NO modifica esta función.

---

## 3. Tipo TypeScript compartido

### Product interface

**Tipo**: TypeScript interface

**Responsable**: Agente 1 (define), otros (usan)

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
  updatedAt?: string;
}
```

**Regla**: Solo Agente 1 puede modificar.

---

## 4. Evento

### ProductReceived

**Tipo**: Evento

**Responsable**: Agente 1 (emite), Agente 3 (escucha)

**Payload:**
```typescript
{
  type: "PRODUCT_RECEIVED";
  payload: {
    productId: number;
    quantity: number;
    timestamp: string;
  };
}
```

**Cuándo se emite**: Después de actualizar stock exitosamente.

**Qué debe hacer el listener**: Generar reporte si stock < threshold.

---

## 5. Tabla de BD compartida

### products table

**Tipo**: Tabla de base de datos

**Responsable**: Agente 1 (escribe), Agente 2 (lee)

**Esquema:**
```sql
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  sku VARCHAR(50) UNIQUE NOT NULL,
  quantity INTEGER DEFAULT 0,
  threshold INTEGER DEFAULT 10,
  archived BOOLEAN DEFAULT false,
  created_at TIMESTAMP DEFAULT NOW()
);
```

**Regla**: Solo Agente 1 hace INSERT/UPDATE. Agente 2 solo SELECT.
