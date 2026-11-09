---
name: Worktrees
description: Usa esta skill para configurar worktrees de git, uno por tarea paralela, para que varios agentes trabajen sin pisarse.
---

# Worktrees

## ¿Cuándo usar esta skill?

Úsala cuando:
- Ya dividiste el trabajo en tareas paralelas (con `/reparto`)
- Vas a lanzar varios agentes trabajando al mismo tiempo
- Necesitas que cada agente tenga su propio directorio de trabajo

**No la uses** cuando:
- Solo hay una tarea (un solo agente puede trabajar en el repo principal)
- Vas a hacer las tareas una por una en secuencia

## Filosofía

Un **worktree** es un directorio adicional asociado al mismo repositorio git, pero con su propia branch. Te permite tener varias branches "checked out" al mismo tiempo en directorios separados.

**Sin worktrees (problema):**
```
repo/
└── main branch
    # Si cambias de branch, pierdes los cambios no commiteados
    # Solo un agente a la vez puede trabajar
```

**Con worktrees (solución):**
```
repo/              # worktree principal (main)
repo-task-1/       # worktree de tarea 1 (branch task-1)
repo-task-2/       # worktree de tarea 2 (branch task-2)
repo-task-3/       # worktree de tarea 3 (branch task-3)
# Cada agente trabaja en su directorio sin pisar a los otros
```

## Ventajas de los worktrees

✅ **Aislamiento**: cada agente tiene su propio espacio  
✅ **Paralelismo real**: varios agentes corren al mismo tiempo  
✅ **Sin pérdida de trabajo**: cambiar de branch no afecta otros worktrees  
✅ **Mismo repo**: todos comparten el `.git`, no son clones separados  

## Proceso paso a paso

### 1. Preparación

Asegúrate de estar en una branch limpia (sin cambios sin commitear):

```bash
git status
```

Si hay cambios, commitealos o guárdalos:

```bash
git add .
git commit -m "wip: guardar antes de crear worktrees"
```

### 2. Crear un worktree por tarea

Para cada tarea del plan de reparto, crea un worktree:

```bash
git worktree add -b <nombre-branch> <directorio> <base-branch>
```

**Parámetros:**
- `<nombre-branch>`: nombre de la nueva branch (ej: `task-1-auth`)
- `<directorio>`: ruta donde se creará el worktree (ej: `../repo-task-1`)
- `<base-branch>`: branch desde donde parte (ej: `main`)

**Ejemplo:**

```bash
# Tarea 1: Autenticación
git worktree add -b task-1-auth ../proyecto-task-1 main

# Tarea 2: API de productos
git worktree add -b task-2-products ../proyecto-task-2 main

# Tarea 3: UI
git worktree add -b task-3-ui ../proyecto-task-3 main
```

### 3. Estructura resultante

Después de crear los worktrees:

```
/home/user/
├── proyecto/              # repo principal (main)
├── proyecto-task-1/       # worktree task-1-auth
├── proyecto-task-2/       # worktree task-2-products
└── proyecto-task-3/       # worktree task-3-ui
```

Cada directorio es **el mismo repo**, pero con una branch diferente activa.

### 4. Asignar tareas a agentes

Ahora puedes:
- Abrir un agente de Cursor en `proyecto-task-1/` con la instrucción de Tarea 1
- Abrir otro agente en `proyecto-task-2/` con la instrucción de Tarea 2
- Abrir otro agente en `proyecto-task-3/` con la instrucción de Tarea 3

Cada agente trabaja en su worktree sin conflictos.

### 5. Verificar worktrees activos

Para ver todos los worktrees:

```bash
git worktree list
```

Salida esperada:

```
/home/user/proyecto              abc123 [main]
/home/user/proyecto-task-1       def456 [task-1-auth]
/home/user/proyecto-task-2       789ghi [task-2-products]
/home/user/proyecto-task-3       jkl012 [task-3-ui]
```

### 6. Trabajo en cada worktree

Cada agente en su worktree hace:

```bash
cd /ruta/al/worktree
# ... trabaja, edita archivos ...
git add .
git commit -m "feat: implementa tarea X"
git push origin <nombre-branch>
```

**Importante**: cada worktree tiene su propia branch, así que el push va a esa branch:

```bash
# En proyecto-task-1/
git push origin task-1-auth

# En proyecto-task-2/
git push origin task-2-products
```

### 7. Limpieza después de integrar

Cuando termines de mergear todo (ver `/integrar`), elimina los worktrees:

```bash
# Desde el repo principal
git worktree remove ../proyecto-task-1
git worktree remove ../proyecto-task-2
git worktree remove ../proyecto-task-3
```

Opcionalmente, borra las branches remotas si ya no las necesitas:

