# Ejemplo: tests primero

Tarea: «Implementa la recepción de inventario según el contrato». Existen `docs/contrato.md` y `docs/reglas.md`
con las reglas `R-001` (la cantidad debe ser positiva), `R-002` (el producto debe existir) y `R-010`
(el stock nuevo es el actual más la cantidad; sin stock previo cuenta como 0).

## 1. Requisito con mis palabras

La función `receiveProduct` recibe `productId` y `quantity`. Valida que la cantidad sea positiva y que el producto
exista. Suma la cantidad al stock actual y devuelve el stock nuevo y un recibo con estado `RECEIVED`.
Rechaza cantidades de 0 o menos y productos inexistentes.

## 2. Ejemplos de entrada y salida

| # | Caso | Entrada | Salida esperada | Reglas |
|---|------|---------|-----------------|--------|
| 1 | Caso normal (stock actual 100) | `{ productId: 456, quantity: 50 }` | `{ newStock: 150, receipt: { status: "RECEIVED" } }` | R-001, R-010 |
| 2 | Cantidad cero | `{ productId: 456, quantity: 0 }` | Error 400: «La cantidad debe ser positiva» | R-001 |
| 3 | Producto inexistente | `{ productId: 999, quantity: 50 }` | Error 404: «Producto no encontrado» | R-002 |
| 4 | Producto sin stock previo | `{ productId: 123, quantity: 10 }` | `{ newStock: 10, receipt: { status: "RECEIVED" } }` | R-010 |

## 3. Suposiciones y dudas

Suposiciones:
1. El producto debe existir antes de recibir stock.
2. La cantidad siempre se suma, nunca se resta.
3. El id del recibo se genera automáticamente.

Dudas:
1. ¿Qué pasa si `quantity` es decimal (por ejemplo, 50.5)? ¿Se redondea o se rechaza?
2. ¿Hay un límite máximo por recepción?

**DETENTE.** «He listado los ejemplos y las dudas. ¿Los confirmas o hay que ajustar algo?»

La persona responde: «Confirmo. Los decimales se rechazan con error 400. No hay límite por ahora».
Se añade el ejemplo 5: `{ productId: 456, quantity: 50.5 }` → error 400 «La cantidad debe ser un número entero» (R-001).

## 4. Solo los tests

```typescript
// tests/inventory.test.ts
describe('receiveProduct', () => {
  test('suma la cantidad al stock actual (R-001, R-010)', async () => {
    const result = await receiveProduct({ productId: 456, quantity: 50 });
    expect(result.newStock).toBe(150);
    expect(result.receipt.status).toBe('RECEIVED');
  });

  test('rechaza cantidad cero (R-001)', async () => {
    await expect(receiveProduct({ productId: 456, quantity: 0 }))
      .rejects.toThrow('La cantidad debe ser positiva');
  });

  test('rechaza cantidad decimal (R-001)', async () => {
    await expect(receiveProduct({ productId: 456, quantity: 50.5 }))
      .rejects.toThrow('La cantidad debe ser un número entero');
  });

  test('rechaza producto inexistente (R-002)', async () => {
    await expect(receiveProduct({ productId: 999, quantity: 50 }))
      .rejects.toThrow('Producto no encontrado');
  });

  test('trata un producto sin stock previo como 0 (R-010)', async () => {
    const result = await receiveProduct({ productId: 123, quantity: 10 });
    expect(result.newStock).toBe(10);
  });
});
```

## 5. Ejecutar: fallan

```bash
npm test
```

```
FAIL tests/inventory.test.ts
  ✗ suma la cantidad al stock actual (R-001, R-010)
    receiveProduct is not defined
```

**DETENTE.** «Los tests están escritos y fallan como se esperaba. ¿Apruebas que implemente?»

La persona responde: «Aprobado».

## 6. Implementación mínima y ejecutar: pasan

Se implementa `receiveProduct` en `src/services/inventory.service.ts` con lo justo para los cinco tests.

```bash
npm test
```

```
PASS tests/inventory.test.ts
  ✓ suma la cantidad al stock actual (R-001, R-010)
  ✓ rechaza cantidad cero (R-001)
  ✓ rechaza cantidad decimal (R-001)
  ✓ rechaza producto inexistente (R-002)
  ✓ trata un producto sin stock previo como 0 (R-010)
```

## 7. Resumen del diff

- `tests/inventory.test.ts` (nuevo): 5 tests sacados de los ejemplos aprobados, con referencias a R-001, R-002 y R-010.
- `src/services/inventory.service.ts` (nuevo): `receiveProduct`, que
  - valida que la cantidad sea un entero mayor que 0 (R-001),
  - comprueba que el producto existe (R-002),
  - calcula `newStock = currentStock + quantity`, con stock nulo como 0 (R-010).
- `src/types/inventory.types.ts` (nuevo): tipos de entrada y salida de `receiveProduct`.

Falta probar en la app: recibir stock desde la pantalla de inventario y ver que el número se actualiza.
