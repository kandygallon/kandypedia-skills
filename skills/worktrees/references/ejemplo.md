# Ejemplo de worktrees

Contexto: `docs/reparto.md` tiene tres tareas en la ronda 1 y `docs/contrato.md` está aprobado.

## 1. Comprobar `main`

```bash
cd ~/proyecto
git switch main
git status
```

Si `docs/reparto.md` o `docs/contrato.md` aparecen sin commitear:

```bash
git add docs/
git commit -m "docs: reparto y contrato antes de los worktrees"
```

## 2. Crear los worktrees

```bash
git worktree add -b task-1-auth ../proyecto-task-1 main
git worktree add -b task-2-products ../proyecto-task-2 main
git worktree add -b task-3-ui ../proyecto-task-3 main
```

Estructura resultante:

```
~/
├── proyecto/            # repo principal (main)
├── proyecto-task-1/     # rama task-1-auth
├── proyecto-task-2/     # rama task-2-products
└── proyecto-task-3/     # rama task-3-ui
```

## 3. Preparar cada worktree

Repite en cada carpeta:

```bash
cd ~/proyecto-task-1
npm install
cp ~/proyecto/.env .
git check-ignore .env
# .env
```

Si la última línea no aparece, detente: el `.env` no está ignorado y acabaría en un commit.

## 4. Comprobar

```bash
git worktree list
# ~/proyecto           abc123 [main]
# ~/proyecto-task-1    def456 [task-1-auth]
# ~/proyecto-task-2    789abc [task-2-products]
# ~/proyecto-task-3    fed321 [task-3-ui]
```

## 5. Lanzar los agentes

Abre un agente en cada carpeta, con su encargo:

- En `~/proyecto-task-1/`: «Haz la Tarea 1 de `docs/reparto.md`. Lee antes `docs/contrato.md`.»
- En `~/proyecto-task-2/`: «Haz la Tarea 2 de `docs/reparto.md`. Lee antes `docs/contrato.md`.»
- En `~/proyecto-task-3/`: «Haz la Tarea 3 de `docs/reparto.md`. Lee antes `docs/contrato.md`.»

Cada agente commitea en su rama, añadiendo los archivos por su ruta:

```bash
# En ~/proyecto-task-1
git add src/services/auth.js src/components/Login.jsx
git commit -m "feat(auth): inicio de sesión"
git push -u origin task-1-auth   # solo si el proyecto usa un remoto
```

## 6. Integrar y limpiar

Cuando todos terminan, `/antes-del-merge` en cada worktree y `/integrar` desde el repo principal. Después:

```bash
cd ~/proyecto
git worktree remove ../proyecto-task-1
git worktree remove ../proyecto-task-2
git worktree remove ../proyecto-task-3
git branch -d task-1-auth task-2-products task-3-ui
```

## Problemas frecuentes

| Mensaje o situación | Causa | Qué hacer |
|---|---|---|
| `fatal: '<rama>' is already checked out` | Esa rama ya está abierta en otro worktree | `git worktree list` y usa otra rama |
| `fatal: '<ruta>' already exists` | La carpeta ya existe | Usa otra ruta o comprueba qué hay en ella antes de borrarla |
| El push falla la primera vez | La rama no existe en el remoto | `git push -u origin <rama>` |
| No sabes en qué worktree estás | — | `pwd` y `git branch --show-current` |
| El agente no encuentra `docs/reparto.md` | No estaba commiteado en `main` al crear el worktree | Commitéalo en `main` y, en el worktree, `git merge main` |
| Cambiaste el `.gitignore` en un worktree | Cada rama tiene su propio `.gitignore` | Cámbialo en `main` y tráelo con `git merge main` |
