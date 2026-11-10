# Estrategias de Merge

## Estrategia 1: Merge commits (recomendado)

```bash
git merge task-1-setup
```

**Pros:**
- Mantiene historial completo
- Fácil ver qué branch aportó qué

**Contras:**
- Historial más complejo visualmente

## Estrategia 2: Squash merge

```bash
git merge --squash task-1-setup
git commit -m "feat: integra setup (task-1)"
```

**Pros:**
- Historial más limpio (un commit por tarea)

**Contras:**
- Pierdes detalle de commits individuales

## Estrategia 3: Rebase (avanzado)

```bash
git rebase task-1-setup
```

**Pros:**
- Historial lineal (sin merges)

**Contras:**
- Más propenso a conflictos
- Reescribe historia (no usar en branches compartidas)

**Recomendación:** Usa merge commits simple para integración de tareas paralelas.
