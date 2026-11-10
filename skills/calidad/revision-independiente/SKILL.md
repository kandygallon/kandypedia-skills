---
name: revision-independiente
description: Usa esta skill antes de /antes-del-merge para revisar el código con ojos frescos, como si fueras el único revisor. Corre en un chat NUEVO con modelo diferente para verdadera independencia.
---

# Revisión independiente

## ¿Cuándo usar esta skill?

Úsala cuando:
- ✅ Terminaste de implementar tu tarea
- ✅ Los tests pasan
- ✅ Quieres una revisión objetiva antes de mergear

**No la uses** cuando:
- ❌ Todavía estás implementando (no terminaste)
- ❌ Los tests no pasan todavía

## ⚠️ Importante: Ejecuta en un chat NUEVO

**"Ignore previous context" no funciona completamente en el mismo chat.**

Para una revisión verdaderamente independiente:

1. **Abre un NUEVO chat** en Cursor
2. **Usa un modelo diferente** al que escribió el código (ej: si escribiste con Claude Sonnet, revisa con GPT o viceversa)
3. **Pasa solo**: el diff o lista de archivos, `docs/contrato.md` si existe, `docs/reglas.md` si existe, y esta skill
4. **No incluyas**: el historial de implementación, comentarios previos, o decisiones del chat original

## Filosofía

Revisa los cambios de esta rama (o el diff que te indique) como si fueras el único revisor.

## Reglas

- **No leas ni tomes en cuenta** revisiones previas, comentarios de otros agentes ni conclusiones anteriores de este chat.
- **Basa tu revisión solo en**: el código, el requisito (contrato.md o reglas.md), y los tests.

## Orden de revisión

Revisa en este orden:

### 1. Correctitud

**¿Hace lo que pide el requisito?**

- Si existe `docs/contrato.md`: ¿cumple con los contratos definidos (entrada, salida, errores)?
- Si existe `docs/reglas.md`: ¿implementa todas las reglas marcadas como "Mantener" o "Cambiar"?
- ¿Qué casos frontera fallarían?

### 2. Manejo de errores y contratos

- ¿Los códigos de respuesta son correctos (200, 400, 404, 500)?
- ¿Los tipos coinciden con el contrato?
- ¿Se validan valores nulos o undefined?
- ¿Los mensajes de error son claros?

### 3. Seguridad

- ¿Hay entradas sin validar?
- ¿Hay secretos o credenciales hardcodeadas?
- ¿Se verifican permisos donde corresponde?
- ¿Hay posibilidad de injection (SQL, XSS, etc.)?

### 4. Tests

**¿Qué comportamiento importante no está cubierto?**

- Si existe `docs/reglas.md`: ¿hay tests para cada regla del checklist?
- ¿Se prueban casos de error?
- ¿Se prueban casos frontera (límites, nulos, vacíos)?

### 5. Legibilidad y mantenimiento

**Solo si afecta de verdad.**

- ¿Hay código duplicado?
- ¿Las funciones son demasiado largas o complejas?
- ¿Los nombres son claros?

## Formato de salida

Una lista numerada:

```
[Gravedad: alta / media / baja] archivo:línea: problema. Por qué importa. Sugerencia concreta.
```

**Ejemplo:**

```markdown
## Hallazgos

1. [Gravedad: alta] src/services/inventory.service.ts:45: No valida que quantity sea un número entero, acepta decimales. Por qué importa: el contrato especifica solo enteros. Sugerencia: agregar validación `if (!Number.isInteger(quantity))` antes de procesar.

2. [Gravedad: alta] src/services/inventory.service.ts:67: No maneja el caso donde el producto existe pero está archivado (R-050). Por qué importa: reglas.md especifica que productos archivados deben rechazarse. Sugerencia: agregar check `if (product.archived)` y lanzar error 404.

3. [Gravedad: media] src/services/inventory.service.ts:52: No hay test para el caso donde quantity es Number.MAX_SAFE_INTEGER. Por qué importa: caso frontera que podría causar overflow. Sugerencia: agregar test con valor extremo.

4. [Gravedad: media] src/types/inventory.types.ts:8: El campo threshold es obligatorio pero el contrato lo marca como opcional. Por qué importa: inconsistencia con docs/contrato.md. Sugerencia: cambiar a `threshold?: number`.

5. [Gravedad: baja] src/services/inventory.service.ts:78: Función calculateNewStock tiene lógica simple que podría ser inline. Por qué importa: facilita lectura al tener menos indirección. Sugerencia: considerar inline o agregar comentario explicando por qué es función separada.
```

## Decisión final

**Al final, di en una línea si lo aprobarías para merge o no, y por qué.**

**Ejemplo:**

```markdown
## Decisión

❌ **No aprobaría para merge**: los hallazgos 1 y 2 (gravedad alta) violan el contrato y reglas documentadas. Deben corregirse antes de mergear. Los demás son mejoras deseables pero no bloqueantes.
```

O:

```markdown
## Decisión

✅ **Aprobaría para merge**: no hay hallazgos de gravedad alta. Los hallazgos de gravedad media/baja pueden abordarse en un PR de seguimiento o ignorarse según prioridades del equipo.
```

## Reglas de gravedad

### Alta
- Viola el contrato o las reglas documentadas
- Introduce vulnerabilidad de seguridad
- Causa bug funcional en casos comunes
- Puede corromper datos

### Media
- Falta test importante pero el código parece correcto
- Caso frontera no manejado pero poco probable
- Inconsistencia menor con contrato/reglas
- Problema de performance que afecta experiencia de usuario

