# Ejemplos de Comandos

## Ejemplo completo: 3 tareas

### Paso 1: Crear worktrees

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

### Paso 2: Lanzar agentes

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

### Paso 3: Trabajo en paralelo

Cada agente en su worktree:

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

### Paso 4: Integración

```bash
cd ~/proyecto
git checkout main

git merge task-1-auth
git merge task-2-products
git merge task-3-ui

git push origin main
```

### Paso 5: Limpieza

```bash
git worktree remove ../proyecto-task-1
git worktree remove ../proyecto-task-2
git worktree remove ../proyecto-task-3

git branch -d task-1-auth task-2-products task-3-ui
git push origin --delete task-1-auth task-2-products task-3-ui
```
