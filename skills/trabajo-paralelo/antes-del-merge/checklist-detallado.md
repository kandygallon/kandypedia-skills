# Checklist Detallado

## ✅ 1. La tarea está completa

**Verifica:** Implementaste todo del plan de reparto para tu tarea.

**Cómo:** Lee el entregable de tu tarea y ejecútalo/pruébalo.

**Si falla:** Arréglalo antes de mergear.

## ✅ 2. Los tests pasan

**Verifica:** Si el proyecto tiene tests, ¿pasan todos?

```bash
npm test
pytest
cargo test
```

**Si fallan:**
- ¿Ya estaba fallando? Verifica en main
- ¿Lo rompiste tú? Arréglalo
- ¿Test mal escrito? Documéntalo

**Regla:** Si rompiste test que pasaba, NO puedes mergear.

## ✅ 3. El linter no se queja

```bash
npm run lint
eslint .
flake8 .
```

**Si hay errores:** Arregla los de tu código (ignora los preexistentes).

**Autofix:**
```bash
npm run lint -- --fix
prettier --write .
```

## ✅ 4. Respetaste fronteras

```bash
git diff --name-only origin/main
```

Compara con archivos asignados en `/reparto`.

**Si modificaste archivos no asignados:**
- ¿Es compartido con owner? → `REQUESTS.md`
- ¿Es de otra tarea? → Deshaz o coordina
- ¿No estaba asignado? → Documenta en PR

## ✅ 5. Todo commiteado

```bash
git status
```

Debe mostrar "nothing to commit, working tree clean".

**Si hay cambios:**
```bash
git add <archivos>
git commit -m "descripción"
```

## ✅ 6. Commits claros

```bash
git log --oneline origin/main..HEAD
```

**Buenos:** `feat(products): agrega CRUD`, `test(products): agrega tests`
**Malos:** `wip`, `fix`, `asdf`

**Si son malos:** `git rebase -i origin/main` para reescribir.

## ✅ 7. Branch actualizada

```bash
git fetch origin
git log HEAD..origin/main --oneline
```

Si hay commits nuevos:
```bash
git merge origin/main
# Resuelve conflictos si hay
```

## ✅ 8. Documentaste cambios

- ¿Agregaste README si es módulo nuevo?
- ¿Actualizaste docs si cambiaste comportamiento?
- ¿Comentaste código complejo?

**Qué documentar:** decisiones no obvias, configuración necesaria, APIs públicas.

## ✅ 9. Limpiaste código temporal

```bash
grep -r "console.log" src/
grep -r "TODO" src/
find . -name "test.*" -o -name "playground.*"
```

Elimina console.logs, TODOs temporales, archivos de prueba.

## ✅ 10. Hiciste push

```bash
git push -u origin <nombre-branch>
```

O si ya hiciste push: `git push`
