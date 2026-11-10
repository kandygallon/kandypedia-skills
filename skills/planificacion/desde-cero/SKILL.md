---
name: desde-cero
description: Usa esta skill cuando comiences un proyecto nuevo desde cero. Hace las preguntas correctas antes de escribir código y produce una especificación mínima.
---

# Desde Cero

## ¿Cuándo usar esta skill?

Úsala cuando:
- Vas a empezar un proyecto completamente nuevo
- El usuario dice "quiero crear", "necesito construir", "vamos a hacer"
- No hay código base existente
- Es un MVP, prototipo o primera versión

**No la uses** cuando haya código existente que modificar (usa `/analizar-existente` en ese caso).

## Filosofía

El error más común al empezar un proyecto es escribir código demasiado pronto. Antes de la primera línea de código, necesitas entender:
- **Qué problema resuelve** (el "por qué")
- **Para quién lo resuelve** (el "para quién")
- **Cuál es la versión más pequeña que funciona** (el "qué")
- **Qué restricciones existen** (el "cómo")
- **Cómo sabrás que está terminado** (la "definición de hecho")

Esta skill te guía para hacer esas preguntas en el orden correcto, **una por una**, y produce una especificación corta antes de escribir código.

## Proceso paso a paso

### 1. El problema (por qué)

Pregunta:
> "¿Qué problema estás tratando de resolver o qué necesidad tiene el usuario final?"

**Espera la respuesta.** No sigas hasta entenderla.

Si la respuesta es vaga ("quiero una app de tareas"), profundiza:
> "¿Qué hace que las apps de tareas actuales no funcionen para ti? ¿Qué te frustra de ellas?"

**Recomendación que puedes ofrecer:**
- Un buen problema es específico: "Los desarrolladores pierden cambios cuando trabajan en paralelo sin coordinación"
- Un mal problema es genérico: "Quiero una app"

### 2. El usuario (para quién)

Pregunta:
> "¿Quién va a usar esto? ¿Qué sabe y qué no sabe esa persona?"

**Espera la respuesta.**

Si es ambiguo ("desarrolladores"), aclara:
> "¿Desarrolladores junior que están aprendiendo, o seniors que ya usan git y agentes todos los días?"

**Recomendación:**
- Un usuario bien definido: "Desarrolladores que ya usan Cursor pero trabajan solos, no en paralelo"
- Un usuario mal definido: "Cualquiera"

### 3. La versión más pequeña que entrega valor (qué)

Pregunta:
> "¿Cuál es la versión más pequeña de esto que ya resuelve el problema? Si tuvieras que lanzarlo mañana, ¿qué debe incluir y qué puede esperar?"

**Espera la respuesta.**

Si responde con una lista larga de funcionalidades, recorta:
> "De esas 5 cosas, ¿cuál es la única que **tiene que estar** para que valga la pena usarlo? Las otras pueden venir después."

**Recomendación:**
- Un buen MVP: "Un comando que crea un worktree por tarea y muestra el estado de cada uno"
- Un mal MVP: "Autenticación, dashboards, notificaciones, integraciones, reportes..."

### 4. Restricciones (cómo)

Pregunta:
> "¿Hay restricciones técnicas, de tiempo o de recursos? Por ejemplo: debe funcionar en un stack específico, no puedes usar servicios de pago, debe ser rápido de hacer, etc."

**Espera la respuesta.**

Ejemplos de restricciones comunes:
- "Debe ser solo frontend, sin backend"
- "Debe funcionar offline"
- "Debe usar React (ya lo conocemos)"
- "Tenemos 2 semanas"

Si no hay restricciones, confirma:
> "¿Puedo elegir libremente las tecnologías?"

**Recomendación:**
- Buena restricción: "Debe funcionar con cualquier repo git, sin dependencias externas"
- Mala restricción: "Debe ser perfecto"

### 5. Fuera de alcance (qué NO es)

Pregunta:
> "¿Qué cosas están explícitamente fuera del alcance de esta primera versión? Es útil saber qué NO vamos a hacer."

**Espera la respuesta.**

Si no están seguros, sugiere ejemplos:
> "¿Fuera de alcance significa cosas como: autenticación de usuarios, deploys automáticos, versionado complejo?"

**Recomendación:**
- Listar lo que NO harás evita el scope creep y aclara expectativas

### 6. Definición de hecho (cómo sabemos que terminamos)

Pregunta:
> "¿Cómo sabemos que está terminado? ¿Qué debe poder hacer un usuario para que digas 'sí, esto funciona'?"

**Espera la respuesta.**

Si es vago ("que funcione"), pide un caso concreto:
> "Dame un ejemplo: un usuario abre la app, hace X, ve Y, y eso le resuelve su problema. ¿Cómo es ese flujo?"

**Recomendación:**
- Buena definición: "El usuario corre `task-split plan.md` y obtiene 3 worktrees con branches listos, puede trabajar en cada uno, y al final hace merge sin conflictos"
- Mala definición: "Que esté bien hecho"

## Especificación mínima

Después de las preguntas, produce un documento corto:

```markdown
# Especificación: [nombre del proyecto]

## Problema
[Qué problema resuelve, en 2-3 oraciones]

## Usuario
[Quién lo usará, qué sabe, qué necesita]

## Versión mínima (MVP)
[La lista más corta posible de lo que debe hacer]

- Feature 1
- Feature 2
- Feature 3

## Restricciones
[Restricciones técnicas o de recursos]

- Stack: [tecnologías que debes usar]
- Tiempo: [si hay límite de tiempo]
- Dependencias: [qué puedes o no puedes usar]

## Fuera de alcance (para después)
[Lista de cosas que NO harás en esta versión]

- Cosa 1 que no harás
- Cosa 2 que no harás

## Definición de hecho
[Cómo se ve el éxito, caso de uso concreto]

"Un usuario puede [acción] y obtiene [resultado], lo cual resuelve [problema]."

## Arquitectura sugerida
[Opción 1]: [descripción breve + pros/contras]
[Opción 2]: [descripción breve + pros/contras]

Recomendación: [cuál y por qué]
```

## Ejemplo de uso completo

**Usuario dice:**
> "Quiero crear una herramienta para trabajar en paralelo con agentes."

**Agente (usando esta skill):**

**Pregunta 1:**
> "¿Qué problema estás tratando de resolver? ¿Qué pasa hoy cuando trabajas con agentes que te gustaría que fuera diferente?"

**Usuario responde:**
> "Cuando lanzo varios agentes en el mismo repo, se pisan entre ellos. Uno modifica un archivo y otro lo sobreescribe."

**Pregunta 2:**
> "¿Quién va a usar esto? ¿Desarrolladores que ya conocen git y worktrees, o gente que nunca los usó?"

**Usuario responde:**
> "Desarrolladores que usan Cursor, saben git básico pero nunca usaron worktrees."

**Pregunta 3:**
> "¿Cuál es la versión más pequeña que resuelve el problema? Si tuvieras que lanzarlo mañana, ¿qué debe hacer?"

**Usuario responde:**
> "Que me ayude a crear un worktree por tarea, me diga dónde está cada uno, y al final los mergee sin conflictos."

**Pregunta 4:**
> "¿Hay restricciones? ¿Debe funcionar solo en Linux, o con ciertas tecnologías?"

**Usuario responde:**
> "Debe ser un script simple, sin dependencias raras. Bash o Node, lo que sea más fácil."

**Pregunta 5:**
> "¿Qué cosas están fuera de alcance para esta primera versión?"

**Usuario responde:**
> "No necesito UI gráfica, ni sincronización en tiempo real, ni nada en la nube. Solo local."

**Pregunta 6:**
> "¿Cómo sabemos que está terminado? Dame un ejemplo de uso."

**Usuario responde:**
> "Yo corro `parallel-work split tasks.txt`, me crea 3 worktrees con sus branches. Trabajo en cada uno con un agente diferente. Cuando termino, corro `parallel-work merge` y queda todo integrado en mi branch principal."

**Agente produce especificación:**

```markdown
# Especificación: parallel-work

## Problema
Cuando varios agentes trabajan en el mismo repositorio git al mismo tiempo, se pisan entre ellos porque modifican los mismos archivos. Esto causa conflictos y pérdida de trabajo.

## Usuario
Desarrolladores que usan Cursor y conocen git básico (commit, push, merge) pero no han usado worktrees. Trabajan en proyectos medianos y quieren paralelizar tareas con agentes.

## Versión mínima (MVP)
- Comando `split`: lee un archivo de tareas y crea un worktree + branch por cada una
- Comando `status`: muestra el estado de cada worktree (qué archivos cambió)
- Comando `merge`: integra todos los worktrees en la branch principal, resolviendo conflictos obvios
- Comando `clean`: elimina los worktrees y branches temporales

## Restricciones
- Debe ser un script simple (Bash o Node.js)
- Sin dependencias externas complejas
- Solo funcionalidad local (no nube, no servidores)

## Fuera de alcance
- UI gráfica
- Sincronización en tiempo real
- Hosting o servicios remotos
- Integración con plataformas de CI/CD

## Definición de hecho
Un usuario corre `parallel-work split tasks.txt` con una lista de 3 tareas. El script crea 3 worktrees en directorios separados con branches independientes. El usuario trabaja en cada worktree con un agente distinto. Cuando termina, corre `parallel-work merge` y todos los cambios se integran en su branch principal sin conflictos manuales, siempre que las tareas no hayan tocado las mismas líneas de código.

## Arquitectura sugerida
**Opción 1: Script Bash**
- Pros: cero dependencias, funciona en cualquier Unix
- Contras: difícil de mantener, sin tests fáciles

**Opción 2: Script Node.js**
- Pros: más fácil de leer, testeable, multiplataforma
- Contras: requiere Node instalado

Recomendación: **Node.js** porque el usuario ya tiene Cursor (que usa Node), es más mantenible y podemos agregar features después más fácil.
```

## Entregables

Al terminar de usar esta skill, debes tener:

1. ✅ Una **especificación escrita** con problema, usuario, MVP, restricciones, fuera de alcance y definición de hecho
2. ✅ **Opciones de arquitectura** con recomendación justificada
3. ✅ Confirmación del usuario de que la especificación es correcta

**No escribas código todavía.** El objetivo es planear primero.

## Transición a las siguientes skills

Después de tener la especificación:
- Si el MVP tiene varias partes independientes → usa `/reparto` para dividir el trabajo
- Si es pequeño (una sola pieza) → implementa directamente

## Notas importantes

- **Pregunta una cosa a la vez**: no abrumes con 6 preguntas juntas
- **Escucha las respuestas**: ajusta las siguientes preguntas según lo que aprendas
- **Recomienda cuando sea útil**: si el usuario está confundido, sugiere opciones concretas
- **Mantén el MVP pequeño**: siempre empuja hacia la versión más simple que funciona
- **Documenta TODO**: la especificación es el contrato, si no está escrito, no existe
