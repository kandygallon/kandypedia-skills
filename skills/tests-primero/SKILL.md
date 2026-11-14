---
name: tests-primero
description: Acuerda ejemplos de entrada y salida, escribe tests que fallan y luego implementa lo mínimo para que pasen. Úsala al implementar una tarea con reglas o contrato definidos: lógica de negocio, cálculos, validaciones, permisos o respuestas de una API.
---

# Tests primero

## Cuándo usarla
- Vas a implementar una tarea que tiene reglas en `docs/reglas.md` o un contrato en `docs/contrato.md`.
- La tarea tiene lógica de negocio, cálculos, validaciones, permisos o respuestas de error que hay que acertar.
- Estás en tu worktree con la especificación lista y quieres que el código haga justo lo pedido.

**No la uses** si todavía estás planificando o analizando, o si es código exploratorio o un prototipo que vas a tirar.
Tampoco para cambios sin lógica: textos, estilos, renombrar.

## Pasos
1. Lee `docs/reglas.md` y `docs/contrato.md` si existen. Son la fuente de verdad.
2. Explica el requisito con tus palabras, en 3 a 5 líneas.
3. Escribe al menos 3 ejemplos de entrada y salida esperada. Incluye casos frontera: vacíos, nulos, límites y errores.
   - Si hay `docs/reglas.md`, deriva los ejemplos de las reglas y cita sus IDs (`R-001`, `R-002`…).
   - Si hay `docs/contrato.md`, los ejemplos deben coincidir con sus entradas, salidas y errores.
4. Lista tus suposiciones y tus dudas sobre el requisito.

**DETENTE.** Pide a la persona que confirme o corrija los ejemplos y que responda las dudas.
No escribas tests ni código todavía.

5. Escribe **solo** los tests, basados en los ejemplos aprobados. Nombra cada test por el comportamiento y, si
   aplica, por la regla (`R-001`). No escribas la implementación.
6. Ejecuta los tests con el comando del proyecto y muestra que **fallan**. Ejemplo:

   ```bash
   npm test
   ```

   Si alguno pasa sin implementación, no está probando nada: corrígelo antes de seguir.

**DETENTE.** Pide a la persona que revise los tests. No implementes hasta que los apruebe.

7. Implementa lo mínimo para que pasen los tests aprobados. Nada «por si acaso».
8. Ejecuta los tests otra vez y muestra que **pasan**.
9. Resume el diff: qué archivos cambiaste y por qué, citando las reglas que cubre cada cambio.
   Di qué falta probar abriendo la app: que pasen los tests no basta.

### Reglas durante el ciclo
- **No cambies un test aprobado para que pase.** Si crees que está mal, dilo y espera.
- **No cambies los ejemplos aprobados** sin permiso de la persona.
- **Implementación mínima:** solo lo que piden los tests.
- Si aparece un caso nuevo a mitad de camino, vuelve al paso 3: ejemplo, aprobación y luego test.

## Qué produce
- Ejemplos de entrada y salida aprobados por la persona, con IDs de regla si existen.
- Tests que fallaron antes de implementar y pasan después.
- La implementación mínima y un resumen del diff.

## Siguiente
- Repite el ciclo con la siguiente parte de tu tarea.
- `/revision-independiente` cuando termines la tarea, en un chat nuevo.
- `/antes-del-merge` para la comprobación final antes de integrar.

Si necesitas un ejemplo completo, lee [references/ejemplo.md](references/ejemplo.md).
