# Ejemplo Completo: Servicio de Recepción de Inventario

Caso real documentado de inicio a fin.

## Contexto

**Usuario dice:**
> "Necesito migrar el servicio de recepción de inventario a una nueva arquitectura."

**Agente pregunta:**

1. "¿Qué servicio legacy migras?" → POST /api/inventory/receive
2. "¿Cuál es el alcance?" → Todo excepto devoluciones
3. "¿Dónde guardo?" → `docs/migration/reglas-inventario.md`

## Resultado: reglas-inventario.md

```markdown
# Reglas de Negocio: Servicio de Recepción de Inventario

## Metadata
- **Servicio legacy**: POST /api/inventory/receive
- **Nuevo servicio**: Inventory Reception Service (sin devoluciones)
- **Fecha**: 2026-11-09
- **Autor**: Agent

## 1. Validaciones

### R-001: Quantity debe ser mayor a cero

**Descripción:**
La cantidad recibida debe ser un número positivo mayor a cero.

**Ubicación:**
- Archivo: `src/validators/inventory.validator.ts`
- Función: `validateReceiptRequest()` línea 15

**Entrada:**
\`\`\`json
{ "quantity": 50 }
\`\`\`

**Salida:**
Acepta la solicitud.

**Casos especiales:**
- Si quantity <= 0: error "Quantity must be positive"

**Estado:** [x] Mantener

---

### R-002: Product debe existir en catálogo

**Descripción:**
El producto debe existir y no estar archivado.

**Ubicación:**
- Archivo: `src/services/inventory.service.ts` línea 45
- Query: `SELECT * FROM products WHERE id = ? AND archived = false`

**Entrada:**
\`\`\`json
{ "productId": 456 }
\`\`\`

**Salida:**
Producto encontrado.

**Casos especiales:**
- Si no existe: error "Product not found"
- Si archived = true: error "Product not found"

**Estado:** [x] Mantener
**Nota:** Separar mensajes en nuevo servicio

## 2. Cálculos

### R-010: Actualización de stock

**Descripción:**
Stock nuevo = stock actual + cantidad recibida.

**Ubicación:**
- Archivo: `src/services/stock.calculator.ts` línea 23

**Entrada:**
\`\`\`json
{ "currentStock": 100, "quantity": 50 }
\`\`\`

**Salida:**
\`\`\`json
{ "newStock": 150 }
\`\`\`

**Estado:** [x] Mantener

## ⚠️ Dudas

### D-001: ¿Es obligatorio el supplierId?

**Comportamiento:** Acepta recepciones sin supplierId.

**Código:** `src/validators/inventory.validator.ts` línea 20

**Pregunta:** ¿Es correcto permitirlo?

**Impacto:**
- Si es bug: hacer obligatorio
- Si es feature: mantener opcional

## ✅ Checklist

| Regla | Test Case | Pasó | Notas |
|-------|-----------|------|-------|
| R-001 | `test_quantity_positive()` | ⬜ | |
| R-002 | `test_product_exists()` | ⬜ | Separar mensajes |
| R-010 | `test_stock_calculation()` | ⬜ | |
```

## Siguiente paso

```
/desde-cero

Usa @docs/migration/reglas-inventario.md como base 
para las reglas de negocio del nuevo servicio.
```
