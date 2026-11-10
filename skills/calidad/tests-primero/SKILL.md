---
name: tests-primero
description: Usa esta skill antes de implementar cualquier código. Define ejemplos de entrada/salida, escribe tests que fallan, y luego implementa lo mínimo para que pasen.
---

# Tests primero

## ¿Cuándo usar esta skill?

Úsala cuando:
- Ya tienes especificación
- Estás en tu worktree listo para implementar
- Quieres asegurar que el código funciona

**No la uses** en fase de planificación o código exploratorio.

## Filosofía

Antes de escribir código:

1. **Explica** el requisito (3-5 líneas)
2. **Ejemplos** con casos frontera
3. **Suposiciones y dudas**
4. **DETENTE** para aprobación
5. **Tests** (sin implementación)
6. **Ejecuta** (deben FALLAR)
7. **DETENTE** para aprobar tests
8. **Implementa** lo mínimo
9. **Ejecuta** (deben PASAR)
10. **Resume** cambios

## Proceso

### Paso 1: Explica requisito

"[Requisito en 3-5 líneas con tus propias palabras]"

### Paso 2: Ejemplos

Al menos 3 ejemplos (entrada/salida/errores, casos frontera).

**Si existe `docs/reglas.md`**: deriva de reglas y referencia IDs (R-001...).

Ver [ejemplos-tests.md](ejemplos-tests.md) con formato detallado.

### Paso 3: Suposiciones y dudas

Lista:
- Suposiciones que haces
- Dudas sobre el requisito

### Paso 4: DETENTE

Espera confirmación antes de continuar.

### Paso 5: Escribe tests

SOLO tests, sin implementación.

### Paso 6: Ejecuta (deben FALLAR)

```bash
npm test
# FAIL: confirma que testean algo
```

### Paso 7: DETENTE

Espera aprobación de tests.

### Paso 8: Implementa

Implementación mínima para que pasen.

### Paso 9: Ejecuta (deben PASAR)

```bash
npm test
# PASS: todos los tests
```

### Paso 10: Resume

Qué archivos cambiaste y por qué.

## Reglas

- **No modifiques tests aprobados**
- **Implementación mínima**: solo lo necesario
- **DETENTE en cada paso**: no avances sin OK

## Entregables

1. ✅ Requisito explicado
2. ✅ Ejemplos aprobados
3. ✅ Tests escritos
4. ✅ Tests fallando antes de implementar
5. ✅ Implementación mínima
6. ✅ Tests pasando
7. ✅ Resumen de cambios

## Transición

Después:
- Continúa con más features (mismo proceso)
- Al terminar: `/revision-independiente`
- Luego: `/antes-del-merge`

## Referencias

- [ejemplos-tests.md](ejemplos-tests.md): Formato de ejemplos y tests
- [formato-ejemplos.md](formato-ejemplos.md): Estructura de entrada/salida/errores

## Notas

- **Tests primero, código después**
- **Los tests deben fallar primero**
- **Ejemplos aprobados son sagrados**
- **Implementación mínima**
- **Si existe reglas.md**: úsalo y referencia IDs
