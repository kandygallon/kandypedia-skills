---
name: desde-cero
description: Hace las preguntas clave antes de escribir código en un proyecto nuevo (problema, usuario, versión mínima, restricciones, fuera de alcance, definición de hecho) y deja la especificación en docs/especificacion.md. Úsala al empezar una app o herramienta sin código previo.
disable-model-invocation: true
---

# Desde cero

## Cuándo usarla
- Vas a empezar un proyecto nuevo y no hay código previo.
- Alguien dice «quiero crear», «necesito construir», «vamos a hacer».
- Es un MVP, un prototipo o una primera versión.

**No la uses** si ya hay código que modificar: usa `/analizar-existente`.

## Pasos
1. No escribas código. Haz estas seis preguntas **de una en una**, en este orden. Espera cada respuesta
   antes de la siguiente y ajusta la siguiente pregunta a lo que oíste.
   1. **Problema (por qué).** «¿Qué problema quieres resolver o qué necesita quien lo va a usar?»
      Si es vago («una app de tareas»), profundiza: «¿Qué te frustra de las que ya existen?».
      Un buen problema es específico. «Quiero una app» no lo es.
   2. **Usuario (para quién).** «¿Quién lo va a usar? ¿Qué sabe y qué no sabe?»
      Si es ambiguo («desarrolladores»), aclara: «¿Gente que está aprendiendo o gente que usa git y
      agentes cada día?». «Cualquiera» no es un usuario.
   3. **Versión mínima (qué).** «¿Cuál es la versión más pequeña que ya resuelve el problema? Si lo
      lanzaras mañana, ¿qué debe incluir y qué puede esperar?»
      Si llega una lista larga, recorta: «De estas, ¿cuál tiene que estar sí o sí? Las demás pueden
      venir después».
   4. **Restricciones (cómo).** «¿Hay restricciones técnicas, de tiempo o de recursos?» Por ejemplo:
      solo frontend, que funcione sin conexión, un stack que ya conocen, dos semanas.
      Si no hay ninguna, confirma: «¿Puedo elegir libremente las tecnologías?». «Que sea perfecto» no
      es una restricción.
   5. **Fuera de alcance.** «¿Qué queda fuera de esta primera versión?» Si duda, sugiere ejemplos:
      autenticación, deploys automáticos, versionado complejo. Escribir lo que no harás evita que el
      alcance crezca solo.
   6. **Definición de hecho.** «¿Cómo sabemos que está terminado? ¿Qué debe poder hacer alguien para
      que digas "sí, esto funciona"?» Si responde «que funcione», pide un flujo concreto: «Alguien
      abre la app, hace X, ve Y y eso le resuelve el problema».
2. Si la persona se pierde, recomienda opciones concretas. Empuja siempre hacia la versión más simple
   que funciona.
3. Escribe `docs/especificacion.md`, corta, con estas secciones:
   - **Problema**, en 2 o 3 frases.
   - **Usuario**: quién es, qué sabe, qué necesita.
   - **Versión mínima**: la lista más corta posible.
   - **Restricciones**: stack, tiempo, dependencias.
   - **Fuera de alcance**.
   - **Definición de hecho**: «Alguien puede [acción] y obtiene [resultado], lo que resuelve [problema]».
   - **Arquitectura sugerida**: dos opciones con pros y contras, y tu recomendación con el porqué.

   Lo que no está escrito no existe: la especificación es el contrato.

**DETENTE.** Presenta la especificación y espera la aprobación de la persona. El alcance, lo que queda
fuera y la definición de hecho los decide ella, no tú.

4. Con la especificación aprobada, commitéala en `main` antes de repartir trabajo o crear worktrees:
   ```bash
   git add docs/especificacion.md
   git commit -m "Especificación: <proyecto>"
   ```

## Qué produce
- `docs/especificacion.md` con problema, usuario, versión mínima, restricciones, fuera de alcance,
  definición de hecho y arquitectura sugerida, aprobada por la persona y commiteada en `main`.
- Ni una línea de código todavía.

## Siguiente
- `/reparto` si la versión mínima tiene varias partes independientes.
- Si es una sola pieza pequeña, impleméntala directamente.

Si necesitas un ejemplo completo, lee [references/ejemplo.md](references/ejemplo.md).
