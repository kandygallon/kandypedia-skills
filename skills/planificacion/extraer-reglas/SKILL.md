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

**No la uses** cuando:
- Solo necesitas entender código para modificarlo (usa `/analizar-existente`)
- Estás empezando algo completamente nuevo sin legacy (usa `/desde-cero`)
- El legacy se va a mantener tal cual (no es una migración)

## Filosofía

En migraciones, el **código legacy es documentación ejecutable de las reglas de negocio**. Aunque el diseño sea malo, las reglas que implementa son las que el negocio espera.

**IMPORTANTE**: Esta skill **NO escribe código nuevo**. Solo extrae y documenta reglas.

## Proceso paso a paso

### 1. Preguntas iniciales

Antes de investigar, pregunta al usuario:

1. "¿Qué servicio o flujo legacy estás migrando?"
2. "¿Cuál es el alcance del nuevo servicio? ¿Migras todo o solo una parte?"
3. "¿Dónde guardo el documento de reglas? (ej: `docs/reglas.md`)"

Espera las respuestas.

### 2. Mapeo del servicio legacy

Investiga el código y mapea:

- **Puntos de entrada**: endpoints, comandos, funciones, parámetros
- **Validaciones**: qué se valida, qué causa rechazo, validaciones condicionales
- **Cálculos**: fórmulas, transformaciones, algoritmos
- **Transiciones de estado**: estados posibles, transiciones permitidas
- **Permisos**: roles, autorización, reglas por usuario
- **Manejo de errores**: qué errores se capturan, rollbacks, reintentos
- **Efectos secundarios**: emails, logs, integraciones externas
- **Reglas implícitas**: lógica en queries SQL, configs, defaults, valores hardcodeados

Ver [ejemplos de mapeo](ejemplos-mapeo.md) para cada categoría.

### 3. Documenta cada regla en `reglas.md`

Para cada regla, usa el [formato de regla](formato-regla.md):

```markdown
### R-001: [Nombre descriptivo]

**Descripción**: [en lenguaje de negocio]
**Ubicación**: archivo y líneas
**Entrada esperada**: [ejemplo]
**Salida esperada**: [ejemplo]
**Casos especiales**: [edge cases]
**Estado**: [ ] Mantener / [ ] Cambiar / [ ] Eliminar / [ ] Por confirmar
**Notas**: [bugs sospechosos, decisiones históricas]
```

Numera las reglas secuencialmente: R-001, R-002, R-003...

Ver [plantilla completa](plantilla-reglas.md) con estructura y categorías.

### 4. Dudas y bugs sospechosos

Lista comportamiento ambiguo con el formato:

```markdown
### D-001: ¿Es bug o feature?
**Comportamiento**: [qué hace el código]
**Código**: archivo y línea
**Pregunta**: [qué necesitas confirmar]
**Impacto**: consecuencias de cada respuesta
```

### 5. Checklist de verificación

Al final del documento, agrega tabla mapeando reglas a test cases:

```markdown
| Regla | Test Case | Pasó | Notas |
|-------|-----------|------|-------|
| R-001 | `test_nombre()` | ⬜ | |
```

### 6. Sugerencia de continuación

Al terminar, di:

> "✅ Documento de reglas completado: `[ruta]`
> 
> **Próximos pasos**:
> 1. Revisar con experto de dominio (resolver dudas)
> 2. Marcar estado de cada regla
> 3. Usar `/desde-cero` con `@reglas.md` como base"

## Entregables

Al terminar:

1. ✅ `reglas.md` con reglas numeradas (R-001...)
2. ✅ Cada regla con descripción, ubicación, ejemplos, estado
3. ✅ Dudas documentadas (D-001...)
4. ✅ Checklist de verificación
5. ✅ Sugerencia de próximos pasos

## Transición

Después de extraer reglas:
1. Revisar con experto de dominio
2. Marcar estado de cada regla
3. Usar `/desde-cero` con `@reglas.md` para especificar nuevo servicio
4. Si es grande, usar `/reparto` para dividir implementación

## Referencias

- [Ejemplos de mapeo](ejemplos-mapeo.md): ejemplos por cada categoría de mapeo
- [Formato de regla](formato-regla.md): plantilla detallada para documentar reglas
- [Plantilla completa](plantilla-reglas.md): estructura completa del documento reglas.md
- [Ejemplo completo](ejemplo-completo.md): caso real documentado de inicio a fin

## Notas importantes

- **No escribas código nuevo**: solo documenta
- **No juzgues el legacy**: documenta como duda, no descartes
- **Las reglas implícitas son peligrosas**: queries, configs, defaults escondidos
- **Bugs vs features**: si dudas, márcalo para confirmar
- **El legacy es la verdad**: las reglas que implementa son las que el negocio espera
- **Numera secuencialmente**: facilita referencias en documentos posteriores
