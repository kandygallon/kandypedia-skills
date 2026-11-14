# Ejemplo: extraer reglas

Todos los nombres, rutas y datos de este ejemplo son inventados.

## Qué buscar en cada categoría

```
Punto de entrada:     POST /api/inventory/receive
Parámetros:           { warehouseId, productId, quantity, supplierId }

Validaciones:         quantity > 0
                      el producto existe en el catálogo
                      si product.needsApproval == true, supplierId es obligatorio

Cálculo:              stock_nuevo = stock_actual + quantity
                      si quantity >= 100: descuento por volumen del 5 %

Estados:              PENDING → RECEIVED → VERIFIED → AVAILABLE
                      no se puede pasar de PENDING a AVAILABLE directamente

Permisos:             solo el rol WAREHOUSE_MANAGER puede recibir productos
                      si quantity > 1000, hace falta aprobación de SUPERVISOR

Errores:              si falla la actualización de stock: rollback de la transacción
                      si falla la notificación: se registra el error, pero no bloquea

Efectos secundarios:  correo a compras cuando el stock baja del umbral
                      registro en la tabla audit_log
                      webhook al sistema de contabilidad

Reglas implícitas:    la consulta filtra products.archived = false (nunca procesa archivados)
                      max_receive_quantity = 10000, fijo en la configuración
                      si supplier.region == "LATAM", usa el tipo de cambio de la configuración
```

## Plantilla de una regla

````markdown
### R-001: [nombre descriptivo]

**Descripción:** [qué hace, en lenguaje de negocio, sin tecnicismos]

**Ubicación en el legacy:**
- Archivo: `src/services/inventory/receive.service.ts`, función `processReceipt()`, líneas 45-67
- También en: `src/validators/inventory.validator.ts`, líneas 12-15

**Entrada esperada:**
```json
{ "warehouseId": 123, "productId": 456, "quantity": 50 }
```

**Salida esperada:**
```json
{ "newStock": 150, "receipt": { "id": 789, "status": "RECEIVED" } }
```

**Casos especiales:**
- quantity == 0: rechaza con «La cantidad debe ser positiva»
- el producto no existe: rechaza con «Producto no encontrado»
- el almacén está cerrado: rechaza con «Almacén cerrado»

**Estado para el servicio nuevo** (propone el agente, decide la persona):
- [ ] Mantener: igual que en el legacy
- [ ] Cambiar: distinto (explica cómo)
- [ ] Eliminar: ya no hace falta
- [ ] Por confirmar: hay que preguntar a quien conoce el dominio

**Notas:** [rarezas, bugs sospechosos, decisiones históricas]
````

## Plantilla de una duda

```markdown
### D-001: ¿Bug o comportamiento intencional?

**Comportamiento observado:** si `supplierId` es nulo, el sistema acepta la recepción pero no
registra el proveedor.

**Código:** `src/services/inventory/receive.service.ts`, línea 89

**Pregunta:** ¿se permite a propósito recibir sin proveedor o es un bug que nunca se detectó?

**Impacto:**
- Si es un bug: el servicio nuevo exige `supplierId`.
- Si es intencional: el servicio nuevo mantiene esa flexibilidad.
```

## Caso completo

**Persona:** «Necesito migrar el servicio de recepción de inventario a una arquitectura nueva».

1. **Agente:** «¿Qué servicio o flujo legacy vas a migrar?»
   **Persona:** «El endpoint `POST /api/inventory/receive` y toda su lógica».
2. **Agente:** «¿Migras todo o solo una parte?»
   **Persona:** «Todo el flujo de recepción, menos las devoluciones: esas se migran después».

El agente investiga: encuentra el endpoint en `src/controllers/inventory.controller.ts`, sigue el
flujo hasta `InventoryService.receiveProduct()`, mapea las validaciones en `InventoryValidator`, los
cálculos en `StockCalculator`, el correo en `NotificationService` y un filtro `archived = false` en
una consulta. Escribe `docs/reglas.md`:

````markdown
# Reglas de negocio: recepción de inventario

- **Servicio legacy:** POST /api/inventory/receive
- **Servicio nuevo:** recepción de inventario, sin devoluciones
- **Fecha de extracción:** AAAA-MM-DD

## 1. Validaciones de entrada

### R-001: la cantidad debe ser mayor que cero
**Descripción:** la cantidad recibida debe ser un número positivo.
**Ubicación:** `src/validators/inventory.validator.ts`, `validateReceiptRequest()`, línea 15
**Entrada:** `{ "quantity": 50 }` → se acepta.
**Casos especiales:** quantity <= 0 → «La cantidad debe ser positiva»; no numérica → «La cantidad
debe ser un número».
**Estado propuesto:** Mantener.

