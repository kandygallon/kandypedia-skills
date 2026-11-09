---
name: Integrar
description: Usa esta skill para mergear todas las branches paralelas en el orden correcto, resolver conflictos y verificar que el resultado funciona end-to-end.
---

# Integrar

## ¿Cuándo usar esta skill?

Úsala cuando:
- Todas las tareas paralelas terminaron (cada una pasó `/antes-del-merge`)
- Tienes múltiples branches listas para mergear
- Necesitas integrar todo en la branch principal
- Quieres verificar que el sistema completo funciona

**No la uses** cuando:
- Todavía hay tareas en progreso (espera a que terminen)
- Solo hay una branch para mergear (hazlo directamente)

## Filosofía

La integración es el momento de verdad: el código que funcionaba por separado debe funcionar junto. Incluso con buena planificación, pueden surgir:
- **Conflictos de git**: dos branches modificaron las mismas líneas
- **Conflictos lógicos**: dos branches funcionan solas, pero juntas no (ej: ambas usan el puerto 3000)
- **Dependencias faltantes**: una tarea agregó una librería que otra necesita

Esta skill te guía para integrar todo en el **orden correcto**, **resolver conflictos** y **verificar** que el resultado final funciona.

## Proceso paso a paso

### 1. Preparación

**Antes de empezar:**

1. Todas las tareas pasaron el checklist de `/antes-del-merge`
2. Todas las branches están pusheadas al remoto
3. Tienes el plan de reparto a mano (para saber el orden)

**Ubícate en el repo principal:**

```bash
cd ~/proyecto  # El worktree principal, no los auxiliares
git checkout main
git pull origin main  # Asegúrate de tener la última versión
```

### 2. Determina el orden de merge

**Regla general:**

1. **Primero**: tareas que son dependencias de otras (ej: setup, modelos)
2. **Segundo**: tareas que tienen archivos compartidos con owner asignado
3. **Tercero**: tareas independientes (pueden ir en cualquier orden)
4. **Último**: tareas de integración o tests end-to-end

**Ejemplo:**

Si tienes:
- Tarea 1: Setup (crea config, rutas base) → **Primero** (otros dependen)
- Tarea 2: API de productos (modelos + controladores) → **Segundo** (UI depende)
- Tarea 3: UI de productos (consume API) → **Tercero** (depende de Tarea 2)
- Tarea 4: Reportes (independiente) → **Tercero** (paralelo con Tarea 3)

Orden de merge:
```
1. task-1-setup
2. task-2-products
3. task-3-ui
4. task-4-reports
```

**Consulta el plan de reparto:**

```markdown
## Integración Final
1. Tarea 1 y Tarea 2 (sin conflictos esperados)
2. Tarea 3 (puede tener conflictos con Tarea 1, resolver manual)
```

### 3. Mergea la primera branch

```bash
git merge task-1-setup
```

**Si el merge es limpio (no hay conflictos):**

```bash
# Git dice: "Fast-forward" o "Merge made by..."
# Verifica que funciona
npm install  # Por si agregó dependencias
npm test     # Por si agregó tests

# Si todo está bien, continúa con la siguiente
```

**Si hay conflictos (ver sección de resolución abajo):**

```bash
# Git dice: "CONFLICT (content): Merge conflict in <archivo>"
# Resuelve el conflicto manual (ver paso 5)
```

### 4. Mergea las siguientes branches una por una

Repite para cada branch en orden:

```bash
git merge task-2-products
# Verifica
npm test

git merge task-3-ui
# Verifica
npm test

git merge task-4-reports
# Verifica
npm test
```

**Importante:**
- **No merges todas juntas**: hazlo una por una para detectar qué merge causa problemas
- **Verifica después de cada merge**: si algo se rompe, sabes qué branch lo causó
- **Commitea después de resolver conflictos**: `git commit` (sin `-m`, git genera el mensaje)

### 5. Resolución de conflictos

**Cuando hay conflicto:**

Git marca el archivo con:

```js
<<<<<<< HEAD
// Código que ya estaba en main
const port = 3000;
=======
// Código que viene de la branch que estás mergeando
const port = 5000;
>>>>>>> task-2-products
```

**Cómo resolver:**

1. **Entiende qué hace cada versión:**
   - `HEAD`: el código actual en tu branch (main después de merges previos)
   - `task-2-products`: el código de la branch que estás mergeando

