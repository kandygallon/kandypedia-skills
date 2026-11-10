---
name: extraer-reglas
description: Usa esta skill para extraer reglas de negocio de un servicio legacy durante migraciones. El código legacy es la fuente de verdad para las reglas, no para el diseño.
---

# Extraer Reglas

## ¿Cuándo usar esta skill?

Úsala cuando:
- Estás migrando un servicio legacy a una nueva implementación
- Necesitas extraer las reglas de negocio del código viejo
- El código legacy es la fuente de verdad para **qué** hace el sistema, pero no para **cómo** debe hacerse
- Vas a construir un servicio nuevo que debe respetar las mismas reglas

**No la uses** cuando:
- Solo necesitas entender código para modificarlo (usa `/analizar-existente`)
- Estás empezando algo completamente nuevo sin legacy (usa `/desde-cero`)
- El legacy se va a mantener tal cual (no es una migración)

## Filosofía

En migraciones, el **código legacy es documentación ejecutable de las reglas de negocio**. Aunque el diseño sea malo, las reglas que implementa son las que el negocio espera.

Esta skill te guía para:
1. **Extraer reglas** del código legacy sin juzgar su implementación
2. **Documentar** cada regla con suficiente detalle para reimplementarla
3. **Identificar** qué reglas son bugs vs. comportamiento intencional
4. **Preparar** la especificación para el nuevo servicio

**IMPORTANTE**: Esta skill **NO escribe código nuevo**. Solo extrae y documenta reglas.

## Proceso paso a paso

### 1. Preguntas iniciales (antes de investigar)

Pregunta al usuario:

**Pregunta 1:**
> "¿Qué servicio o flujo legacy estás migrando? Dame el nombre del servicio, módulo o endpoint principal."

**Espera la respuesta.**

**Pregunta 2:**
> "¿Cuál es el alcance del nuevo servicio? ¿Vas a migrar TODO el legacy o solo una parte? ¿Qué funcionalidades quedan fuera?"

**Espera la respuesta.**

**Pregunta 3:**
> "¿Dónde quieres que guarde el documento de reglas? (ej: `docs/reglas.md`, `migration/reglas-inventario.md`)"

**Espera la respuesta.**

### 2. Mapeo del servicio legacy

Investiga el código legacy y mapea:

#### a) Puntos de entrada
- ¿Qué endpoints, comandos o funciones inician el flujo?
- ¿Cómo se llaman (HTTP, eventos, cron)?
- ¿Qué parámetros reciben?

**Ejemplo:**
```
Punto de entrada: POST /api/inventory/receive
Parámetros: { warehouseId, productId, quantity, supplierId }
```

#### b) Validaciones
- ¿Qué se valida antes de procesar?
- ¿Qué causa un rechazo (error 400)?
- ¿Hay validaciones condicionales (solo si X, entonces valida Y)?

**Ejemplo:**
```
Validaciones:
- quantity > 0
- product existe en catálogo
- Si product.needsApproval == true, requiere supplierId
```

#### c) Cálculos y transformaciones
- ¿Qué cálculos matemáticos hace?
- ¿Qué transformaciones de datos aplica?
- ¿Hay fórmulas o algoritmos específicos?

**Ejemplo:**
```
Cálculo de stock nuevo:
stock_nuevo = stock_actual + quantity
Si quantity > 100: aplicar descuento_volumen = 0.05
```

#### d) Transiciones de estado
- ¿Cambia el estado de alguna entidad?
- ¿Qué estados son posibles?
- ¿Qué transiciones están permitidas?

**Ejemplo:**
```
Estado del producto:
PENDING → RECEIVED → VERIFIED → AVAILABLE
No se puede ir de PENDING directo a AVAILABLE
```

#### e) Permisos y autorización
- ¿Quién puede hacer qué?
- ¿Hay roles o permisos verificados?
- ¿Hay reglas de negocio basadas en el usuario?

**Ejemplo:**
```
Permisos:
- Solo rol WAREHOUSE_MANAGER puede recibir productos
- Si quantity > 1000, requiere aprobación de SUPERVISOR
```

