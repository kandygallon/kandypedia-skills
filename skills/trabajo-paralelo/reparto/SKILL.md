---
name: Reparto
description: Usa esta skill para dividir un proyecto o especificación en tareas independientes que pueden ejecutarse en paralelo por distintos agentes.
---

# Reparto

## ¿Cuándo usar esta skill?

Úsala cuando:
- Ya tienes una especificación o diagnóstico (de `/desde-cero` o `/analizar-existente`)
- El trabajo es demasiado grande para un solo agente
- Hay partes del proyecto que pueden hacerse en paralelo sin pisarse
- Quieres distribuir el trabajo entre varios agentes de Cursor

**No la uses** cuando:
- El proyecto es muy pequeño (una sola funcionalidad simple)
- Todas las partes dependen unas de otras (no hay paralelización posible)
- Todavía no tienes especificación (haz eso primero)

## Filosofía

El trabajo en paralelo acelera proyectos grandes, pero solo si las tareas están bien divididas. Una mala división causa:
- Conflictos de git (dos agentes tocan el mismo archivo)
- Dependencias circulares (A espera B, B espera A)
- Duplicación de trabajo (dos agentes hacen lo mismo)
- Retrabajos (un agente deshace lo que hizo otro)

Esta skill te guía para dividir el trabajo en **tareas independientes**, con **fronteras claras**, **archivos asignados** y **orden de ejecución** cuando hay dependencias.

## Criterios para una buena división

Una tarea paralela debe ser:

1. **Independiente**: puede empezar sin esperar a que otra termine
2. **Completa**: produce algo funcional por sí sola (aunque sea parcial)
3. **Limitada**: tiene alcance claro, no se desborda
4. **Verificable**: puedes probar que funciona antes de integrarla

## Proceso paso a paso

### 1. Analiza la especificación o diagnóstico

Lee la especificación (de `/desde-cero`) o el diagnóstico (de `/analizar-existente`).

Identifica:
- **Funcionalidades principales**: ¿qué cosas debe hacer el proyecto?
- **Capas o módulos**: ¿hay partes independientes (frontend/backend, modelos/controladores, componentes)?
- **Dependencias obvias**: ¿qué debe existir antes que otra cosa?

### 2. Identifica fronteras naturales

Busca divisiones naturales:

**Por tipo de código:**
- Frontend vs Backend
- UI vs lógica de negocio vs datos
- Componentes vs servicios vs utilidades

**Por funcionalidad:**
- Autenticación vs Dashboard vs Reportes
- Crear vs Editar vs Eliminar
- Formulario vs Validación vs Guardado

**Por archivo o carpeta:**
- `components/` vs `pages/` vs `services/`
- `models/` vs `controllers/` vs `views/`

**Ejemplo:**
Si el proyecto es "app de tareas con usuarios", puedes dividir en:
- Tarea 1: Autenticación (login, registro)
- Tarea 2: CRUD de tareas (crear, listar, editar, eliminar)
- Tarea 3: UI de tareas (componentes visuales)

### 3. Define cada tarea con claridad

Para cada tarea, especifica:

#### a) Nombre y objetivo
- Nombre corto y descriptivo
- Objetivo: qué produce esta tarea

#### b) Archivos que posee
- Lista explícita de archivos que esta tarea puede crear o modificar
- Estos son **exclusivos** de esta tarea (ninguna otra tarea los toca)

#### c) Archivos que lee (pero no modifica)
- Lista de archivos que puede consultar, pero no cambiar
- Si necesita cambios en esos archivos, debe pedirlo (ver `/archivos-compartidos`)

#### d) Dependencias
- ¿De qué otras tareas depende?
- ¿Qué debe estar terminado antes de empezar esta?

#### e) Entregable
- ¿Qué debe funcionar cuando esta tarea esté lista?
- ¿Cómo se verifica que está completa?

### 4. Asigna un orden de ejecución

Dibuja el grafo de dependencias:

```
Tarea A (sin dependencias) --> puede empezar YA
Tarea B (sin dependencias) --> puede empezar YA
Tarea C (depende de A) --> empieza después de A
Tarea D (depende de B y C) --> empieza después de B y C
```

**Regla:**
- Tareas sin dependencias → **paralelo inmediato**
- Tareas con dependencias → **secuencial o segundo round de paralelo**

### 5. Produce el plan de reparto

Escribe el plan en markdown:

```markdown
# Plan de Reparto: [nombre del proyecto]

## Resumen
[Breve descripción de cómo se dividió el trabajo y por qué]

## Tareas en Paralelo

### Ronda 1 (sin dependencias, empiezan ya)

#### Tarea 1: [Nombre]
- **Objetivo**: [Qué produce]
- **Archivos que posee** (puede crear/modificar):
  - `path/to/file1.ext`
  - `path/to/file2.ext`
- **Archivos que lee** (solo lectura):
  - `path/to/shared-config.ext`
- **Dependencias**: Ninguna
- **Entregable**: [Qué debe funcionar]
- **Verificación**: [Cómo probar que está lista]

#### Tarea 2: [Nombre]
[...mismo formato...]

### Ronda 2 (dependen de Ronda 1, empiezan después)

#### Tarea 3: [Nombre]
- **Objetivo**: [Qué produce]
- **Archivos que posee**:
  - `path/to/file3.ext`
- **Archivos que lee**:
  - `path/to/file1.ext` (de Tarea 1)
- **Dependencias**: Tarea 1 debe estar completa
- **Entregable**: [Qué debe funcionar]
- **Verificación**: [Cómo probar que está lista]

## Archivos Compartidos (ver `/archivos-compartidos`)
[Lista de archivos que más de una tarea necesita tocar]

- `config/routes.js` → Owner: [quién decide cambios]
- `styles/global.css` → Owner: [quién decide cambios]

## Integración Final (ver `/integrar`)
[Orden en que deben mergearse las branches]

1. Tarea 1 y Tarea 2 (sin conflictos esperados)
2. Tarea 3 (puede tener conflictos con Tarea 1, resolver manual)
```

