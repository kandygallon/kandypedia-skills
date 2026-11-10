---
name: worktrees
description: Usa esta skill para configurar worktrees de git, uno por tarea paralela, para que varios agentes trabajen sin pisarse.
---

# Worktrees

## ¿Cuándo usar esta skill?

Úsala cuando:
- Ya dividiste el trabajo (con `/reparto`)
- Vas a lanzar varios agentes al mismo tiempo
- Cada agente necesita su propio directorio

**No la uses** cuando solo hay una tarea o las tareas van en secuencia.

## Filosofía

Un **worktree** es un directorio adicional del mismo repo con su propia branch. Te permite tener varias branches activas al mismo tiempo en directorios separados.

**Sin worktrees:** Solo un agente a la vez, cambiar branch pierde cambios.

**Con worktrees:** Cada agente en su directorio sin pisarse.

## Crear worktrees

Para cada tarea:

```bash
git worktree add -b <nombre-branch> <directorio> <base-branch>
```

**Ejemplo:**

```bash
# Tarea 1
git worktree add -b task-1-auth ../proyecto-task-1 main

# Tarea 2
git worktree add -b task-2-products ../proyecto-task-2 main

# Tarea 3
git worktree add -b task-3-ui ../proyecto-task-3 main
```

**Resultado:**
```
/home/user/
├── proyecto/          # repo principal (main)
├── proyecto-task-1/   # worktree task-1-auth
├── proyecto-task-2/   # worktree task-2-products
└── proyecto-task-3/   # worktree task-3-ui
```

## Trabajo en worktrees

Cada agente en su directorio:

```bash
cd ~/proyecto-task-1
# Trabaja, edita archivos
git add .
git commit -m "feat: implementa tarea"
git push origin task-1-auth
```

## Comandos útiles

**Listar worktrees:**
```bash
git worktree list
```

**Eliminar worktree:**
```bash
git worktree remove ../proyecto-task-1
```

**Si el directorio ya no existe:**
```bash
git worktree prune
```

Ver [ejemplos-comandos.md](ejemplos-comandos.md) con casos completos y [problemas-comunes.md](problemas-comunes.md) para troubleshooting.

## Limpieza después de integrar

```bash
git worktree remove ../proyecto-task-1
git worktree remove ../proyecto-task-2
git branch -d task-1-auth task-2-products
git push origin --delete task-1-auth task-2-products
```

## Convenciones

**Branches:** `task-<numero>-<descripcion>` (ej: `task-1-auth`)
**Directorios:** `../<repo>-task-<numero>` (ej: `../proyecto-task-1`)

## Entregables

Al terminar:

1. ✅ Un worktree por tarea
2. ✅ Una branch por worktree
3. ✅ Lista de worktrees activos
4. ✅ Instrucciones para cada agente

## Transición

Después de crear:
1. Asigna cada agente a su worktree
2. Usa `/archivos-compartidos` si aplica
3. Cada agente usa `/tests-primero`
4. Usa `/antes-del-merge` antes de terminar
5. Usa `/integrar` para mergear

## Referencias

- [ejemplos-comandos.md](ejemplos-comandos.md): Casos completos de uso
- [problemas-comunes.md](problemas-comunes.md): Troubleshooting

## Notas

- **No borres worktree mientras agente trabaja**
- **Pushea frecuentemente**
- **Verifica branches diferentes**
- **Los worktrees comparten .git**
