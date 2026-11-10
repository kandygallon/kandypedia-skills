# Ejemplos de Revisión

## Ejemplo: Revisión de implementación de productos

**Archivos revisados:**
- src/services/inventory.service.ts (120 líneas)
- src/types/inventory.types.ts (30 líneas)
- tests/inventory.test.ts (85 líneas)

### 1. Correctitud

✅ Flujo básico correcto según contrato.md

❌ No implementa regla R-011 (descuento por volumen) marcada como "Mantener" en reglas.md

### 2. Manejo de errores

✅ Códigos correctos (400 validación, 404 not found)

❌ Lanza 400 para SKU duplicado, contrato especifica 409

### 3. Seguridad

✅ No hay credenciales hardcodeadas

⚠️ productId se usa en query sin sanitizar

### 4. Tests

✅ Tests cubren casos básicos

❌ Falta test para R-011

### 5. Legibilidad

✅ Código claro

⚠️ Función de 120 líneas, considerar separar

---

## Hallazgos

1. [Gravedad: alta] src/services/inventory.service.ts:45-67: No implementa regla R-011 de descuento por volumen. Por qué importa: reglas.md la marca como "Mantener" y es requisito documentado. Sugerencia: agregar `if (quantity >= 100) { applyDiscount(0.05) }` según reglas.md.

2. [Gravedad: alta] src/services/inventory.service.ts:82: Lanza 400 para SKU duplicado, contrato especifica 409. Por qué importa: inconsistencia con contrato que UI/otros servicios esperan. Sugerencia: cambiar a `throw new ConflictError('DUPLICATE_SKU')`.

3. [Gravedad: media] tests/inventory.test.ts: Falta test para R-011. Por qué importa: regla crítica sin cobertura. Sugerencia: `test_volume_discount_100_units()`.

4. [Gravedad: baja] src/services/inventory.service.ts:45-120: Función larga. Por qué importa: facilita mantenimiento separar. Sugerencia: extraer lógica de descuento.

---

## Decisión

❌ **No aprobaría para merge**: hallazgos 1 y 2 (gravedad alta) omiten regla de negocio y rompen contrato API. Deben corregirse. Hallazgos 3-4 deseables pero no bloqueantes.