### Baja
- Mejora de legibilidad
- Optimización que no afecta funcionamiento
- Preferencia de estilo (si no hay linter)
- Comentario faltante en código complejo

## Importante: NO hagas cambios en el código

**Solo reporta.**

No modifiques archivos, no escribas código de solución, solo documenta los hallazgos.

El agente original o el usuario decidirán qué hacer con el reporte.

## Ejemplo completo

**Contexto (en chat NUEVO):**
> Revisa estos cambios usando `/revision-independiente`:
> 
> Diff: [pega el diff]
> 
> Requisito: @docs/contrato.md
> 
> Reglas: @docs/reglas.md

**Agente revisor (usando esta skill):**

**Revisión de cambios en rama task-2-products**

**Archivos revisados:**
- src/services/inventory.service.ts (120 líneas agregadas)
- src/types/inventory.types.ts (30 líneas agregadas)
- tests/inventory.test.ts (85 líneas agregadas)

---

### 1. Correctitud

✅ La función `receiveProduct` implementa el flujo básico correctamente según contrato.md.

❌ Problema: no implementa la regla R-011 (descuento por volumen) documentada en reglas.md como "Mantener".

✅ Los casos frontera básicos están cubiertos.

---

### 2. Manejo de errores y contratos

✅ Códigos de respuesta correctos (400 para validación, 404 para not found).

❌ Problema: el contrato especifica error 409 para SKU duplicado, pero el código lanza 400.

✅ Validación de nulos en campos obligatorios.

---

### 3. Seguridad

✅ No hay credenciales hardcodeadas.

⚠️  Advertencia: el campo `productId` se usa directamente en query sin sanitizar. Aunque el tipo es number, podría venir como string del request.

✅ No hay otras vulnerabilidades obvias.

---

### 4. Tests

✅ Tests cubren casos básicos.

❌ Problema: falta test para regla R-011 (descuento por volumen >= 100 unidades).

⚠️  Advertencia: no hay test para el caso donde la DB lanza error durante el update.

---

### 5. Legibilidad y mantenimiento

✅ Código claro y bien nombrado.

⚠️  Observación: la función tiene 120 líneas, considera separar la lógica de descuento en función aparte.

---

## Hallazgos

1. [Gravedad: alta] src/services/inventory.service.ts:45-67: No implementa la regla R-011 de descuento por volumen. Por qué importa: reglas.md la marca como "Mantener" y es requisito documentado. Sugerencia: agregar lógica `if (quantity >= 100) { applyDiscount(0.05) }` según reglas.md líneas 89-95.

2. [Gravedad: alta] src/services/inventory.service.ts:82: Lanza error 400 para SKU duplicado, pero contrato.md especifica 409 Conflict. Por qué importa: inconsistencia con contrato que otros servicios/UI esperan. Sugerencia: cambiar a `throw new ConflictError('DUPLICATE_SKU', ...)`.

3. [Gravedad: media] tests/inventory.test.ts: Falta test para regla R-011 (descuento por volumen). Por qué importa: regla crítica sin cobertura de test. Sugerencia: agregar test `receiveProduct({ quantity: 150 })` y verificar que el costo tiene 5% descuento.

4. [Gravedad: media] src/services/inventory.service.ts:34: El productId viene del request como any y se usa directamente. Por qué importa: aunque el tipo es number, podría venir string y causar error en DB. Sugerencia: validar `if (typeof productId !== 'number')` o usar parseInt con validación.

5. [Gravedad: baja] src/services/inventory.service.ts:45-120: Función larga (120 líneas). Por qué importa: facilita mantenimiento separar responsabilidades. Sugerencia: considerar extraer lógica de descuento y validaciones a funciones helper.

---

## Decisión

❌ **No aprobaría para merge**: los hallazgos 1 y 2 son de gravedad alta. El hallazgo 1 omite una regla de negocio documentada, y el hallazgo 2 rompe el contrato API. Deben corregirse. Los hallazgos 3-5 son deseables pero no bloqueantes.

## Entregables

Al terminar de usar esta skill, debes tener:

1. ✅ Revisión completa en 5 áreas (correctitud, errores, seguridad, tests, legibilidad)
2. ✅ Lista de hallazgos con gravedad, ubicación, problema, impacto y sugerencia
3. ✅ Decisión clara: aprobar o no aprobar para merge con justificación

## Transición a las siguientes skills

Después de la revisión independiente:
1. El agente original o el usuario **corrige los hallazgos de gravedad alta**
2. Opcionalmente corrige gravedad media/baja según prioridades
3. Usa `/antes-del-merge` para verificar que todo está listo
4. Procede con la integración usando `/integrar`

## Relación con otras skills

- **Después de `/tests-primero`**: los tests ya deben existir y pasar
- **Antes de `/antes-del-merge`**: esta revisión es más profunda, antes-del-merge es checklist mecánico
- **Usa `docs/contrato.md`**: como fuente de verdad para validar contratos
- **Usa `docs/reglas.md`**: como fuente de verdad para validar reglas de negocio

## Notas importantes

- **Ejecuta en chat NUEVO con modelo diferente**: es la única forma de tener independencia real
- **No modifiques código**: solo reporta hallazgos
- **Usa el formato estricto**: `[Gravedad] archivo:línea: problema. Por qué. Sugerencia.`
- **Sé objetivo**: no defiendas ni ataques, solo evalúa contra requisitos
- **Gravedad alta bloquea merge**: si hay alta, debe corregirse antes de integrar
- **Si existe contrato.md o reglas.md**: son la fuente de verdad, el código debe cumplirlos
