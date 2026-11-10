---
name: analizar-existente
description: Usa esta skill cuando necesites entender un código o flujo existente antes de modificarlo. Mapea el sistema actual, identifica riesgos y reglas de negocio implícitas.
---

# Analizar Existente

## ¿Cuándo usar esta skill?

Úsala cuando:
- Vas a modificar un código que ya existe
- Necesitas agregar una funcionalidad a un sistema en producción
- Te piden "arreglar", "mejorar" o "cambiar" algo que ya funciona

**No la uses** cuando estés empezando un proyecto desde cero (usa `/desde-cero`).

## Filosofía

Nunca modifiques código sin entenderlo primero. El código existente tiene contexto, restricciones y razones que no siempre están documentadas.

## Proceso paso a paso

### 1. Mapeo inicial (sin preguntar al usuario)

Antes de hacer cualquier pregunta, investiga:

- **Puntos de entrada**: ¿Qué archivo o función inicia el flujo?
- **Flujo de datos**: ¿De dónde vienen, cómo se transforman, a dónde van?
- **Dependencias**: ¿Qué usa este código? ¿Qué depende de él?
- **Estado actual**: ¿Cuándo se modificó? ¿Hay tests? ¿Hay TODOs?
- **Áreas de riesgo**: ¿Qué partes parecen frágiles?

Usa herramientas: `grep`, `find`, `git log`, `git blame`. No adivines.

### 2. Preguntas al usuario (después de investigar)

Ahora que entiendes el código, pregunta lo que no puedes saber leyendo:

**Contexto de negocio:**
- ¿Qué problema resuelve este código?
- ¿Quién lo usa?
- ¿Qué restricciones existen?

**Restricciones técnicas:**
- ¿Qué NO debe romperse?
- ¿Hay partes legacy en uso?
- ¿Cómo se prueba y despliega?

Haz preguntas **una por una**, espera respuestas, ajusta las siguientes.

### 3. Diagnóstico escrito

Produce documento con estructura de [plantilla-diagnostico.md](plantilla-diagnostico.md):

- Estado actual
- Flujo de datos
- Dependencias
- Áreas de riesgo
- Reglas de negocio implícitas
- Próximos pasos sugeridos

### 4. Dudas abiertas

Lista preguntas específicas que no pudiste resolver:

```markdown
## Dudas abiertas
1. ¿[Pregunta específica con contexto]?
2. ¿[Otra pregunta accionable]?
```

Ver [ejemplo completo de uso](ejemplo-uso.md) con caso real documentado.

## Entregables

Al terminar:

1. ✅ Diagnóstico escrito (estado, flujo, dependencias, riesgos)
2. ✅ Lista de dudas específicas
3. ✅ Próximos pasos claros y ordenados

**No escribas código todavía.** El objetivo es entender primero.

## Transición

Después del diagnóstico:
- Si el trabajo es grande: `/reparto` para dividir en tareas
- Si es pequeño: implementa directamente respetando el diagnóstico

## Referencias

- [plantilla-diagnostico.md](plantilla-diagnostico.md): Estructura completa del diagnóstico
- [ejemplo-uso.md](ejemplo-uso.md): Caso real paso a paso

## Notas importantes

- **Investiga antes de preguntar**: el usuario aprecia que conozcas el código
- **Sé específico en preguntas**: evita preguntas vagas
- **No asumas**: si no estás seguro, pregunta o investiga más
- **Respeta decisiones pasadas**: el código "raro" a veces tiene razones válidas
