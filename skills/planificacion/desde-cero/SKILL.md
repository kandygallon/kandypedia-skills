---
name: desde-cero
description: Usa esta skill cuando comiences un proyecto nuevo desde cero. Hace las preguntas correctas antes de escribir código y produce una especificación mínima.
---

# Desde Cero

## ¿Cuándo usar esta skill?

Úsala cuando:
- Vas a empezar un proyecto completamente nuevo
- Es un MVP, prototipo o primera versión
- No hay código base existente

**No la uses** cuando hay código existente (usa `/analizar-existente`).

## Filosofía

El error más común es escribir código demasiado pronto. Antes de la primera línea, necesitas entender:
- **Qué** problema resuelve (por qué)
- **Para quién** (usuario)
- **Cuál** es la versión más pequeña que funciona (MVP)
- **Qué** restricciones existen (cómo)
- **Cómo** sabrás que está terminado (definición de hecho)

## Proceso: 6 preguntas

Haz preguntas **una por una**, espera respuestas, ajusta las siguientes.

### 1. El problema (por qué)

> "¿Qué problema estás resolviendo o qué necesidad tiene el usuario?"

Si es vaga, profundiza: "¿Qué frustra de las soluciones actuales?"

### 2. El usuario (para quién)

> "¿Quién va a usar esto? ¿Qué sabe y qué no?"

Si es ambiguo, aclara: "¿Desarrolladores junior o seniors?"

### 3. MVP (qué)

> "¿Cuál es la versión más pequeña que resuelve el problema? Si lanzaras mañana, ¿qué debe incluir?"

Si lista muchas cosas, recorta: "¿Cuál es la ÚNICA que tiene que estar?"

### 4. Restricciones (cómo)

> "¿Hay restricciones técnicas, de tiempo o recursos?"

Ejemplos: debe usar X stack, no puede costar, debe ser rápido.

### 5. Fuera de alcance (qué NO)

> "¿Qué cosas están fuera del alcance de esta primera versión?"

Lista lo que NO harás para evitar scope creep.

### 6. Definición de hecho

> "¿Cómo sabemos que está terminado? Dame un ejemplo: un usuario hace X, ve Y, resuelve su problema."

## Especificación

Después de las preguntas, produce documento con [plantilla-especificacion.md](plantilla-especificacion.md):

- Problema
- Usuario
- MVP
- Restricciones
- Fuera de alcance
- Definición de hecho
- Arquitectura sugerida

Ver [ejemplos-preguntas.md](ejemplos-preguntas.md) con diálogo completo.

## Entregables

1. ✅ Especificación con problema, usuario, MVP, restricciones, fuera de alcance, definición de hecho
2. ✅ Opciones de arquitectura con recomendación
3. ✅ Confirmación del usuario

**No escribas código todavía.**

## Transición

Después de especificación:
- Si el MVP tiene varias partes: `/reparto`
- Si es pequeño: `/tests-primero` para implementar

## Referencias

- [plantilla-especificacion.md](plantilla-especificacion.md): Estructura completa
- [ejemplos-preguntas.md](ejemplos-preguntas.md): Diálogo completo con usuario

## Notas

- **Pregunta una cosa a la vez**
- **Escucha respuestas**
- **Recomienda cuando sea útil**
- **Mantén MVP pequeño**
- **Documenta TODO**
