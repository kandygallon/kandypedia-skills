# Referencia de Comandos

Guía rápida de los 11 skills de kandypedia para trabajo con agentes de IA.

---

## 🧭 ¿Cuál skill uso?

| Situación | Skill recomendada |
|-----------|-------------------|
| **Modificar código existente** | `/analizar-existente` |
| **Proyecto completamente nuevo** | `/desde-cero` |
| **Migrar servicio legacy a nueva implementación** | `/extraer-reglas` → `/desde-cero` |
| **Dividir trabajo grande en tareas paralelas** | `/reparto` |
| **Definir contratos entre tareas paralelas** | `/contrato` |
| **Configurar espacios de trabajo para múltiples agentes** | `/worktrees` |
| **Gestionar archivos tocados por varios agentes** | `/archivos-compartidos` |
| **Implementar con tests primero** | `/tests-primero` |
| **Revisar código con ojos frescos** | `/revision-independiente` |
| **Verificar antes de mergear una tarea** | `/antes-del-merge` |
| **Integrar todas las branches al final** | `/integrar` |

---

## 📋 Planificación (antes del código)

### `/analizar-existente`

**Para qué sirve:**
Mapear y entender código o flujos existentes antes de modificarlos. Identifica puntos de entrada, dependencias, riesgos y reglas de negocio implícitas.

**Cuándo usarla:**
- ✅ Vas a modificar, arreglar o extender código que ya existe
- ✅ Necesitas agregar una funcionalidad a un sistema en producción
- ✅ Te piden cambiar algo que ya funciona pero no conoces bien

**Cuándo NO usarla:**
- ❌ Estás empezando un proyecto desde cero (usa `/desde-cero`)
- ❌ Estás migrando un servicio legacy (usa `/extraer-reglas`)

**Invocación:**
```
/analizar-existente

Necesito agregar la funcionalidad de filtros avanzados al listado 
de productos. El código actual está en src/components/ProductList.tsx
```

**Qué produce:**
1. Diagnóstico escrito con estado actual, flujo de datos, dependencias y riesgos
2. Lista de dudas específicas para el usuario o equipo
3. Próximos pasos ordenados

**Siguiente comando típico:**
- Si el trabajo es grande → `/reparto`
- Si es pequeño → implementar directamente

---

### `/desde-cero`

**Para qué sirve:**
Especificar proyectos nuevos haciendo las preguntas correctas antes de escribir código. Produce una especificación mínima (MVP).

**Cuándo usarla:**
- ✅ Vas a empezar un proyecto completamente nuevo
- ✅ Es un MVP, prototipo o primera versión
- ✅ No hay código base existente

**Cuándo NO usarla:**
- ❌ Hay código existente que modificar (usa `/analizar-existente`)
- ❌ Estás migrando desde legacy (usa `/extraer-reglas` primero)

**Invocación:**
```
/desde-cero

Quiero crear una herramienta CLI para gestionar worktrees de git
y facilitar el trabajo en paralelo con múltiples agentes.
```

**Qué produce:**
1. Especificación con: problema, usuario, MVP, restricciones, fuera de alcance, definición de hecho
2. Opciones de arquitectura con recomendación justificada

**Siguiente comando típico:**
- Si el MVP tiene varias partes independientes → `/reparto`
- Si es pequeño (una sola pieza) → `/tests-primero` para implementar

---

### `/extraer-reglas`

**Para qué sirve:**
Extraer reglas de negocio de un servicio legacy durante migraciones. El código legacy es la fuente de verdad para las reglas, no para el diseño.

**Cuándo usarla:**
- ✅ Estás migrando un servicio legacy a una nueva arquitectura
- ✅ Necesitas entender QUÉ hace el sistema, no CÓMO está implementado
- ✅ El legacy define las reglas pero el diseño se reescribirá

**Cuándo NO usarla:**
- ❌ Solo necesitas entender código para modificarlo (usa `/analizar-existente`)
- ❌ Es un proyecto nuevo sin legacy (usa `/desde-cero`)
- ❌ El legacy se mantiene tal cual (no es migración)

