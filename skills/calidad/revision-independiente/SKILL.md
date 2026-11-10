---
name: revision-independiente
description: Usa esta skill antes de /antes-del-merge para revisar el código con ojos frescos, como si fueras el único revisor. Corre en un chat NUEVO con modelo diferente para verdadera independencia.
---

# Revisión independiente

## ¿Cuándo usar esta skill?

Úsala cuando:
- Terminaste de implementar
- Los tests pasan
- Quieres revisión objetiva antes de mergear

**No la uses** cuando todavía implementas o tests no pasan.

## ⚠️ Importante: Chat NUEVO

**"Ignore previous context" no funciona en el mismo chat.**

Para revisión verdaderamente independiente:

1. **Abre NUEVO chat** en Cursor
2. **Usa modelo diferente** (si escribiste con Claude, revisa con GPT o viceversa)
3. **Pasa solo**: diff, `docs/contrato.md`, `docs/reglas.md`, y esta skill
4. **No incluyas**: historial, comentarios previos, decisiones

## Filosofía

Revisa como si fueras el único revisor.

**Reglas:**
- No leas revisiones previas ni conclusiones anteriores
- Basa tu revisión solo en: código, requisito, tests

## Orden de revisión

Revisa en este orden:

### 1. Correctitud

¿Hace lo que pide el requisito?
- Si existe `docs/contrato.md`: ¿cumple contratos?
- Si existe `docs/reglas.md`: ¿implementa reglas marcadas como "Mantener"?
- ¿Qué casos frontera fallarían?

### 2. Manejo de errores

- ¿Códigos de respuesta correctos (200, 400, 404, 500)?
- ¿Tipos coinciden con contrato?
- ¿Se validan nulos?
- ¿Mensajes de error claros?

### 3. Seguridad

- ¿Entradas sin validar?
- ¿Secretos hardcodeados?
- ¿Se verifican permisos?
- ¿Posibilidad de injection?

### 4. Tests

¿Qué comportamiento importante no está cubierto?
- Si existe `docs/reglas.md`: ¿hay tests por regla?
- ¿Se prueban casos de error?
- ¿Se prueban casos frontera?

### 5. Legibilidad

**Solo si afecta de verdad.**
- ¿Código duplicado?
- ¿Funciones muy largas?
- ¿Nombres claros?

## Formato de salida

Lista numerada:

```
[Gravedad: alta / media / baja] archivo:línea: problema. Por qué importa. Sugerencia concreta.
```

Ver [formato-hallazgos.md](formato-hallazgos.md) con reglas de gravedad y [ejemplos-revision.md](ejemplos-revision.md).

### Decisión final

**Al final, una línea:**

```
❌ No aprobaría: [razón]
```

O:

```
✅ Aprobaría: [razón]
```

## Reglas de gravedad

- **Alta**: Viola contrato/reglas, vulnerabilidad, bug funcional, corrupción de datos
- **Media**: Falta test importante, caso frontera, inconsistencia menor
- **Baja**: Mejora de legibilidad, optimización, preferencia de estilo

## Importante: NO modifiques código

**Solo reporta.** El agente original o usuario decidirán qué hacer.

## Entregables

1. ✅ Revisión en 5 áreas
2. ✅ Lista de hallazgos con formato
3. ✅ Decisión: aprobar o no

## Transición

Después:
1. Agente original corrige hallazgos de gravedad alta
2. Opcional: corrige media/baja
3. Usa `/antes-del-merge`
4. Procede con `/integrar`

## Referencias

- [formato-hallazgos.md](formato-hallazgos.md): Formato detallado y reglas de gravedad
- [ejemplos-revision.md](ejemplos-revision.md): Ejemplos completos de revisión

## Notas

- **Chat NUEVO con modelo diferente**: única forma de independencia real
- **No modifiques**: solo reporta
- **Gravedad alta bloquea merge**
- **Usa contrato.md y reglas.md**: son fuente de verdad
