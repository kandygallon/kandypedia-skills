---
name: worktrees
description: Crea un worktree de git y una rama por cada tarea del reparto, y prepara cada uno (dependencias, .env) para que varios agentes trabajen a la vez sin pisarse. Úsala cuando docs/reparto.md y docs/contrato.md están aprobados y vas a lanzar varios agentes.
disable-model-invocation: true
---

# Worktrees

> **De dónde sale.** Probé Multitask, la función de Cursor para lanzar varias tareas a la vez, con dos
> encargos sobre el mismo archivo: añadir un gráfico debajo del título y quitar todo lo de debajo del título
> para poner un login. No hubo error ni conflicto. El agente se quedó con el login, dejó el gráfico fuera y
> solo lo explicó en su razonamiento. Con un worktree por agente, cada uno trabaja en su propia copia, y si
> dos chocan lo ves al juntar las ramas. No lo decide un agente por ti sin avisar.

## Cuándo usarla

- Ya tienes `docs/reparto.md` aprobado (y `docs/contrato.md`, si las tareas tienen puntos de unión).
- Vas a lanzar varios agentes a la vez, cada uno con su tarea.
- Necesitas que cada agente tenga su propia carpeta y su propia rama.

**No la uses** si solo hay una tarea o vas a hacer las tareas una detrás de otra: basta con una rama en el repo
principal.

## Pasos

1. Ve a `main` y comprueba que no hay cambios sin commitear:

   ```bash
   git switch main
   git status
   ```

2. **Los docs del plan tienen que estar commiteados en `main`.** Un worktree nuevo solo ve lo commiteado: si
   `docs/reparto.md` o `docs/contrato.md` no lo están, los agentes no los tienen. Si `git status` los muestra:

   ```bash
   git add docs/
   git commit -m "docs: reparto y contrato antes de los worktrees"
   ```

   Añade los archivos por su nombre. **Nunca uses `git add .`**: podrías commitear un `.env` con claves.
3. Crea un worktree por cada tarea de `docs/reparto.md`, siempre desde `main`. Un worktree es otra carpeta del
   mismo repositorio con su propia rama; todos comparten el `.git`:

   ```bash
   git worktree add -b task-1-auth ../proyecto-task-1 main
   git worktree add -b task-2-products ../proyecto-task-2 main
   git worktree add -b task-3-ui ../proyecto-task-3 main
   ```

   Rama: `task-<número>-<descripción>`. Carpeta: `../<repo>-task-<número>`. Una rama distinta por worktree.
4. **Prepara cada worktree** antes de lanzar a su agente. Git no copia lo que no está commiteado, así que cada
   worktree empieza sin dependencias instaladas y sin `.env`:

   ```bash
   cd ../proyecto-task-1
   npm install             # o el comando de instalación del proyecto
   cp ../proyecto/.env .   # solo si el proyecto lo necesita
   git check-ignore .env   # tiene que responder ".env"
   ```

   **DETENTE** si `git check-ignore .env` no responde nada: el `.env` acabaría en un commit. Avisa a la persona
   y añade `.env` al `.gitignore` antes de seguir.
5. Comprueba que están todos, cada uno con su rama:

   ```bash
   git worktree list
   ```

6. Abre un agente en cada carpeta con su tarea: «Abre un agente en `../proyecto-task-1/` con la Tarea 1 de
   `docs/reparto.md`». Cada agente:
   - lee `docs/reparto.md` y `docs/contrato.md` antes de empezar;
   - toca solo los archivos de su tarea;
   - commitea en su rama añadiendo los archivos por su ruta (`git add src/services/auth.js`), nunca en `main`.

   No borres un worktree mientras su agente trabaja en él: perderías sus cambios.
7. Cuando `/integrar` haya juntado todas las ramas, borra los worktrees y las ramas desde el repo principal:

   ```bash
   git worktree remove ../proyecto-task-1
   git branch -d task-1-auth
   ```

   Si la carpeta ya no existe, usa `git worktree prune`.

## Qué produce

- Un worktree por tarea, cada uno en su carpeta y con su propia rama.
- Cada worktree con las dependencias instaladas y el `.env` ignorado por git.
- La lista de `git worktree list` y qué agente trabaja en cada carpeta.

## Siguiente

- `/archivos-compartidos` si algún archivo lo necesitan varios agentes.
- `/tests-primero` en cada worktree para implementar a partir de los ejemplos del contrato.
- `/antes-del-merge` en cada worktree cuando su agente termine.
- `/integrar` para juntar las ramas en el orden de `docs/reparto.md`.

Si necesitas un ejemplo completo, lee [references/ejemplo.md](references/ejemplo.md).