**Invocación:**
```
/extraer-reglas

Voy a migrar el servicio de gestión de pedidos del sistema legacy.
El endpoint principal es POST /api/orders/create y necesito extraer
todas las reglas de validación, cálculos y efectos secundarios.
```

**Qué produce:**
1. Documento `reglas.md` con reglas numeradas (R-001, R-002...)
2. Cada regla con: descripción, ubicación en legacy, ejemplos, edge cases, estado (mantener/cambiar/eliminar)
3. Dudas para expertos de dominio (D-001, D-002...)
4. Checklist de verificación mapeando reglas a test cases

**Siguiente comando típico:**
- Revisar dudas con experto de dominio
- Usar `/desde-cero` con `@reglas.md` como base

---

## 🔀 Trabajo Paralelo

### `/reparto`

**Para qué sirve:**
Dividir un proyecto en tareas independientes que pueden ejecutarse en paralelo por distintos agentes.

**Cuándo usarla:**
- ✅ Ya tienes especificación o diagnóstico
- ✅ El trabajo es grande para un solo agente
- ✅ Hay partes que pueden hacerse en paralelo sin pisarse

**Cuándo NO usarla:**
- ❌ El proyecto es muy pequeño (una sola funcionalidad)
- ❌ Todas las partes dependen unas de otras
- ❌ No tienes especificación todavía (haz eso primero)

**Invocación:**
```
/reparto

Tengo la especificación de una app de inventario con: autenticación,
CRUD de productos, reportes y notificaciones. Necesito dividirlo
en tareas para trabajar con 4 agentes en paralelo.
```

**Qué produce:**
1. Plan de reparto con tareas numeradas
2. Archivos asignados a cada tarea (sin overlap)
3. Dependencias explícitas entre tareas
4. Rondas de ejecución (qué va en paralelo, qué espera)
5. Entregables y verificación por tarea

**Siguiente comando típico:**
- `/contrato` para definir contratos entre tareas
- `/worktrees` para crear espacios de trabajo

---

### `/contrato`

**Para qué sirve:**
Definir el contrato exacto (entradas, salidas, errores) de cada punto de unión antes de crear worktrees. Documenta en `docs/contrato.md` y lo commitea a main para que todos los agentes partan de la misma versión.

**Cuándo usarla:**
- ✅ Ya dividiste el trabajo con `/reparto`
- ✅ Hay múltiples agentes que necesitan comunicarse entre sí
- ✅ Necesitas especificar contratos exactos (APIs, funciones, tipos)

**Cuándo NO usarla:**
- ❌ No dividiste el trabajo todavía (usa `/reparto` primero)
- ❌ Es una tarea individual sin puntos de unión

**Invocación:**
```
/contrato

Tenemos 3 agentes trabajando en paralelo: backend API, frontend UI y reportes.
Necesito definir los contratos de los endpoints y tipos compartidos antes 
de que empiecen a codear.
```

**Qué produce:**
1. Archivo `docs/contrato.md` con:
   - Objetivo en 2 líneas
   - División de trabajo por agente/worktree
   - Cada punto de unión con entrada, salida, errores y ejemplos
   - Decisiones tomadas y fuera de alcance
   - Preguntas abiertas
2. DETENTE para aprobación del usuario
3. Commit a `main` del contrato aprobado

**Siguiente comando típico:**
- Después de aprobar: `/worktrees` para crear espacios de trabajo
- Dentro de cada worktree: `/tests-primero` usando el contrato como referencia

---

### `/worktrees`

**Para qué sirve:**
Configurar git worktrees (uno por tarea) para que varios agentes trabajen en paralelo sin pisarse.

**Cuándo usarla:**
- ✅ Ya dividiste el trabajo en tareas (con `/reparto`)
- ✅ Vas a lanzar varios agentes al mismo tiempo
- ✅ Cada agente necesita su propio directorio de trabajo

**Cuándo NO usarla:**
- ❌ Solo hay una tarea (un agente trabaja en el repo principal)
- ❌ Las tareas se harán en secuencia, no en paralelo

**Invocación:**
```
/worktrees

Tengo 4 tareas del plan de reparto. Necesito crear un worktree
y branch por cada una: task-1-auth, task-2-products, task-3-ui, task-4-reports
```