## Ejemplo completo

**Especificación de entrada:**
> Crear una app web de gestión de inventario para un almacén. Debe tener login, listado de productos, agregar/editar productos, y un reporte de stock bajo.

**Análisis:**

Fronteras naturales:
- Autenticación (independiente)
- CRUD de productos (independiente del login después de tener el modelo de usuario)
- UI de productos (depende del CRUD)
- Reporte (depende del modelo de productos)

**Plan de reparto producido:**

```markdown
# Plan de Reparto: App de Inventario

## Resumen
Dividimos el trabajo en 5 tareas: 2 en paralelo inmediato (autenticación y modelo de productos), 2 en segunda ronda (UI de productos y reporte), y 1 de integración final.

## Tareas en Paralelo

### Ronda 1 (sin dependencias)

#### Tarea 1: Autenticación
- **Objetivo**: Login y registro de usuarios funcional
- **Archivos que posee**:
  - `src/components/Login.jsx`
  - `src/components/Register.jsx`
  - `src/services/auth.js`
  - `src/models/User.js`
- **Archivos que lee**:
  - `src/config/db.js` (configuración de base de datos)
- **Dependencias**: Ninguna
- **Entregable**: Un usuario puede registrarse, hacer login y ver su sesión activa
- **Verificación**: `npm test -- auth.test.js` debe pasar

#### Tarea 2: Modelo y API de Productos
- **Objetivo**: CRUD backend de productos (sin UI)
- **Archivos que posee**:
  - `src/models/Product.js`
  - `src/controllers/products.js`
  - `src/routes/products.js`
  - `src/migrations/create-products.js`
- **Archivos que lee**:
  - `src/config/db.js`
- **Dependencias**: Ninguna
- **Entregable**: API REST funcional para crear, listar, editar y eliminar productos
- **Verificación**: `curl` a los endpoints debe devolver datos correctos

### Ronda 2 (dependen de Ronda 1)

#### Tarea 3: UI de Productos
- **Objetivo**: Interfaz visual para gestionar productos
- **Archivos que posee**:
  - `src/components/ProductList.jsx`
  - `src/components/ProductForm.jsx`
  - `src/pages/Products.jsx`
  - `src/styles/products.css`
- **Archivos que lee**:
  - `src/services/auth.js` (de Tarea 1, para proteger rutas)
  - `src/models/Product.js` (de Tarea 2, para tipos)
- **Dependencias**: Tarea 1 (para autenticación) y Tarea 2 (para API)
- **Entregable**: Un usuario logueado puede ver, agregar, editar y eliminar productos desde la UI
- **Verificación**: Navegador en `http://localhost:3000/products` debe mostrar la lista

#### Tarea 4: Reporte de Stock Bajo
- **Objetivo**: Vista de productos con stock menor a un umbral
- **Archivos que posee**:
  - `src/components/LowStockReport.jsx`
  - `src/pages/Reports.jsx`
  - `src/controllers/reports.js`
  - `src/routes/reports.js`
- **Archivos que lee**:
  - `src/models/Product.js` (de Tarea 2)
  - `src/services/auth.js` (de Tarea 1)
- **Dependencias**: Tarea 2 (para datos de productos)
- **Entregable**: Un reporte que muestra productos con stock < 10 unidades
- **Verificación**: `GET /api/reports/low-stock` devuelve lista correcta

## Archivos Compartidos
- `src/config/routes.js` → Owner: Tarea 1 (crea el archivo), otras tareas piden agregar sus rutas
- `src/App.jsx` → Owner: Tarea 3 (agrega las páginas principales)

## Integración Final
1. Merge Tarea 1 y Tarea 2 (sin conflictos)
2. Merge Tarea 3 (requiere ambas anteriores)
3. Merge Tarea 4 (puede hacerse en paralelo con Tarea 3)
4. Prueba end-to-end: usuario se loguea, agrega productos, ve el reporte
```

## Entregables

Al terminar de usar esta skill, debes tener:

1. ✅ Un **plan de reparto** con tareas numeradas
2. ✅ **Archivos asignados** a cada tarea (claros y sin overlap)
3. ✅ **Dependencias** explícitas entre tareas
4. ✅ **Rondas de ejecución** (qué va en paralelo, qué espera)
5. ✅ **Entregables y verificación** para cada tarea

## Transición a las siguientes skills

Después del reparto:
1. Usa `/worktrees` para crear un worktree y branch por cada tarea
2. Asigna cada tarea a un agente (o lánzalos con `Task` de Cursor)
3. Usa `/archivos-compartidos` para gestionar archivos que más de una tarea necesita
4. Cada agente usa `/antes-del-merge` antes de terminar
5. Usa `/integrar` para mergear todo al final

## Notas importantes

- **No fuerces la paralelización**: si el proyecto es chico o muy acoplado, no lo dividas
- **Los archivos compartidos son el mayor riesgo**: minimízalos, y asigna un owner claro
- **Una tarea debe poder verificarse sola**: si no puedes probar que funciona sin mergear todo, la división es mala
- **Prefiere pocas tareas grandes que muchas tareas chicas**: el overhead de coordinar muchos agentes puede ser peor que hacer menos tareas