#### f) Manejo de errores
- ¿Qué errores se capturan?
- ¿Qué hace el sistema cuando algo falla?
- ¿Hay rollbacks, reintentos o compensaciones?

**Ejemplo:**
```
Error handling:
- Si falla actualización de stock: rollback transacción
- Si falla notificación: loguea error pero no bloquea
```

#### g) Efectos secundarios
- ¿Envía emails, notificaciones, mensajes?
- ¿Escribe logs o auditoría?
- ¿Llama a otros servicios o APIs?

**Ejemplo:**
```
Side effects:
- Email a purchasing@empresa.com cuando stock < threshold
- Log en audit_log tabla
- Webhook a sistema de contabilidad
```

#### h) Reglas implícitas (las más peligrosas)
- ¿Hay lógica en queries SQL (WHERE, GROUP BY)?
- ¿Hay reglas en archivos de configuración?
- ¿Hay defaults o valores hardcodeados?
- ¿Hay comportamiento que depende de datos (no código)?

**Ejemplo:**
```
Reglas implícitas:
- Query filtra products.archived = false (nunca se procesan archivados)
- Config: max_receive_quantity = 10000 (hardcoded)
- Si supplier.region == "LATAM", usa exchange_rate de config
```

### 3. Documenta cada regla en `reglas.md`

Para cada regla que encuentres, escribe una entrada numerada:

```markdown
### R-001: [Nombre descriptivo de la regla]

**Descripción (lenguaje de negocio):**
[Explica QUÉ hace la regla, no cómo está implementada. Evita tecnicismos.]

**Ubicación en código legacy:**
- Archivo: `src/services/inventory/receive.service.ts`
- Función: `processReceipt()` líneas 45-67
- También en: `src/validators/inventory.validator.ts` líneas 12-15

**Entrada esperada:**
[Ejemplo de input válido]
```json
{
  "warehouseId": 123,
  "productId": 456,
  "quantity": 50
}
```

**Salida esperada:**
[Qué produce esta regla]
```json
{
  "newStock": 150,
  "receipt": { "id": 789, "status": "RECEIVED" }
}
```

**Casos especiales / Edge cases:**
- Si quantity == 0: rechaza con error "Quantity must be positive"
- Si product no existe: rechaza con error "Product not found"
- Si warehouse está cerrado (closed == true): rechaza con error "Warehouse closed"

**Estado para nuevo servicio:**
- [ ] **Mantener**: implementar igual en el nuevo servicio
- [ ] **Cambiar**: implementar diferente (especificar cómo)
- [ ] **Eliminar**: ya no es necesaria
- [ ] **Por confirmar**: preguntar a experto de dominio

**Notas:**
[Cualquier cosa rara, bugs sospechosos, o decisiones históricas]
```

**Numera las reglas secuencialmente**: R-001, R-002, R-003...

### 4. Agrupa reglas por categoría

Organiza las reglas en secciones:

```markdown
# Reglas de Negocio: [Nombre del Servicio Legacy]

## Metadata
- **Servicio legacy**: [nombre]
- **Nuevo servicio**: [nombre o alcance]
- **Fecha de extracción**: [fecha]
- **Autor**: [quien extrajo las reglas]

## 1. Validaciones de Entrada

### R-001: Quantity debe ser positiva
[...]

### R-002: Product debe existir en catálogo
[...]

## 2. Cálculos de Negocio

### R-010: Cálculo de stock nuevo
[...]

### R-011: Descuento por volumen
[...]

## 3. Transiciones de Estado

### R-020: Flujo de estados de producto
[...]

## 4. Permisos y Autorización

### R-030: Solo WAREHOUSE_MANAGER puede recibir
[...]

## 5. Efectos Secundarios

### R-040: Email cuando stock bajo
[...]

## 6. Reglas Implícitas

### R-050: No procesar productos archivados
[...]
```

### 5. Dudas y bugs sospechosos

Lista aparte para comportamiento ambiguo:

```markdown
## ⚠️ Dudas para Expertos de Dominio

### D-001: ¿Es bug o feature?
**Comportamiento observado:**
Si `supplierId` es null, el sistema acepta el receipt pero no registra quién lo envió.

**Código:**
`src/services/inventory/receive.service.ts` línea 89

**Pregunta:**
¿Es intencional permitir receipts sin proveedor, o es un bug que nunca se detectó?

**Impacto:**
- Si es bug: debemos validar supplierId obligatorio
- Si es feature: debemos mantener esa flexibilidad

---

### D-002: ¿Regla de negocio o workaround técnico?
**Comportamiento observado:**
El sistema espera 5 segundos después de actualizar stock antes de enviar el webhook.

**Código:**
`src/services/inventory/receive.service.ts` línea 120 (`setTimeout(5000)`)

**Pregunta:**
¿Por qué el delay? ¿Es para dar tiempo a que la DB replique, o es un requisito de negocio?

**Impacto:**
- Si es workaround: no lo necesitamos en el nuevo servicio con mejor arquitectura
- Si es requisito: debemos mantener algún delay (quizás configurable)
```

### 6. Checklist de verificación

Al final del documento, agrega:

```markdown
## ✅ Checklist de Verificación (para el nuevo servicio)

Esta lista mapea cada regla a un test case que debe pasar en el nuevo servicio.

| Regla | Test Case | Pasó | Notas |
|-------|-----------|------|-------|
| R-001 | `test_rechaza_cantidad_cero()` | ⬜ | |
| R-002 | `test_rechaza_producto_inexistente()` | ⬜ | |
| R-010 | `test_calcula_stock_nuevo_correcto()` | ⬜ | |
| R-011 | `test_aplica_descuento_volumen()` | ⬜ | |
| R-020 | `test_transicion_estados_valida()` | ⬜ | |
| R-030 | `test_permisos_warehouse_manager()` | ⬜ | |
| R-040 | `test_envia_email_stock_bajo()` | ⬜ | |
| R-050 | `test_ignora_productos_archivados()` | ⬜ | |

**Instrucciones:**
1. Implementar cada test en el nuevo servicio
2. Marcar ✅ cuando el test pase
3. Agregar notas si hubo cambios respecto al legacy
```

### 7. Sugerencia de continuación

Al terminar el documento `reglas.md`, di al usuario:

> "✅ Documento de reglas completado: `[ruta/reglas.md]`
> 
> **Próximos pasos sugeridos:**
> 
> 1. **Revisar con experto de dominio**: Resuelve las dudas marcadas (⚠️ sección de Dudas)
> 2. **Decidir estado de cada regla**: Marca cuáles mantener/cambiar/eliminar
> 3. **Usar `/desde-cero`** para especificar el nuevo servicio:
>    ```
>    /desde-cero
>    Usa @reglas.md como base para las reglas de negocio
>    ```
> 
> La especificación del nuevo servicio debe respetar las reglas marcadas como "Mantener" y documentar las que marcaste como "Cambiar"."

## Ejemplo completo

**Usuario dice:**
> "Necesito migrar el servicio de recepción de inventario a una nueva arquitectura."

**Agente (usando esta skill):**

**Pregunta 1:**
> "¿Qué servicio o flujo legacy estás migrando?"

**Usuario responde:**
> "El endpoint POST /api/inventory/receive y toda su lógica."

**Pregunta 2:**
> "¿Cuál es el alcance del nuevo servicio? ¿Migras todo o solo una parte?"

**Usuario responde:**
> "Todo el flujo de recepción, pero SIN la parte de devoluciones (esa se migra después)."

**Pregunta 3:**
> "¿Dónde guardo el documento de reglas?"

**Usuario responde:**
> "En `docs/migration/reglas-inventario.md`"

**Agente investiga el código legacy:**

1. Busca el endpoint: `src/controllers/inventory.controller.ts`
2. Sigue el flujo: `InventoryService.receiveProduct()`
3. Mapea validaciones en `InventoryValidator`
4. Encuentra cálculos en `StockCalculator`
5. Descubre email side-effect en `NotificationService`
6. Encuentra regla implícita: query filtra `archived = false`

**Agente produce `docs/migration/reglas-inventario.md`:**

