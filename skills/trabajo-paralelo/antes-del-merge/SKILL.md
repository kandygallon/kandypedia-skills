---
name: Antes del Merge
description: Usa esta skill antes de mergear tu branch. Verifica que tu tarea está completa, no rompiste nada, y respetaste las fronteras asignadas.
---

# Antes del Merge

## ¿Cuándo usar esta skill?

Úsala cuando:
- Terminaste tu tarea y estás listo para mergear
- Antes de hacer el último push de tu branch
- Antes de avisar "mi parte está lista"

**No la uses** cuando:
- Todavía estás en medio del trabajo (aún no terminaste)

## Filosofía

Mergear código sin verificar es la forma más rápida de romper el proyecto. Esta skill es un **checklist de calidad** que cada agente debe pasar antes de declarar su tarea completa.

Si todos los agentes siguen este checklist, la integración final será mucho más fácil.

## Checklist completo

### ✅ 1. La tarea está completa

**Verifica:**
- ¿Implementaste todo lo que decía el plan de reparto para tu tarea?
- ¿El entregable funciona como se esperaba?
- ¿Puedes demostrar que funciona (manualmente o con tests)?

**Cómo verificar:**

Lee el plan de reparto de tu tarea:

```markdown
#### Tarea 2: API de Productos
- **Entregable**: API REST funcional para crear, listar, editar y eliminar productos
- **Verificación**: `curl` a los endpoints debe devolver datos correctos
```

Ejecuta la verificación:

```bash
# Ejemplo para API
curl http://localhost:3000/api/products
# Debe devolver JSON con lista de productos

curl -X POST http://localhost:3000/api/products -d '{"name":"Test"}' -H "Content-Type: application/json"
# Debe crear un producto
```

Si algo no funciona, **no merges todavía**. Arréglalo primero.

### ✅ 2. Los tests pasan

**Verifica:**
- Si el proyecto tiene tests, ¿pasan todos?
- Si escribiste tests nuevos, ¿están pasando?

**Cómo verificar:**

```bash
# Depende del proyecto, ejemplos:
npm test
pytest
cargo test
./gradlew test
```

**Si algún test falla:**
- ¿Es un test que ya estaba fallando antes de ti? (verifica en `main`)
- ¿Es un test que tu cambio rompió? (arréglalo)
- ¿Es un test que agregaste y está mal? (arréglalo)

**Regla:**
- Si rompiste un test que antes pasaba, **no puedes mergear** hasta arreglarlo
- Si un test ya estaba roto y no es tu responsabilidad, documéntalo en tu PR

### ✅ 3. El linter no se queja

**Verifica:**
- ¿El código sigue el estilo del proyecto?
- ¿No hay errores de linter?

**Cómo verificar:**

```bash
# Ejemplos según el proyecto:
npm run lint
eslint .
flake8 .
rubocop
```

**Si hay errores:**
- Arregla los errores de lint en tu código
- Si el linter se queja de código que no tocaste, ignóralo (no es tu responsabilidad)

**Regla:**
- Tu código nuevo debe pasar el linter
- Si hay configuración de autofix, úsala:

```bash
npm run lint -- --fix
prettier --write .
black .
```

### ✅ 4. Respetaste las fronteras de archivos

**Verifica:**
- ¿Solo modificaste archivos que tu tarea tenía asignados?
- ¿No tocaste archivos de otras tareas?

**Cómo verificar:**

Lista los archivos que cambiaste:

```bash
git diff --name-only origin/main
```

Compara con el plan de reparto:

```markdown
#### Tarea 2: API de Productos
- **Archivos que posee**:
  - `src/models/Product.js`
  - `src/controllers/products.js`
  - `src/routes/products.js`
```

**Si modificaste un archivo no asignado:**

1. ¿Es un archivo compartido con owner? → Crea `REQUESTS.md` (ver `/archivos-compartidos`)
2. ¿Es un archivo de otra tarea? → **Deshaz el cambio** o coordina con esa tarea
3. ¿Es un archivo nuevo que nadie había asignado? → Documéntalo en tu PR

**Regla:**
- Si no estaba en tu lista de archivos, no deberías haberlo tocado
- Excepción: archivos compartidos con protocolo documentado

### ✅ 5. No hay cambios sin commitear

**Verifica:**
- ¿Todos tus cambios están en commits?
- ¿No dejaste archivos modificados sin agregar?

**Cómo verificar:**

```bash
git status
```

**Si hay archivos "modified" o "untracked":**

```bash
git add <archivos-que-quieres-incluir>
git commit -m "feat: descripción del cambio"
```

**Si hay archivos que NO quieres commitear:**

```bash
# Descártalos
git checkout -- <archivo>

# O agrégalos a .gitignore si son generados
echo "<archivo>" >> .gitignore
```

### ✅ 6. Los commits tienen mensajes claros

**Verifica:**
- ¿Tus commits explican qué hiciste?
- ¿Siguen alguna convención (ej: Conventional Commits)?

**Cómo verificar:**

```bash
git log --oneline origin/main..HEAD
```

**Buenos commits:**
```
feat(products): agrega modelo de Product
feat(products): implementa CRUD en controlador
test(products): agrega tests de API
```

**Malos commits:**
```
wip
fix
asdf
cambios
```

**Si tus commits son malos**, puedes reescribir la historia (solo si no has pusheado):

```bash
git rebase -i origin/main
# Marca commits para "reword" (cambiar mensaje) o "squash" (combinar)
```

**O haz un squash commit:**

```bash
git reset --soft origin/main
git commit -m "feat(products): implementa API REST de productos completa"
```

### ✅ 7. La branch está actualizada con main

