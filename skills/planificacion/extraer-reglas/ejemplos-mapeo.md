# Ejemplos de Mapeo del Servicio Legacy

Esta referencia muestra ejemplos concretos para cada categoría de mapeo.

## a) Puntos de entrada

**Ejemplo:**
```
Punto de entrada: POST /api/inventory/receive
Parámetros: { warehouseId, productId, quantity, supplierId }
```

## b) Validaciones

**Ejemplo:**
```
Validaciones:
- quantity > 0
- product existe en catálogo
- Si product.needsApproval == true, requiere supplierId
```

## c) Cálculos y transformaciones

**Ejemplo:**
```
Cálculo de stock nuevo:
stock_nuevo = stock_actual + quantity
Si quantity > 100: aplicar descuento_volumen = 0.05
```

## d) Transiciones de estado

**Ejemplo:**
```
Estado del producto:
PENDING → RECEIVED → VERIFIED → AVAILABLE
No se puede ir de PENDING directo a AVAILABLE
```

## e) Permisos y autorización

**Ejemplo:**
```
Permisos:
- Solo rol WAREHOUSE_MANAGER puede recibir productos
- Si quantity > 1000, requiere aprobación de SUPERVISOR
```

## f) Manejo de errores

**Ejemplo:**
```
Error handling:
- Si falla actualización de stock: rollback transacción
- Si falla notificación: loguea error pero no bloquea
```

## g) Efectos secundarios

**Ejemplo:**
```
Side effects:
- Email a purchasing@empresa.com cuando stock < threshold
- Log en audit_log tabla
- Webhook a sistema de contabilidad
```

## h) Reglas implícitas

**Ejemplo:**
```
Reglas implícitas:
- Query filtra products.archived = false (nunca se procesan archivados)
- Config: max_receive_quantity = 10000 (hardcoded)
- Si supplier.region == "LATAM", usa exchange_rate de config
```
