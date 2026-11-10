# Plantilla: Documento de Reglas

Estructura completa para el archivo `reglas.md`.

```markdown
# Reglas de Negocio: [Nombre del Servicio Legacy]

## Metadata
- **Servicio legacy**: [nombre del servicio/endpoint]
- **Nuevo servicio**: [nombre o alcance]
- **Fecha de extracción**: [fecha]
- **Autor**: [quien extrajo las reglas]

## 1. Validaciones de Entrada

### R-001: [Nombre de validación]
[Usar formato-regla.md]

### R-002: [Otra validación]
[...]

## 2. Cálculos de Negocio

### R-010: [Nombre de cálculo]
[...]

## 3. Transiciones de Estado

### R-020: [Nombre de transición]
[...]

## 4. Permisos y Autorización

### R-030: [Nombre de permiso]
[...]

## 5. Efectos Secundarios

### R-040: [Nombre de efecto]
[...]

## 6. Reglas Implícitas

### R-050: [Nombre de regla implícita]
[...]

## ⚠️ Dudas para Expertos de Dominio

### D-001: ¿Es bug o feature?
**Comportamiento observado:**
[Qué hace el código]

**Código:**
[Archivo y línea]

**Pregunta:**
[Qué necesitas confirmar]

**Impacto:**
- Si es bug: [consecuencia]
- Si es feature: [consecuencia]

---

### D-002: [Otra duda]
[...]

## ✅ Checklist de Verificación

| Regla | Test Case | Pasó | Notas |
|-------|-----------|------|-------|
| R-001 | `test_nombre()` | ⬜ | |
| R-002 | `test_nombre()` | ⬜ | |
| ...   | ...             | ⬜ | |

**Instrucciones:**
1. Implementar cada test en el nuevo servicio
2. Marcar ✅ cuando el test pase
3. Agregar notas si hubo cambios respecto al legacy
```

## Convenciones de numeración

- **Reglas (R-NNN)**: 001, 002, 003... secuencial
- **Dudas (D-NNN)**: 001, 002, 003... secuencial
- Agrupa por decenas: R-001 a R-009 validaciones, R-010 a R-019 cálculos, etc.
- Deja espacio entre grupos para agregar reglas después

## Categorías sugeridas

1. Validaciones de entrada (R-001 a R-009)
2. Cálculos de negocio (R-010 a R-019)
3. Transiciones de estado (R-020 a R-029)
4. Permisos y autorización (R-030 a R-039)
5. Efectos secundarios (R-040 a R-049)
6. Reglas implícitas (R-050 a R-059)
7. [Otras categorías según necesidad]
