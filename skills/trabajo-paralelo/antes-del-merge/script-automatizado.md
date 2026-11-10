# Script Automatizado (Opcional)

Crea `pre-merge-check.sh` en tu repo:

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
  echo "⚠️  main tiene commits nuevos, considera merge/rebase"
fi

# 5. Console.logs
if grep -r "console.log" src/ > /dev/null; then
  echo "⚠️  Hay console.log en src/"
fi

echo "✅ Verificación completa"
```

**Uso:**

```bash
chmod +x pre-merge-check.sh
./pre-merge-check.sh
```

**Nota:** Ajusta comandos según tu proyecto (npm/pytest, eslint/flake8, etc.).