**Qué produce:**
1. Comandos para crear worktrees con branches
2. Estructura de directorios resultante
3. Instrucciones de asignación por agente
4. Comandos de limpieza para después

**Siguiente comando típico:**
- Asignar cada worktree a un agente
- Usar `/archivos-compartidos` si aplica
- Dentro de cada worktree: `/tests-primero` para implementar
- Cada agente usa `/revision-independiente` y `/antes-del-merge` al terminar

---

### `/archivos-compartidos`

**Para qué sirve:**
Gestionar archivos que varios agentes necesitan modificar, evitando conflictos y sobrescrituras.

**Cuándo usarla:**
- ✅ Dos o más tareas necesitan tocar el mismo archivo
- ✅ Hay configuración global que varias partes usan
- ✅ El plan de reparto identificó archivos compartidos

**Cuándo NO usarla:**
- ❌ Cada tarea tiene archivos completamente independientes (ideal)

**Invocación:**
```
/archivos-compartidos

Las tareas 2, 3 y 4 necesitan agregar rutas a src/routes.ts
y todas necesitan agregar dependencias a package.json.
¿Cómo lo coordinamos?
```

**Qué produce:**
1. Lista de archivos compartidos con owner asignado
2. Protocolo: qué hace el owner, qué hacen los demás
3. Plantilla de archivo `REQUESTS.md` para solicitar cambios
4. Estrategias por tipo de archivo (rutas, configs, estilos, types)

**Siguiente comando típico:**
- Los no-owners crean `REQUESTS.md` en sus branches
- El owner aplica cambios antes de mergear
- Dentro de cada worktree: `/tests-primero` para implementar
- Al terminar: `/revision-independiente` para revisar

---

## ✅ Calidad

### `/tests-primero`

**Para qué sirve:**
Implementar código con tests primero: define ejemplos de entrada/salida, escribe tests que fallan, y luego implementa lo mínimo para que pasen.

**Cuándo usarla:**
- ✅ Ya tienes especificación del requisito (de `/desde-cero` o `/extraer-reglas`)
- ✅ Estás en tu worktree listo para implementar
- ✅ Quieres asegurar que el código hace lo que debe

**Cuándo NO usarla:**
- ❌ Todavía estás en fase de planificación
- ❌ Es código exploratorio/prototipo

**Invocación:**
```
/tests-primero

Voy a implementar la función receiveProduct según el contrato.
Primero quiero definir los tests.
```

**Qué produce:**
1. Explicación del requisito en 3-5 líneas
2. Al menos 3 ejemplos (entrada/salida/errores, casos frontera)
3. Suposiciones y dudas
4. DETENTE para aprobación
5. Tests escritos (sin implementación)
6. Tests ejecutados y FALLANDO (confirma que testean algo)
7. DETENTE para aprobación de tests
8. Implementación mínima
9. Tests ejecutados y PASANDO
10. Resumen de cambios

**Siguiente comando típico:**
- Continuar implementando más features con el mismo proceso
- Al terminar la tarea: `/revision-independiente` para revisar
- Luego `/antes-del-merge`

---

### `/revision-independiente`

**Para qué sirve:**
Revisar el código con ojos frescos, como si fueras el único revisor. Se ejecuta en un chat NUEVO con modelo diferente para verdadera independencia.

**Cuándo usarla:**
- ✅ Terminaste de implementar tu tarea
- ✅ Los tests pasan
- ✅ Quieres una revisión objetiva antes de mergear

**Cuándo NO usarla:**
- ❌ Todavía estás implementando
- ❌ Los tests no pasan

**Invocación:**
```
En un NUEVO chat con modelo diferente:

/revision-independiente

Revisa estos cambios:
[pega diff]

Requisito: @docs/contrato.md
Reglas: @docs/reglas.md
```

**Qué produce:**
1. Revisión en 5 áreas:
   - Correctitud (¿hace lo que pide?)
   - Manejo de errores y contratos
   - Seguridad
   - Tests (¿qué no está cubierto?)
   - Legibilidad (solo si afecta de verdad)