2. **Decide qué quieres:**
   - ¿Solo una versión? Elimina la otra y los marcadores
   - ¿Ambas? Combínalas de forma lógica
   - ¿Ninguna? Escribe algo nuevo

3. **Edita el archivo:**

```js
// Resolución: usa el puerto de la config, no hardcodeado
const port = process.env.PORT || 3000;
```

4. **Marca como resuelto:**

```bash
git add <archivo-resuelto>
```

5. **Termina el merge:**

```bash
git commit  # Sin -m, git genera mensaje de merge
```

6. **Verifica que funciona:**

```bash
npm test
npm start  # O lo que sea relevante
```

**Si no sabes cómo resolver:**
- Consulta a los agentes/personas que trabajaron en ambas branches
- Lee el historial: `git log --oneline --graph task-1-setup task-2-products`
- Prueba ambas versiones por separado en worktrees temporales

### 6. Merge de archivos compartidos (protocolo especial)

Si usaste `/archivos-compartidos` con el protocolo de `REQUESTS.md`:

**Antes de mergear la branch owner del archivo compartido:**

1. Revisa las solicitudes de cambio:

```bash
# Ver REQUESTS.md de cada branch
git show origin/task-2-products:REQUESTS.md
git show origin/task-3-ui:REQUESTS.md
```

2. Aplica los cambios en la branch owner:

```bash
git checkout task-1-setup
# Edita el archivo compartido con los cambios solicitados
git add <archivo-compartido>
git commit -m "feat: aplica cambios solicitados de otras tareas"
git push origin task-1-setup
```

3. Ahora mergea la branch owner (ya tiene todo):

```bash
git checkout main
git merge task-1-setup
```

4. Las otras branches no deberían tocar ese archivo, así que sus merges serán limpios.

### 7. Verifica la integración completa

Después de mergear todo:

**Tests:**

```bash
npm test            # O el comando de tests del proyecto
npm run test:e2e    # Si hay tests end-to-end
```

**Linter:**

```bash
npm run lint
```

**Ejecuta el proyecto:**

```bash
npm start
# O el comando para levantar el proyecto
```

**Verifica manualmente:**
- Navega por la aplicación
- Prueba las funcionalidades de cada tarea
- Verifica que las integraciones entre tareas funcionan

**Ejemplo:**
- Loguea un usuario (Tarea 1)
- Crea un producto (Tarea 2)
- Ve la lista de productos en la UI (Tarea 3)
- Genera un reporte (Tarea 4)

Si algo no funciona, identifica qué merge lo rompió y revierte:

```bash
git log --oneline -10  # Ve los últimos merges
git revert <commit-sha>  # Revierte el merge problemático
# O restaura main:
git reset --hard origin/main  # CUIDADO: pierdes los merges
```

### 8. Pushea la integración

Si todo funciona:

```bash
git push origin main
```

**Antes de pushear, considera:**
- ¿Hay CI/CD? Espera a que pase
- ¿Hay code review? Crea un PR de la branch de integración
- ¿Es producción? Verifica en staging primero

### 9. Limpia worktrees y branches

Después de un push exitoso:

```bash
# Elimina worktrees
git worktree remove ../proyecto-task-1
git worktree remove ../proyecto-task-2
git worktree remove ../proyecto-task-3

# Elimina branches locales
git branch -d task-1-setup task-2-products task-3-ui

# Elimina branches remotas (opcional, si ya no las necesitas)
git push origin --delete task-1-setup
git push origin --delete task-2-products
git push origin --delete task-3-ui
```

### 10. Produce reporte de integración

Documenta qué se integró:

```markdown
# Reporte de Integración

## Fecha
[fecha]

## Branches integradas
- `task-1-setup`: Setup inicial (rutas, config)
- `task-2-products`: API de productos
- `task-3-ui`: UI de productos
- `task-4-reports`: Reportes de stock

## Orden de merge
1. task-1-setup (sin conflictos)
2. task-2-products (sin conflictos)
3. task-3-ui (conflicto en `routes.js`, resuelto manual)
4. task-4-reports (sin conflictos)

## Conflictos resueltos
- `src/routes.js`: Ambas tareas agregaron rutas en el mismo lugar.
  Resolución: combinadas en orden alfabético.

## Verificación
- ✅ Tests pasan (23 tests, 0 fallos)
- ✅ Linter pasa sin errores
- ✅ Aplicación funciona end-to-end
- ✅ Todas las funcionalidades verificadas manualmente

## Commits finales
- `abc1234`: Merge task-1-setup
- `def5678`: Merge task-2-products
- `ghi9012`: Merge task-3-ui (conflictos resueltos)
- `jkl3456`: Merge task-4-reports

## Próximos pasos
- Deploy a staging
- QA manual completo
- Deploy a producción
```

