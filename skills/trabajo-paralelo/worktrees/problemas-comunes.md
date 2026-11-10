# Problemas Comunes

## "fatal: '<path>' is already checked out"

**Causa:** Ya existe worktree con esa branch.

**Solución:**
```bash
git worktree list  # Ver worktrees existentes
git worktree remove <path>  # Eliminar duplicado
# O usa otra branch
```

## "fatal: '<path>' already exists"

**Causa:** El directorio ya existe.

**Solución:**
```bash
# Usar otra ruta o eliminar directorio
rm -rf <path>
git worktree add ...
```

## No puedo hacer push

**Causa:** La branch no existe en remoto.

**Solución:**
```bash
# Primera vez
git push -u origin <nombre-branch>
```

## Olvidé en qué worktree estoy

**Solución:**
```bash
pwd  # Muestra ruta
git branch --show-current  # Muestra branch
```

## El worktree está corrupto

**Solución:**
```bash
# Si hiciste push antes
git worktree remove <path>
git worktree add -b <branch> <path> origin/<branch>

# Si no hiciste push, el trabajo se perdió
```

## Quiero mover un worktree

```bash
git worktree move <ruta-vieja> <ruta-nueva>
```
