---
name: tests-primero
description: Usa esta skill antes de implementar cualquier código. Define ejemplos de entrada/salida, escribe tests que fallan, y luego implementa lo mínimo para que pasen.
---

# Tests primero

## ¿Cuándo usar esta skill?

Úsala cuando:
- ✅ Ya tienes la especificación del requisito (de `/desde-cero` o `/extraer-reglas`)
- ✅ Estás en tu worktree listo para implementar
- ✅ Quieres asegurar que el código hace lo que debe hacer

**No la uses** cuando:
- ❌ Todavía estás en fase de planificación o análisis
- ❌ Estás escribiendo código exploratorio/prototipo

## Filosofía

Antes de escribir cualquier código para esta tarea:

### Paso 1: Explica el requisito

**Explícame el requisito con tus propias palabras, en 3 a 5 líneas.**

Esto verifica que entendiste lo que hay que hacer.

### Paso 2: Ejemplos de entrada y salida

**Dame al menos 3 ejemplos de entrada y salida esperada, incluyendo casos frontera** (valores vacíos, nulos, límites, errores).

**Si existe `docs/reglas.md`** (de `/extraer-reglas`): deriva los ejemplos de las reglas documentadas y referencia los IDs de regla (R-001, R-002...).

**Ejemplo:**

```markdown
## Ejemplos

### Ejemplo 1: Caso exitoso (R-001, R-010)
**Entrada:**
```json
{
  "productId": 456,
  "quantity": 50
}
```

**Salida esperada:**
```json
{
  "newStock": 150,
  "receipt": {"id": 789, "status": "RECEIVED"}
}
```

### Ejemplo 2: Cantidad cero (R-001)
**Entrada:**
```json
{
  "productId": 456,
  "quantity": 0
}
```

**Salida esperada:**
Error 400: "Quantity must be positive"

### Ejemplo 3: Producto inexistente (R-002)
**Entrada:**
```json
{
  "productId": 999,
  "quantity": 50
}
```

**Salida esperada:**
Error 404: "Product not found"

### Ejemplo 4: Caso frontera: producto nuevo (R-010)
**Entrada:**
```json
{
  "productId": 123,  // producto sin stock previo
  "quantity": 10
}
```

**Salida esperada:**
```json
{
  "newStock": 10,  // trata stock previo como 0
  "receipt": {"id": 790, "status": "RECEIVED"}
}
```
```

### Paso 3: Suposiciones y dudas

**Lista cualquier suposición que estés haciendo y cualquier duda sobre el requisito.**

**Ejemplo:**

```markdown
## Suposiciones
1. El productId debe existir en la base de datos antes de recibir stock
2. La cantidad siempre se suma al stock actual, nunca se resta
3. El receiptId se genera automáticamente

## Dudas
1. ¿Qué pasa si quantity es decimal (ej: 50.5)? ¿Se redondea o rechaza?
2. ¿Hay un límite máximo de quantity por recepción?
```

### Paso 4: DETENTE

**DETENTE y espera a que yo confirme o corrija los ejemplos.**

No escribas código ni tests todavía.

## Cuando el usuario confirme

### Paso 5: Escribe SOLO los tests

**Escribe SOLO los tests basados en los ejemplos aprobados. No escribas la implementación todavía.**

**Ejemplo:**

```typescript
// tests/inventory.test.ts
describe('receiveProduct', () => {
  test('debe aumentar stock cuando quantity es válido (R-001, R-010)', async () => {
    const result = await receiveProduct({ productId: 456, quantity: 50 });
    expect(result.newStock).toBe(150);
    expect(result.receipt.status).toBe('RECEIVED');
  });

  test('debe rechazar quantity cero (R-001)', async () => {
    await expect(
      receiveProduct({ productId: 456, quantity: 0 })
    ).rejects.toThrow('Quantity must be positive');
  });

  test('debe rechazar producto inexistente (R-002)', async () => {
    await expect(
      receiveProduct({ productId: 999, quantity: 50 })
    ).rejects.toThrow('Product not found');
  });

  test('debe tratar producto nuevo como stock 0 (R-010)', async () => {
    const result = await receiveProduct({ productId: 123, quantity: 10 });
    expect(result.newStock).toBe(10);
  });
});
```

### Paso 6: Ejecuta los tests (deben fallar)

**Ejecuta los tests y muestra que FALLAN.**

Esto confirma que los tests realmente están probando algo (no son falsos positivos).

```bash
npm test

# Salida esperada:
# FAIL tests/inventory.test.ts
# ✗ debe aumentar stock cuando quantity es válido (R-001, R-010)
#   receiveProduct is not defined
```

### Paso 7: DETENTE de nuevo

**DETENTE y espera a que revise los tests.**

## Cuando el usuario apruebe los tests

### Paso 8: Implementa lo mínimo

**Escribe la implementación mínima para que pasen.**

No agregues funcionalidad extra que no esté en los tests aprobados.