2. Lista de hallazgos con formato:
   `[Gravedad: alta/media/baja] archivo:línea: problema. Por qué importa. Sugerencia concreta.`
3. Decisión: aprobar o no aprobar para merge

**Siguiente comando típico:**
- Corregir hallazgos de gravedad alta
- Opcionalmente corregir media/baja
- Usar `/antes-del-merge` para verificar

---

### `/antes-del-merge`

**Para qué sirve:**
Checklist de calidad antes de mergear una branch. Verifica que la tarea está completa, no se rompió nada y se respetaron las fronteras asignadas.

**Cuándo usarla:**
- ✅ Terminaste tu tarea y estás listo para mergear
- ✅ Antes del último push de tu branch
- ✅ Antes de avisar "mi parte está lista"

**Cuándo NO usarla:**
- ❌ Todavía estás trabajando (no terminaste)

**Invocación:**
```
/antes-del-merge

Terminé la tarea 2 (API de productos). Antes de mergear,
verifica que todo está bien.
```

**Qué produce:**
1. Checklist con 10 verificaciones:
   - ✅ Tarea completa
   - ✅ Tests pasan
   - ✅ Linter no se queja
   - ✅ Solo modifiqué archivos asignados
   - ✅ Todo está commiteado
   - ✅ Commits con mensajes claros
   - ✅ Branch actualizada con main
   - ✅ Cambios importantes documentados
   - ✅ Código temporal limpio
   - ✅ Branch pusheada
2. Reporte de verificación

**Siguiente comando típico:**
- Notificar que la tarea pasó el checklist
- Si eres owner de archivos compartidos, revisar `REQUESTS.md` de otras branches
- Esperar la integración final (usar `/integrar`)

---

### `/integrar`

**Para qué sirve:**
Mergear todas las branches paralelas en el orden correcto, resolver conflictos y verificar que el resultado funciona end-to-end.

**Cuándo usarla:**
- ✅ Todas las tareas terminaron (cada una pasó `/antes-del-merge`)
- ✅ Tienes múltiples branches listas para mergear
- ✅ Necesitas verificar que el sistema completo funciona junto

**Cuándo NO usarla:**
- ❌ Todavía hay tareas en progreso (espera a que terminen)
- ❌ Solo hay una branch (hazlo directamente)

**Invocación:**
```
/integrar

Tengo 4 branches listas (task-1-auth, task-2-products, task-3-ui, task-4-reports).
Todas pasaron /antes-del-merge. Necesito mergearlas a main en el orden correcto.
```

**Qué produce:**
1. Orden de merge según dependencias
2. Comandos paso a paso para cada merge
3. Guía de resolución de conflictos
4. Verificaciones después de cada merge
5. Reporte de integración final
6. Comandos de limpieza de worktrees/branches

**Siguiente comando típico:**
- Deploy a staging/producción
- Code review del resultado integrado
- Retrospectiva del proceso

---

## 🔄 Flujos Típicos

### A. Nueva funcionalidad en proyecto existente

```
1. /analizar-existente
   → Entiendes el código actual, dependencias, riesgos

2. (opcional) /reparto
   → Si es grande, divide en tareas
   → Si es pequeño, salta al paso 6

3. /contrato
   → Define contratos entre tareas
   → Commitea a main

4. /worktrees
   → Crea espacios de trabajo por tarea

5. (si aplica) /archivos-compartidos
   → Coordina archivos tocados por varias tareas

6. En cada worktree: /tests-primero
   → Implementa con tests primero

7. /revision-independiente (chat nuevo, modelo diferente)
   → Revisa cada tarea objetivamente

8. /antes-del-merge (cada tarea)
   → Verifica calidad antes de mergear

9. /integrar (si hay múltiples branches)
   → Mergea todo en orden correcto
```

**Ejemplo:**
> "Agregar sistema de notificaciones push al dashboard existente"

---

### B. Proyecto desde cero