```bash
git push origin --delete task-1-auth
git push origin --delete task-2-products
git push origin --delete task-3-ui
```

## Comandos útiles

### Listar worktrees

```bash
git worktree list
```

### Eliminar un worktree

```bash
git worktree remove <ruta-worktree>
```

O si el directorio ya no existe:

```bash
git worktree prune
```

### Mover un worktree

```bash
git worktree move <ruta-vieja> <ruta-nueva>
```

### Ver información de un worktree

```bash
cd /ruta/al/worktree
git branch
git status
```

## Ejemplo completo

**Contexto**: tienes 3 tareas del plan de reparto.

**Paso 1: crear worktrees**

```bash
cd ~/proyecto

git worktree add -b task-1-auth ../proyecto-task-1 main
git worktree add -b task-2-products ../proyecto-task-2 main
git worktree add -b task-3-ui ../proyecto-task-3 main

git worktree list
# ~/proyecto              abc123 [main]
# ~/proyecto-task-1       def456 [task-1-auth]
# ~/proyecto-task-2       789ghi [task-2-products]
# ~/proyecto-task-3       jkl012 [task-3-ui]
```

**Paso 2: lanzar agentes**

En Cursor o terminal:

```bash
# Terminal 1
cd ~/proyecto-task-1
# Lanza agente con instrucciones de Tarea 1

# Terminal 2
cd ~/proyecto-task-2
# Lanza agente con instrucciones de Tarea 2

# Terminal 3
cd ~/proyecto-task-3
# Lanza agente con instrucciones de Tarea 3
```

**Paso 3: cada agente trabaja**

Cada uno hace commits en su branch:

```bash
# En proyecto-task-1
git add .
git commit -m "feat(auth): implementa login"
git push origin task-1-auth

# En proyecto-task-2 (en paralelo)
git add .
git commit -m "feat(products): implementa CRUD"
git push origin task-2-products
```

**Paso 4: integración**

Cuando todos terminan, desde el repo principal:

```bash
cd ~/proyecto
git checkout main

# Merge cada branch (ver /integrar para el proceso completo)
git merge task-1-auth
git merge task-2-products
git merge task-3-ui

git push origin main
```

**Paso 5: limpieza**

```bash
git worktree remove ../proyecto-task-1
git worktree remove ../proyecto-task-2
git worktree remove ../proyecto-task-3

git branch -d task-1-auth task-2-products task-3-ui
git push origin --delete task-1-auth task-2-products task-3-ui
```

## Convenciones de nombres

**Para branches:**
- `task-<numero>-<descripcion-corta>`
- Ejemplos: `task-1-auth`, `task-2-products`, `task-3-ui`

**Para directorios de worktrees:**
- `../<repo-nombre>-task-<numero>`
- Ejemplos: `../proyecto-task-1`, `../proyecto-task-2`

Esto hace fácil identificar qué worktree corresponde a qué tarea.

## Problemas comunes

### "fatal: '<path>' is already checked out"

**Causa**: ya existe un worktree con esa branch.

**Solución**: lista los worktrees con `git worktree list` y elimina el duplicado, o usa otra branch.

### "fatal: '<path>' already exists"

**Causa**: el directorio ya existe.

**Solución**: usa otra ruta o elimina el directorio primero.

### No puedo hacer push

**Causa**: la branch no existe en remoto.

**Solución**: primera vez haz:

```bash
git push -u origin <nombre-branch>
```

### Olvidé en qué worktree estoy

**Solución**:

```bash
pwd  # muestra la ruta
git branch --show-current  # muestra la branch actual
```

## Entregables

Al terminar de usar esta skill, debes tener:

1. ✅ Un **worktree por tarea**, cada uno en su directorio
2. ✅ Una **branch por worktree**, nombrada consistentemente
3. ✅ **Lista de worktrees** activos (`git worktree list`)
4. ✅ **Instrucciones para cada agente** de en qué directorio trabajar

## Transición a las siguientes skills

Después de crear los worktrees:
1. Asigna a cada agente su worktree y la tarea correspondiente (del `/reparto`)
2. Usa `/archivos-compartidos` si algún archivo necesita ser tocado por varios agentes
3. Cada agente usa `/antes-del-merge` antes de hacer push final
4. Usa `/integrar` para mergear todas las branches en el orden correcto

## Notas importantes

- **No borres un worktree mientras un agente esté trabajando en él**: puede perder cambios
- **Pushea frecuentemente**: si un worktree se corrompe, el push te salva
- **Verifica que las branches son diferentes**: no crees dos worktrees con la misma branch
- **Los worktrees comparten el `.git`**: cambios en configuración (ej: `.gitignore`) afectan a todos
