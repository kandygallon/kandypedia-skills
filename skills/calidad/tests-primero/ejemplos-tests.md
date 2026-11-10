# Ejemplos de Tests

## Ejemplo con reglas.md

**Requisito:** Implementar recepción de inventario según reglas.md

**Ejemplo 1: Caso exitoso (R-001, R-010)**

**Entrada:**
```json
{
  "productId": 456,
  "quantity": 50
}
```

**Salida:**
```json
{
  "newStock": 150,
  "receipt": {"id": 789, "status": "RECEIVED"}
}
```

**Ejemplo 2: Cantidad cero (R-001)**

**Entrada:**
```json
{"productId": 456, "quantity": 0}
```

**Salida:** Error 400: "Quantity must be positive"

**Ejemplo 3: Producto inexistente (R-002)**

**Entrada:**
```json
{"productId": 999, "quantity": 50}
```

**Salida:** Error 404: "Product not found"

## Tests correspondientes

```typescript
describe('receiveProduct', () => {
  test('debe aumentar stock (R-001, R-010)', async () => {
    const result = await receiveProduct({
      productId: 456,
      quantity: 50
    });
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
});
```

## Suposiciones

1. productId debe existir antes de recibir stock
2. quantity siempre se suma
3. receiptId se genera automáticamente

## Dudas

1. ¿Qué pasa si quantity es decimal?
2. ¿Hay límite máximo de quantity?