### R-002: el producto debe existir en el catálogo activo
**Descripción:** el producto existe y no está archivado.
**Ubicación:** `src/services/inventory.service.ts`, `receiveProduct()`, línea 45:
`SELECT * FROM products WHERE id = ? AND archived = false`
**Entrada:** `{ "productId": 456 }` → producto encontrado.
**Casos especiales:** no existe → «Producto no encontrado»; archivado → el mismo mensaje.
**Estado propuesto:** Mantener, separando los dos mensajes de error.

## 2. Cálculos de negocio

### R-010: actualización de stock
**Descripción:** el stock nuevo es el actual más la cantidad recibida.
**Ubicación:** `src/services/stock.calculator.ts`, `calculateNewStock()`, línea 23
**Entrada:** `{ "currentStock": 100, "quantity": 50 }` → `{ "newStock": 150 }`
**Casos especiales:** currentStock nulo (producto nuevo) → cuenta como 0.
**Estado propuesto:** Mantener.

### R-011: descuento por volumen (100 unidades o más)
**Descripción:** si se reciben 100 unidades o más, el costo unitario baja un 5 %.
**Ubicación:** `src/services/pricing.service.ts`, `applyVolumeDiscount()`, líneas 67-72
**Entrada:** `{ "quantity": 150, "unitCost": 10.00 }` → `{ "finalUnitCost": 9.50 }`
**Casos especiales:** quantity < 100 → sin descuento.
**Estado propuesto:** Por confirmar. ¿Sigue vigente o cambió con las nuevas condiciones de proveedores?

## 3. Efectos secundarios

### R-040: aviso cuando el stock baja del umbral
**Descripción:** tras actualizar el stock, si queda por debajo del umbral del producto, se envía un
correo a compras.
**Ubicación:** `src/services/notification.service.ts`, `checkStockThreshold()`, líneas 34-45. El
destinatario está fijo en el código.
**Entrada:** `{ "newStock": 8, "threshold": 10 }` → correo «Stock bajo del producto X».
**Casos especiales:** si el correo falla, se registra el error pero la recepción no se bloquea.
**Estado propuesto:** Cambiar: notificación asíncrona y destinatario configurable.

## 4. Reglas implícitas

### R-050: los productos archivados no se procesan
**Descripción:** un producto archivado no puede recibir stock.
**Ubicación:** `src/services/inventory.service.ts`, línea 48: `WHERE archived = false`
**Entrada:** `{ "productId": 789 }` (archivado) → «Producto no encontrado».
**Estado propuesto:** Mantener, con un mensaje que distinga «no existe» de «archivado» (ver R-002).

## Dudas

### D-001: ¿es obligatorio el proveedor?
**Observado:** se aceptan recepciones sin `supplierId`.
**Código:** `src/validators/inventory.validator.ts`, línea 20 (`supplierId` no es obligatorio)
**Pregunta:** ¿es correcto o es un bug que nunca se arregló?
**Impacto:** si es intencional, sigue opcional; si es un bug, pasa a ser obligatorio.

### D-002: ¿por qué se espera 5 segundos?
**Observado:** tras actualizar el stock, el código espera 5 segundos antes de avisar a contabilidad.
**Código:** `src/services/inventory.service.ts`, línea 120:
`setTimeout(() => webhookService.notify(receiptData), 5000)`
**Pregunta:** ¿es un requisito de negocio o un parche técnico (por ejemplo, esperar a que la base de
datos replique)?
**Impacto:** si es requisito, se mantiene una espera configurable; si es un parche, se elimina.

## Checklist de verificación

| Regla | Test | Pasó | Notas |
|---|---|---|---|
| R-001 | `rechaza_cantidad_no_positiva` | ⬜ | |
| R-002 | `exige_producto_existente_y_activo` | ⬜ | Separar mensajes |
| R-010 | `calcula_stock_nuevo` | ⬜ | |
| R-011 | `aplica_descuento_por_volumen` | ⬜ | Confirmar vigencia |
| R-040 | `avisa_stock_bajo` | ⬜ | Notificación asíncrona |
| R-050 | `rechaza_productos_archivados` | ⬜ | Mejorar mensaje |

Estados propuestos: 4 mantener, 1 cambiar, 0 eliminar, 1 por confirmar.
````

El agente presenta el documento y se detiene. La persona resuelve D-001, D-002 y R-011 y confirma el
estado de cada regla. El agente actualiza `docs/reglas.md`, lo commitea en `main` y sugiere seguir con
`/desde-cero` usando el documento como base.