**Verifica:**
- ¿Tu branch tiene los últimos cambios de `main`?
- ¿Alguien más mergeó algo mientras trabajabas?

**Cómo verificar:**

```bash
git fetch origin
git log HEAD..origin/main --oneline
```

Si hay commits nuevos en `main`, actualiza:

```bash
git merge origin/main
# O si prefieres rebase:
git rebase origin/main
```

**Si hay conflictos**, resuélvelos antes de mergear (ver `/integrar` para estrategias).

### ✅ 8. Documentaste cambios importantes

**Verifica:**
- ¿Agregaste un README si creaste un módulo nuevo?
- ¿Actualizaste la documentación existente si cambiaste comportamiento?
- ¿Dejaste comentarios en código complejo?

**Qué documentar:**
- Decisiones no obvias ("usamos X en lugar de Y porque...")
- Configuración necesaria para que funcione tu código
- APIs públicas (funciones, endpoints, comandos)

**Qué NO documentar:**
- Código obvio ("// suma a y b")
- Cosas que ya están en el plan de reparto

### ✅ 9. Limpiaste código temporal

**Verifica:**
- ¿Dejaste `console.log`, `print`, `debugger` que no deberían estar?
- ¿Dejaste comentarios tipo `// TODO: arreglar esto`?
- ¿Dejaste archivos de prueba (`test.js`, `playground.py`)?

**Cómo verificar:**

```bash
# Busca console.logs
grep -r "console.log" src/

# Busca TODOs
grep -r "TODO" src/

# Busca archivos de test manual
find . -name "test.*" -o -name "playground.*"
```

**Si encuentras algo:**
- Elimínalo si no es necesario
- Si es necesario (ej: un TODO válido), déjalo pero documéntalo

### ✅ 10. Hiciste push de tu branch

**Verifica:**
- ¿Tu branch está en el remoto?
- ¿Alguien más puede ver tus cambios?

**Cómo verificar:**

```bash
git push -u origin <nombre-branch>
```

Si ya hiciste push antes, solo:

```bash
git push
```

## Checklist rápido (TL;DR)

Antes de mergear, verifica:

```markdown
- [ ] Mi tarea funciona (lo probé manual o con tests)
- [ ] Los tests pasan
- [ ] El linter no se queja de mi código
- [ ] Solo modifiqué archivos asignados a mi tarea
- [ ] Todos mis cambios están commiteados
- [ ] Mis commits tienen mensajes claros
- [ ] Mi branch está actualizada con main
- [ ] Documenté cambios importantes
- [ ] Limpié código temporal (console.log, TODOs)
- [ ] Hice push de mi branch
```

## Script automatizado (opcional)

Puedes crear un script `pre-merge-check.sh` en tu repo:

```bash
#!/bin/bash

echo "🔍 Verificando antes del merge..."

# 1. Tests
echo "▶ Ejecutando tests..."
npm test || { echo "❌ Tests fallaron"; exit 1; }

# 2. Linter
echo "▶ Ejecutando linter..."
npm run lint || { echo "❌ Linter falló"; exit 1; }

# 3. Cambios sin commitear
if [[ -n $(git status -s) ]]; then
  echo "❌ Hay cambios sin commitear"
  git status -s
  exit 1
fi

# 4. Actualizado con main
git fetch origin
if [[ -n $(git log HEAD..origin/main --oneline) ]]; then
  echo "⚠️  main tiene commits nuevos, considera hacer merge/rebase"
fi

# 5. Console.logs
if grep -r "console.log" src/ > /dev/null; then
  echo "⚠️  Hay console.log en src/"
fi

echo "✅ Verificación completa"
```

Ejecútalo antes de mergear:

```bash
chmod +x pre-merge-check.sh
./pre-merge-check.sh
```

## Qué hacer si algo falla

### Los tests fallan
1. Identifica qué test falló
2. ¿Lo rompiste tú? Arréglalo
3. ¿Ya estaba roto? Verifica en `main`, documéntalo en tu PR

### El linter se queja
1. Ejecuta autofix: `npm run lint -- --fix`
2. Corrige errores manualmente
3. Si el linter está mal configurado, habla con el equipo (no lo desactives tú solo)

### Modificaste archivos de otra tarea
1. Deshaz el cambio: `git checkout origin/main -- <archivo>`
2. Si realmente lo necesitas, usa el protocolo de archivos compartidos (ver `/archivos-compartidos`)

### Hay conflictos con main
1. Haz merge de main: `git merge origin/main`
2. Resuelve conflictos manualmente
3. Verifica que todo sigue funcionando después del merge
4. Commitea: `git commit -m "merge: integra cambios de main"`

### Olvidaste algo de tu tarea
1. Complétalo antes de mergear
2. Commitea y pushea
3. Vuelve a correr este checklist

## Entregables

Al terminar este checklist, debes tener:

1. ✅ Una branch que **pasa todos los checks**
2. ✅ Commits claros y pusheados
3. ✅ Confianza de que tu código **no romperá la integración**
4. ✅ Documentación de cualquier **excepción o problema** que encontraste

## Transición a las siguientes skills

Después de pasar este checklist:
1. Notifica que tu tarea está lista
2. Si eres owner de archivos compartidos, revisa `REQUESTS.md` de otras branches (ver `/archivos-compartidos`)
3. Espera la integración final (ver `/integrar`)

## Notas importantes

- **Este checklist no es opcional**: si lo saltas, causarás problemas en la integración
- **No hagas merge tú mismo** si hay un coordinador de integración: solo notifica que pasaste el checklist
- **Sé honesto**: si algo no está bien, dílo, no lo escondas
- **Mejor prevenir que arreglar**: 10 minutos en este checklist te ahorran horas de debugging después