```
1. /desde-cero
   → Especifica problema, usuario, MVP, restricciones

2. (opcional) /reparto
   → Si el MVP es grande, divide en tareas
   → Si es pequeño, salta al paso 6

3. /contrato
   → Define contratos entre tareas
   → Commitea a main

4. /worktrees
   → Crea espacios de trabajo por tarea

5. (si aplica) /archivos-compartidos
   → Coordina archivos compartidos

6. En cada worktree: /tests-primero
   → Implementa con tests primero

7. /revision-independiente (chat nuevo, modelo diferente)
   → Revisa cada tarea objetivamente

8. /antes-del-merge (cada tarea)
   → Verifica calidad

9. /integrar (si hay múltiples branches)
   → Mergea todo
```

**Ejemplo:**
> "Crear una CLI para gestionar sesiones de tmux para agentes de IA"

---

### C. Migración de servicio legacy

```
1. /extraer-reglas
   → Documenta todas las reglas de negocio del legacy
   → Produce reglas.md con R-001, R-002...
   → Lista dudas para expertos (D-001, D-002...)

2. Revisar reglas con experto de dominio
   → Resuelve dudas
   → Marca cada regla: mantener/cambiar/eliminar

3. /desde-cero (con @reglas.md)
   → Especifica el nuevo servicio respetando reglas

4. (opcional) /reparto
   → Si es grande, divide la implementación

5. /contrato
   → Define contratos entre tareas
   → Commitea a main

6. /worktrees
   → Espacios de trabajo por tarea

7. (si aplica) /archivos-compartidos
   → Coordina overlaps

8. En cada worktree: /tests-primero
   → Implementa con tests derivados de reglas.md
   → Referencia IDs de regla (R-001...) en tests

9. /revision-independiente (chat nuevo, modelo diferente)
   → Revisa cada tarea contra contrato y reglas

10. /antes-del-merge (cada tarea)
    → Verifica que pasaron tests de reglas

11. /integrar
    → Mergea todo y verifica end-to-end
```

**Ejemplo:**
> "Migrar el servicio de procesamiento de pedidos del sistema monolito 
> a un microservicio independiente, manteniendo todas las reglas de negocio"

---

## 📦 Instalación en Cursor

Cursor descubre skills automáticamente desde directorios específicos. 

**Documentación oficial**: [Cursor Agent Skills](https://cursor.com/docs/skills)

### Opción 1: Instalación global (recomendado)

Instala en tu directorio personal para usar en todos tus proyectos:

```bash
git clone https://github.com/kandygallon/kandypedia-skills.git ~/.cursor/skills/kandypedia
```

Cursor descubre automáticamente skills en `~/.cursor/skills/` y subdirectorios.

**Ventajas:**
- ✅ Los skills NO se commitean al repositorio de tu empleador
- ✅ Disponibles en todos tus proyectos locales
- ✅ Actualizas una sola vez: `cd ~/.cursor/skills/kandypedia && git pull`

**Nota:** Cursor también descubre skills en `~/.agents/skills/` y `.claude/skills/` por compatibilidad.

### Opción 2: Instalación por proyecto

Si prefieres tener los skills en el proyecto:

```bash
# Dentro de tu proyecto
git clone https://github.com/kandygallon/kandypedia-skills.git .cursor/skills/kandypedia

# Opcional: agregar al .gitignore si NO quieres commitearlos
echo ".cursor/skills/" >> .gitignore
```

Cursor descubre skills en `.cursor/skills/` y `.agents/skills/` del proyecto.

---

## 💡 Uso en el chat

Invoca skills escribiendo `/` en el chat y buscando el nombre:

```
/analizar-existente

Necesito modificar el flujo de autenticación...
```

Cursor carga la skill automáticamente cuando es relevante.

---

## 🔗 Enlaces

- **Repositorio**: https://github.com/kandygallon/kandypedia-skills
- **Sitio web**: https://kandypedia.com
- **Coaching 1:1**: https://kandypedia.com/coaching

---

## 📝 Notas

- **Los skills no escriben código directamente**: son guías de proceso que el agente sigue
- **Combínalos según tu flujo**: no todas las situaciones necesitan todos los skills
- **Personalízalos**: puedes copiar y modificar los skills para tu equipo
- **Compártelos**: MIT License, libre para usar y distribuir

---

**¿Dudas o mejoras?** Abre un [issue](https://github.com/kandygallon/kandypedia-skills/issues) en el repositorio.