### Paso 9: Ejecuta los tests

**Ejecuta los tests y muéstrame el resultado.**

```bash
npm test

# Salida esperada:
# PASS tests/inventory.test.ts
# ✓ debe aumentar stock cuando quantity es válido (R-001, R-010)
# ✓ debe rechazar quantity cero (R-001)
# ✓ debe rechazar producto inexistente (R-002)
# ✓ debe tratar producto nuevo como stock 0 (R-010)
```

### Paso 10: Resume el diff

**Resume el diff: qué archivos cambiaste y por qué.**

**Ejemplo:**

```markdown
## Cambios realizados

### tests/inventory.test.ts (nuevo)
- 4 tests basados en ejemplos aprobados
- Referencias a reglas R-001, R-002, R-010

### src/services/inventory.service.ts (nuevo)
- Función receiveProduct que:
  - Valida quantity > 0 (R-001)
  - Verifica que product existe (R-002)
  - Calcula newStock = currentStock + quantity (R-010)
  - Trata stock null como 0 para productos nuevos (R-010)

### src/types/inventory.types.ts (nuevo)
- Tipos para receiveProduct input/output
```

## Reglas importantes

- **No modifiques los tests aprobados para que pasen**: si crees que un test está mal, dímelo y espera.
- **Implementación mínima**: solo lo necesario para pasar los tests, nada más.
- **DETENTE en cada paso de aprobación**: no avances sin confirmación.

## Ejemplo completo

**Usuario dice:**
> "Implementa la función de recepción de inventario según el contrato."

**Agente (usando esta skill):**

**Paso 1: Explico el requisito**
> La función `receiveProduct` recibe un productId y una quantity, valida que ambos sean válidos, aumenta el stock del producto, y devuelve el nuevo stock y un receipt. Debe rechazar quantity <= 0 y productos inexistentes.

**Paso 2: Ejemplos**
> [Lista los 4 ejemplos como arriba, referenciando R-001, R-002, R-010 si existe reglas.md]

**Paso 3: Suposiciones y dudas**
> [Lista suposiciones y dudas como arriba]

**Paso 4: DETENTE**
> He listado los ejemplos y dudas. ¿Confirmas que son correctos o hay que ajustar algo?

**Usuario:**
> "Confirmo. Sobre tu duda 1: rechaza decimales con error. Sobre la 2: no hay límite por ahora."

**Agente:**

**Paso 5: Escribo tests**
```typescript
[Escribe los tests como arriba, agregando uno para decimales]
```

**Paso 6: Ejecuto tests (fallan)**
```bash
npm test
# FAIL: receiveProduct is not defined
```

**Paso 7: DETENTE**
> Tests escritos y fallan como esperado. ¿Apruebas que implemente el código?

**Usuario:**
> "Aprobado."

**Agente:**

**Paso 8: Implemento**
```typescript
[Implementa la función mínima]
```

**Paso 9: Ejecuto tests (pasan)**
```bash
npm test
# PASS: 5 tests pasaron
```

**Paso 10: Resume diff**
> Cambios:
> - tests/inventory.test.ts: 5 tests
> - src/services/inventory.service.ts: función receiveProduct
> - src/types/inventory.types.ts: tipos de entrada/salida

## Entregables

Al terminar de usar esta skill, debes tener:

1. ✅ Requisito explicado en tus propias palabras
2. ✅ Al menos 3 ejemplos aprobados (entrada/salida/errores)
3. ✅ Tests escritos basados en ejemplos
4. ✅ Tests ejecutados y fallando ANTES de implementar
5. ✅ Implementación mínima que hace pasar los tests
6. ✅ Tests pasando después de implementar
7. ✅ Resumen de cambios

## Transición a las siguientes skills

Después de usar tests-primero:
- Continúa implementando otras funcionalidades con el mismo proceso
- Al terminar tu tarea completa, usa `/revision-independiente` para revisar todo
- Luego usa `/antes-del-merge` antes de mergear

## Relación con otras skills

- **Después de `/desde-cero` o `/extraer-reglas`**: usa la especificación o reglas como base para los ejemplos
- **Dentro de cada worktree**: cada agente usa esta skill para su parte
- **Si existe `docs/contrato.md`**: los ejemplos deben coincidir con los contratos definidos
- **Si existe `docs/reglas.md`**: deriva ejemplos de las reglas y referencia IDs (R-001...)
- **Antes de `/revision-independiente`**: los tests ya deben existir y pasar

## Notas importantes

- **Tests primero, código después**: nunca al revés
- **Los tests deben fallar primero**: si pasan sin implementación, no están testeando nada
- **Ejemplos aprobados son sagrados**: no los cambies sin permiso
- **Implementación mínima**: no agregues features extra "por si acaso"
- **DETENTE dos veces**: después de ejemplos y después de tests
- **Si existe reglas.md**: usa las reglas como fuente de verdad y referéncialas en los tests