```markdown
# Reglas de Negocio: Servicio de Recepción de Inventario

## Metadata
- **Servicio legacy**: POST /api/inventory/receive
- **Nuevo servicio**: Inventory Reception Service (sin devoluciones)
- **Fecha de extracción**: 2026-11-09
- **Autor**: Agent

## 1. Validaciones de Entrada

### R-001: Quantity debe ser mayor a cero

**Descripción:**
La cantidad recibida debe ser un número positivo mayor a cero.

**Ubicación en código legacy:**
- Archivo: `src/validators/inventory.validator.ts`
- Función: `validateReceiptRequest()` línea 15

**Entrada esperada:**
```json
{ "quantity": 50 }
```

**Salida esperada:**
Acepta la solicitud.

**Casos especiales:**
- Si quantity <= 0: error "Quantity must be positive"
- Si quantity no es número: error "Quantity must be a number"

**Estado para nuevo servicio:**
- [x] **Mantener**

---

### R-002: Product debe existir en catálogo activo

**Descripción:**
El producto debe existir en la base de datos y no estar archivado.

**Ubicación en código legacy:**
- Archivo: `src/services/inventory.service.ts`
- Función: `receiveProduct()` línea 45
- Query: `SELECT * FROM products WHERE id = ? AND archived = false`

**Entrada esperada:**
```json
{ "productId": 456 }
```

**Salida esperada:**
Producto encontrado y no archivado.

**Casos especiales:**
- Si product no existe: error "Product not found"
- Si product.archived = true: error "Product not found" (mismo mensaje)

**Estado para nuevo servicio:**
- [x] **Mantener**
- Nota: En el nuevo servicio, separar los mensajes (product no existe vs archivado)

---

## 2. Cálculos de Negocio

### R-010: Actualización de stock

**Descripción:**
El stock nuevo es el stock actual más la cantidad recibida.

**Ubicación en código legacy:**
- Archivo: `src/services/stock.calculator.ts`
- Función: `calculateNewStock()` línea 23

**Entrada esperada:**
```json
{ "currentStock": 100, "quantity": 50 }
```

**Salida esperada:**
```json
{ "newStock": 150 }
```

**Casos especiales:**
- Si currentStock es null (producto nuevo): trata como 0

**Estado para nuevo servicio:**
- [x] **Mantener**

---

### R-011: Descuento por volumen (≥100 unidades)

**Descripción:**
Si la cantidad recibida es 100 o más, se aplica un descuento del 5% en el costo unitario.

**Ubicación en código legacy:**
- Archivo: `src/services/pricing.service.ts`
- Función: `applyVolumeDiscount()` líneas 67-72

**Entrada esperada:**
```json
{ "quantity": 150, "unitCost": 10.00 }
```

**Salida esperada:**
```json
{ "finalUnitCost": 9.50 }
```

**Casos especiales:**
- Si quantity >= 100: aplica 5% descuento
- Si quantity < 100: sin descuento

**Estado para nuevo servicio:**
- [ ] **Por confirmar**
- Nota: ¿Esta regla sigue vigente o cambió con el nuevo contrato de proveedores?

---

## 3. Efectos Secundarios

### R-040: Email cuando stock cae bajo umbral

**Descripción:**
Después de actualizar el stock, si el nuevo stock es menor que el threshold del producto, envía email a compras.

**Ubicación en código legacy:**
- Archivo: `src/services/notification.service.ts`
- Función: `checkStockThreshold()` líneas 34-45
- Email destinatario: `purchasing@company.com` (hardcoded)

**Entrada esperada:**
```json
{ "newStock": 8, "threshold": 10 }
```

**Salida esperada:**
Email enviado: "Low stock alert for Product XYZ"

**Casos especiales:**
- Si envío de email falla: loguea error pero NO bloquea la recepción

**Estado para nuevo servicio:**
- [x] **Cambiar**
- Cambio: usar sistema de notificaciones async, no email directo
- Cambio: destinatario configurable, no hardcoded

---

## 4. Reglas Implícitas

### R-050: Productos archivados no se procesan

**Descripción:**
Los productos marcados como archivados (archived = true) no pueden recibir stock nuevo.

**Ubicación en código legacy:**
- Archivo: `src/services/inventory.service.ts`
- Query SQL: línea 48 `WHERE archived = false`

**Entrada esperada:**
```json
{ "productId": 789 }
```
(donde product 789 tiene archived = true)

**Salida esperada:**
Error "Product not found" (no revela que está archivado)

**Casos especiales:**
Ninguno.

**Estado para nuevo servicio:**
- [x] **Mantener**
- Nota: En el nuevo servicio, mejorar el mensaje de error para distinguir "no existe" vs "archivado"

---

## ⚠️ Dudas para Expertos de Dominio

### D-001: ¿Es obligatorio el supplierId?

**Comportamiento observado:**
El código acepta recepciones sin supplierId (campo opcional).

**Código:**
`src/validators/inventory.validator.ts` línea 20 (supplierId no está en required)

**Pregunta:**
¿Es correcto permitir recepciones sin registrar el proveedor? ¿O es un bug que nunca se arregló?

**Impacto:**
- Si es feature: mantener opcional
- Si es bug: hacer obligatorio en nuevo servicio

---

### D-002: ¿Por qué el setTimeout de 5 segundos?

**Comportamiento observado:**
Después de actualizar stock, el código espera 5 segundos antes de enviar el webhook al sistema de contabilidad.

**Código:**
`src/services/inventory.service.ts` línea 120
```typescript
setTimeout(() => {
  webhookService.notify(receiptData);
}, 5000);
```

**Pregunta:**
¿Cuál es la razón del delay? ¿Es requisito de negocio o workaround técnico?

**Impacto:**
- Si es requisito: mantener delay (configurable)
- Si es workaround: eliminar en nuevo servicio con arquitectura async

---

## ✅ Checklist de Verificación

| Regla | Test Case | Pasó | Notas |
|-------|-----------|------|-------|
| R-001 | `test_quantity_must_be_positive()` | ⬜ | |
| R-002 | `test_product_must_exist_and_active()` | ⬜ | Separar mensajes de error |
| R-010 | `test_stock_calculation()` | ⬜ | |
| R-011 | `test_volume_discount_100_units()` | ⬜ | Confirmar vigencia con dominio |
| R-040 | `test_low_stock_notification()` | ⬜ | Usar notif async |
| R-050 | `test_archived_products_rejected()` | ⬜ | Mejorar mensaje |

**Total reglas extraídas**: 6 mantener, 1 cambiar, 0 eliminar, 1 por confirmar
```

