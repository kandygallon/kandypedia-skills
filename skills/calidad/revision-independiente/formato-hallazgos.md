# Formato de Hallazgos

## Estructura

```
[Gravedad: alta/media/baja] archivo:línea: problema. Por qué importa. Sugerencia concreta.
```

## Ejemplo

```
[Gravedad: alta] src/services/inventory.service.ts:45: No valida que quantity sea entero, acepta decimales. Por qué importa: el contrato especifica solo enteros. Sugerencia: agregar `if (!Number.isInteger(quantity))` antes de procesar.
```

## Reglas de Gravedad

### Alta (bloquea merge)
- Viola contrato o reglas documentadas
- Introduce vulnerabilidad de seguridad
- Causa bug funcional en casos comunes
- Puede corromper datos

**Ejemplos:**
- No implementa regla R-011 marcada como "Mantener"
- Lanza error 400 cuando contrato especifica 409
- productId sin validar usado en query (SQL injection)
- Falta validación de campo obligatorio

### Media (deseable corregir)
- Falta test importante pero código parece correcto
- Caso frontera no manejado (poco probable)
- Inconsistencia menor con contrato/reglas
- Problema de performance que afecta UX

**Ejemplos:**
- Falta test para regla R-011
- No maneja caso donde quantity es Number.MAX_SAFE_INTEGER
- Campo opcional en type pero obligatorio en contrato

### Baja (opcional)
- Mejora de legibilidad
- Optimización que no afecta funcionamiento
- Preferencia de estilo (sin linter)
- Comentario faltante en código complejo

**Ejemplos:**
- Función larga que podría separarse
- Variable podría tener nombre más claro
- Lógica simple que podría ser inline
