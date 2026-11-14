# Ejemplo: revisión independiente

## Cómo se abre

En un chat nuevo, con un modelo distinto del que escribió el código, la persona escribe:

> Revisa estos cambios con `/revision-independiente`.
> Diff: (pega el diff de la rama `tarea-2-productos`)
> Contrato: `docs/contrato.md`
> Reglas: `docs/reglas.md`

El código no se escribió en este chat, así que la revisión sigue.

## Revisión por áreas

Archivos revisados:
- `src/services/inventory.service.ts` (120 líneas añadidas)
- `src/types/inventory.types.ts` (30 líneas añadidas)
- `tests/inventory.test.ts` (85 líneas añadidas)

1. **Correctitud.** `receiveProduct` sigue el flujo básico del contrato. No implementa la regla `R-011`
   (descuento por volumen), marcada como «Mantener» en `docs/reglas.md`. Los casos frontera básicos están cubiertos.
2. **Errores y contrato.** Los códigos 400 y 404 son correctos. El contrato pide 409 para SKU duplicado y el código
   devuelve 400. Los campos obligatorios validan nulos.
3. **Seguridad.** No hay credenciales en el código. `productId` llega del request sin validar el tipo y se usa
   directamente en la consulta.
4. **Tests.** Cubren los casos básicos. Falta el test de `R-011` y el caso en que la base de datos falla al actualizar.
5. **Legibilidad.** Nombres claros. La función principal tiene unas 75 líneas y mezcla validación y descuento.

## Hallazgos

1. [Gravedad: alta] src/services/inventory.service.ts:45-67: No implementa la regla R-011 de descuento por volumen.
   Por qué importa: `docs/reglas.md` la marca como «Mantener». Sugerencia: aplicar el 5 % de descuento cuando
   `quantity >= 100`, como indica la regla.
2. [Gravedad: alta] src/services/inventory.service.ts:82: Devuelve 400 para SKU duplicado y el contrato pide 409.
   Por qué importa: otras partes de la app esperan el código del contrato. Sugerencia: lanzar un error de conflicto
   (409) con el código `DUPLICATE_SKU`.
3. [Gravedad: media] tests/inventory.test.ts: Falta el test de la regla R-011. Por qué importa: una regla de negocio
   sin cobertura. Sugerencia: probar `quantity: 150` y comprobar el 5 % de descuento.
4. [Gravedad: media] src/services/inventory.service.ts:34: `productId` se usa sin validar el tipo. Por qué importa:
   si llega como texto, la consulta falla o se comporta distinto. Sugerencia: validar que es un entero antes de usarlo.
5. [Gravedad: baja] src/services/inventory.service.ts:45-120: Función larga que mezcla validación y descuento.
   Por qué importa: cuesta más de mantener. Sugerencia: separar el cálculo del descuento en su propia función.

## Decisión

No lo aprobaría para merge: los hallazgos 1 y 2 son de gravedad alta (falta una regla documentada y se rompe el
contrato). Los hallazgos 3 a 5 son deseables, pero no bloquean.

Si no hubiera hallazgos de gravedad alta, la decisión sería: «Lo aprobaría para merge: no hay hallazgos de gravedad
alta; los de media y baja pueden ir en un cambio posterior».
