---
name: analizar-existente
description: Mapea un código o flujo que ya existe antes de tocarlo: puntos de entrada, flujo de datos, dependencias, riesgos y reglas implícitas. Investiga primero, pregunta después y deja el diagnóstico en docs/diagnostico.md. Úsala antes de cambiar, arreglar o ampliar algo que ya funciona.
disable-model-invocation: true
---

# Analizar lo existente

## Cuándo usarla
- Vas a modificar código que ya existe o añadir algo a un sistema en producción.
- Te piden «arreglar», «mejorar» o «cambiar» algo que ya funciona.
- No conoces el código, o lleva tiempo sin tocarse.

**No la uses** si empiezas un proyecto nuevo: usa `/desde-cero`. Si vas a migrar un sistema y
reescribirlo, usa `/extraer-reglas`.

## Pasos
1. **Investiga antes de preguntar.** Lee el código y sigue el flujo. No adivines.
   - **Puntos de entrada:** qué archivo o función arranca el flujo; cómo se invoca (HTTP, evento,
     comando, interfaz); qué rutas, controladores o handlers intervienen.
   - **Flujo de datos:** de dónde vienen (base de datos, API, archivo), qué transformaciones sufren,
     adónde van.
   - **Dependencias:** qué módulos o servicios usa este código, cuáles dependen de él, qué librerías
     de terceros son críticas.
   - **Estado:** cuándo se modificó por última vez, comentarios o TODO relevantes, si hay tests.
   - **Riesgos:** partes frágiles o complejas, condicionales enredadas, manejo de errores ausente.
   ```bash
   grep -rn "<término>" <carpeta>
   git log --oneline -- <ruta>
   git blame <archivo>
   ```
2. **Pregunta lo que el código no dice.** De una en una: espera cada respuesta y ajusta la siguiente.
   Que cada pregunta sea concreta: «¿La tabla Y tiene índice en Z?», no «¿Qué hace X?».
   - **Negocio:** qué problema resuelve hoy; quién lo usa (personas, otros sistemas, procesos
     internos); qué restricciones hay (regulación, contratos, políticas).
   - **Técnica:** qué **no debe romperse** bajo ninguna circunstancia; qué partes son legacy pero
     siguen en uso; si hay deploys o migraciones de datos de por medio.
   - **Pruebas y deploy:** cómo se prueba hoy (manual, tests, staging); cómo se despliega; si hay
     rollback rápido.
   - **Historia:** por qué se hizo así; si hubo intentos de cambiarlo que fallaron. El código raro a
     veces tiene razones válidas: respétalas hasta saberlas.
3. **Escribe `docs/diagnostico.md`**, corto, con estas secciones:
   - **Estado actual:** qué hace hoy, puntos de entrada, tecnologías.
   - **Flujo de datos:** entrada → procesamiento → salida, con el formato en cada paso.
   - **Dependencias:** de qué depende y qué depende de él.
   - **Áreas de riesgo:** lógica frágil, falta de tests, dependencias inestables, legacy intocable.
   - **Reglas de negocio implícitas:** restricciones y validaciones no documentadas que encontraste.
   - **Próximos pasos sugeridos:** qué cambia, qué queda igual y en qué orden.
   - **Dudas abiertas**, al final: específicas y accionables. Por ejemplo: «¿El endpoint
     `/legacy/import` sigue en uso?», «¿Algún cliente externo consume este JSON directamente?».

**DETENTE.** Presenta el diagnóstico y las dudas, y espera las respuestas antes de proponer cambios.
No escribas código todavía.

4. Incorpora las respuestas al diagnóstico. Cuando la persona lo apruebe, commitéalo en `main` antes
   de repartir trabajo o crear worktrees:
   ```bash
   git add docs/diagnostico.md
   git commit -m "Diagnóstico: <flujo o módulo>"
   ```

## Qué produce
- `docs/diagnostico.md` con estado actual, flujo de datos, dependencias, riesgos, reglas implícitas,
  próximos pasos ordenados y dudas abiertas, aprobado y commiteado en `main`.

## Siguiente
- `/reparto` si el trabajo es grande y se puede dividir.
- Si es pequeño, impleméntalo directamente, respetando el diagnóstico.

Si necesitas un ejemplo completo, lee [references/ejemplo.md](references/ejemplo.md).
