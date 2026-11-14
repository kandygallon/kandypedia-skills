---
name: extraer-reglas
description: Extrae las reglas de negocio de un sistema legacy antes de migrarlo: validaciones, cálculos, estados, permisos, errores, efectos y reglas implícitas. Las numera con su ubicación y casos especiales en docs/reglas.md. Úsala cuando vas a reescribir un servicio que debe respetar lo que hace hoy.
disable-model-invocation: true
---

# Extraer reglas

## Cuándo usarla
- Vas a migrar un servicio legacy a una implementación nueva.
- El servicio nuevo debe respetar las mismas reglas de negocio que el viejo.
- El código legacy es la fuente de verdad del **qué** hace el sistema, no del **cómo** debe hacerse.

**No la uses** si solo vas a modificar el código que ya hay (usa `/analizar-existente`), si empiezas
algo nuevo sin legacy (usa `/desde-cero`) o si el legacy se queda como está.

## Pasos
1. Pregunta, **de una en una** y esperando cada respuesta:
   - «¿Qué servicio o flujo legacy vas a migrar? Dime el servicio, módulo o endpoint principal».
   - «¿Migras todo o solo una parte? ¿Qué queda fuera del servicio nuevo?»

   El documento va siempre a `docs/reglas.md`.
2. **Mapea el legacy sin juzgarlo.** Aunque el diseño sea malo, sus reglas son las que el negocio
   espera. Recorre, en este orden:
   - **Puntos de entrada:** endpoints, comandos o funciones; cómo se invocan (HTTP, eventos, cron);
     qué parámetros reciben.
   - **Validaciones:** qué se valida, qué provoca un rechazo, validaciones condicionales
     (solo si X, valida Y).
   - **Cálculos y transformaciones:** fórmulas, redondeos, algoritmos.
   - **Transiciones de estado:** qué estados existen y qué transiciones están permitidas.
   - **Permisos:** quién puede hacer qué; roles; umbrales que piden aprobación.
   - **Errores:** qué se captura; rollbacks, reintentos o compensaciones; qué falla sin bloquear.
   - **Efectos secundarios:** correos, notificaciones, auditoría, llamadas a otros servicios.
   - **Reglas implícitas, las más peligrosas:** filtros en consultas SQL (`WHERE`, `GROUP BY`),
     archivos de configuración, valores por defecto o fijos en el código, comportamiento que depende
     de los datos y no del código.
3. **Documenta cada regla en `docs/reglas.md`** con un número secuencial (R-001, R-002…), agrupadas
   por las categorías del paso 2. Encabeza el documento con: servicio legacy, alcance del servicio
   nuevo y fecha de extracción. Cada regla lleva:
   - **Descripción** en lenguaje de negocio: qué hace, no cómo está implementada.
   - **Ubicación en el legacy:** archivo, función y líneas (todas, si está repartida).
   - **Entrada y salida esperadas**, con un ejemplo.
   - **Casos especiales**: qué pasa con cero, vacío, inexistente, cerrado…
   - **Estado para el servicio nuevo:** Mantener, Cambiar (y cómo), Eliminar o Por confirmar.
     Tú solo propones; el estado lo decide la persona.
   - **Notas:** rarezas, bugs sospechosos, decisiones históricas.
4. **Lo dudoso no se descarta: se pregunta.** Si no sabes si algo es un bug o es intencional, anótalo
   en una sección de dudas (D-001, D-002…) con el comportamiento observado, el código, la pregunta y
   el impacto de cada respuesta. Por ejemplo: una espera de 5 segundos antes de avisar a otro sistema,
   ¿es requisito de negocio o un parche técnico?
5. **Cierra con una checklist de verificación:** una fila por regla con el test que el servicio nuevo
   debe pasar, una columna para marcar si pasó y otra para notas.
6. En las entradas y salidas de ejemplo usa datos inventados: nunca datos reales de clientes, nombres
   de empresas ni dominios sensibles. No escribas código nuevo: esta skill solo documenta.

**DETENTE.** Presenta `docs/reglas.md`. Las dudas las resuelve la persona o quien conozca el dominio,
y el estado de cada regla lo decide ella. Espera sus respuestas.

7. Actualiza el documento con sus decisiones. Cuando lo apruebe, commitéalo en `main` antes de
   especificar el servicio nuevo o crear worktrees:
   ```bash
   git add docs/reglas.md
   git commit -m "Reglas de negocio: <servicio legacy>"
   ```

## Qué produce
- `docs/reglas.md` con las reglas numeradas (descripción, ubicación, entrada y salida, casos
  especiales, estado y notas), las dudas D-xxx resueltas o pendientes y la checklist regla → test,
  commiteado en `main`.

## Siguiente
- `/desde-cero` para especificar el servicio nuevo con `docs/reglas.md` como base: respeta las reglas
  marcadas «Mantener» y documenta cómo cambian las marcadas «Cambiar».
- `/reparto` si la implementación del servicio nuevo es grande.

Si necesitas un ejemplo completo, lee [references/ejemplo.md](references/ejemplo.md).
