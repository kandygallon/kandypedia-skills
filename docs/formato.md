# Formato de una skill

Este es el contrato que sigue cada skill del repositorio. Si añades o reescribes una, cúmplelo.

Funciona igual en **Cursor**, **Claude Code** y **Codex**, porque se ciñe al estándar abierto
[Agent Skills](https://agentskills.io/specification).

## Estructura

```
skills/<nombre>/
├── SKILL.md              # Obligatorio. Lo que el agente lee al usar la skill
└── references/
    └── ejemplo.md        # Opcional. Ejemplos largos y plantillas, solo si hacen falta
```

- **Una carpeta por skill, directamente en `skills/`.** Sin carpetas de categoría: Claude Code y Codex no las usan.
- La carpeta se llama **igual que el `name`** del frontmatter.

## Frontmatter

```yaml
---
name: reparto
description: Divide un proyecto en tareas que varios agentes pueden hacer en paralelo sin tocar los mismos archivos. Úsala cuando ya tienes la especificación y el trabajo es demasiado grande para un solo agente.
disable-model-invocation: true
---
```

- **`name`**: minúsculas, números y guiones. Igual que la carpeta.
- **`description`**: qué hace **y** cuándo usarla, con las palabras que diría alguien que la necesita. Concreta:
  el agente decide si usarla leyendo solo esto. Máximo unos 300 caracteres. **Nunca** «antes de cualquier cosa».
- **`disable-model-invocation: true`** solo en las skills que dirigen el trabajo y que debe lanzar la persona:
  `desde-cero`, `analizar-existente`, `extraer-reglas`, `reparto`, `contrato`, `worktrees`, `integrar`,
  `revision-independiente`, `bitacora`. Las demás (`archivos-compartidos`, `antes-del-merge`, `tests-primero`) el agente puede
  usarlas solo cuando vienen al caso.

## Cuerpo del `SKILL.md`

**Máximo 120 líneas.** En este orden:

```markdown
# Título

> **De dónde sale.** …   ← Solo si la skill ya lo tiene. No se inventa.

## Cuándo usarla
- 2 a 4 casos
**No la uses** si …        ← 1 o 2 casos

## Pasos
1. …                      ← Imperativo, dirigido al agente. Comandos concretos cuando hagan falta.
2. …
**DETENTE.** …            ← En cada punto donde decide la persona, no el agente.

## Qué produce
- `docs/<archivo>.md` con …

## Siguiente
- `/<skill>` para …

Si necesitas un ejemplo completo, lee [references/ejemplo.md](references/ejemplo.md).
```

## Reglas que no se rompen

1. **Cada plan va a un archivo fijo de `docs/`**: `docs/especificacion.md`, `docs/diagnostico.md`,
   `docs/reglas.md`, `docs/reparto.md`, `docs/contrato.md`. Y se commitea en `main` antes de crear worktrees.
2. **Las decisiones de negocio son de la persona.** El agente presenta y espera: `**DETENTE.**`
3. **`main` no se toca sin aprobación.** Nada de `git reset --hard`, `git rebase -i`, `git add .` ni `git add -A`.
4. **Neutral entre herramientas.** Nada que solo exista en una («Task de Cursor», «abre un chat en Cursor»).
   Escribe «abre un chat nuevo» o «tu herramienta».
5. **Que pasen el build y los tests no basta:** se abre la app y se usa.

## Estilo

- Español neutro, de tú. **«Las skills»**, en femenino. **«Rama»**, no «branch».
- Frases cortas. Sin relleno ni frases de motivación.
- Los comandos, en bloques de código.