## Estrategias de merge

### Estrategia 1: Merge commits (recomendado)

```bash
git merge task-1-setup
```

**Pros:**
- Mantiene historial completo
- Fácil ver qué branch aportó qué

**Contras:**
- Historial más complejo visualmente

### Estrategia 2: Squash merge

```bash
git merge --squash task-1-setup
git commit -m "feat: integra setup (task-1)"
```

**Pros:**
- Historial más limpio (un commit por tarea)

**Contras:**
- Pierdes detalle de commits individuales

### Estrategia 3: Rebase (avanzado)

```bash
git rebase task-1-setup
```

**Pros:**
- Historial lineal (sin merges)

**Contras:**
- Más propenso a conflictos
- Reescribe historia (no usar en branches compartidas)

**Recomendación:** usa merge commits simple para integración de tareas paralelas.

## Problemas comunes

### "CONFLICT: both modified <archivo>"

**Causa:** Ambas branches modificaron las mismas líneas del mismo archivo.

**Solución:** Resuelve manual (ver paso 5).

### "CONFLICT: both added <archivo>"

**Causa:** Ambas branches crearon un archivo con el mismo nombre pero diferente contenido.

**Solución:**
- Si los archivos deben ser uno solo: combina el contenido
- Si son archivos diferentes: renombra uno

### Tests pasan individualmente pero fallan juntos

**Causa:** Conflicto lógico (ej: ambas tareas usan el mismo puerto, nombre de variable global, etc.)

**Solución:**
- Identifica el conflicto (busca en los archivos de ambas tareas)
- Cambia uno para que no colisione (ej: usa puerto dinámico, namespaces)

### Un merge rompió algo pero no hubo conflictos de git

**Causa:** Las branches tocaron partes relacionadas pero no las mismas líneas.

**Solución:**
- Revierte el merge: `git revert -m 1 <merge-commit-sha>`
- Investiga qué salió mal
- Arregla en la branch original y vuelve a intentar el merge

### No sé en qué orden mergear

**Solución:**
- Consulta el plan de reparto (sección "Integración Final")
- Regla general: dependencias primero, dependientes después
- Si hay dudas, mergea una y ve si las demás causan conflictos

## Checklist de integración

```markdown
- [ ] Todas las tareas pasaron /antes-del-merge
- [ ] Estoy en main y tengo la última versión
- [ ] Determiné el orden de merge según dependencias
- [ ] Mergeé cada branch una por una
- [ ] Resolví conflictos (si hubo)
- [ ] Verifiqué después de cada merge (tests, linter)
- [ ] Ejecuté el proyecto completo end-to-end
- [ ] Todas las funcionalidades trabajan juntas
- [ ] Hice push a main
- [ ] Limpié worktrees y branches
- [ ] Documenté la integración
```

## Entregables

Al terminar de usar esta skill, debes tener:

1. ✅ Todas las branches **integradas en main**
2. ✅ **Conflictos resueltos** (si hubo)
3. ✅ **Tests pasando** en la integración completa
4. ✅ **Proyecto funcional** end-to-end
5. ✅ **Reporte de integración** documentado
6. ✅ **Worktrees y branches limpios**

## Transición después de integrar

Después de la integración exitosa:
- Deploy a staging/producción
- Code review del resultado (si aplica)
- Retrospectiva: ¿qué funcionó bien? ¿qué mejorar la próxima vez?

## Notas importantes

- **No integres si algo no funciona**: mejor resolver en la branch individual
- **Mergea una branch a la vez**: no hagas `git merge branch-1 branch-2 branch-3` de golpe
- **Verifica después de cada merge**: detecta problemas temprano
- **Documenta las resoluciones**: el próximo que integre te lo agradecerá
- **Si algo sale muy mal, puedes empezar de nuevo**: `git reset --hard origin/main` y vuelve a intentar (solo antes de pushear)