## Entregables

Al terminar de usar esta skill, debes tener:

1. ✅ Un **documento `reglas.md`** con todas las reglas numeradas (R-001, R-002...)
2. ✅ Cada regla con: descripción, ubicación en legacy, entrada/salida, edge cases, estado
3. ✅ **Dudas documentadas** para expertos de dominio (D-001, D-002...)
4. ✅ **Checklist de verificación** mapeando reglas a test cases
5. ✅ **Sugerencia de próximos pasos** (revisar dudas, usar `/desde-cero`)

## Transición a las siguientes skills

Después de extraer las reglas:
1. **Revisar con experto de dominio**: resuelve las dudas marcadas
2. **Marcar estado de cada regla**: mantener / cambiar / eliminar / por confirmar
3. **Usar `/desde-cero`** con `@reglas.md` como input para especificar el nuevo servicio
4. Si el nuevo servicio es grande, usa `/reparto` para dividir la implementación

## Notas importantes

- **No escribas código nuevo**: esta skill solo documenta reglas
- **No juzgues el legacy**: si está raro, documéntalo como duda, no lo descartes
- **Las reglas implícitas son peligrosas**: queries, configs, defaults escondidos rompen migraciones
- **Bugs vs features**: si no estás seguro, márcalo como duda para confirmar
- **El legacy es la verdad**: aunque el código sea horrible, las reglas que implementa son las que el negocio espera
- **Numera secuencialmente**: R-001, R-002... facilita referencias en documentos posteriores
- **Mantén ejemplos genéricos**: evita datos reales, nombres de empresas o dominios sensibles
