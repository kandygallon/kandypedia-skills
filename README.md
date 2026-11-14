# kandypedia-skills

**Skills en español para trabajar con agentes de IA**: planificar antes de programar, repartir el trabajo entre
varios agentes en paralelo sin que se pisen, revisar antes de juntar y cerrar cada sesión sabiendo qué quedó
pendiente.

Funcionan en **Cursor**, **Claude Code** y **Codex**, porque siguen el estándar abierto
[Agent Skills](https://agentskills.io).

## Por qué existen

Salen de trabajo real: del rediseño de [kandypedia.com](https://kandypedia.com) con cuatro agentes en paralelo y de
lo que me fue saliendo mal. Varias empiezan contando **de dónde salen**: lo que me pasó y me obligó a escribirla.

La idea de fondo: **antes de que un agente escriba una línea, decide qué se construye, quién toca cada archivo y
cómo se sabe que terminó.**

## Las skills

| Skill | Para qué | Produce |
|---|---|---|
| **Planificar** | | |
| [`desde-cero`](skills/desde-cero/SKILL.md) | Proyecto nuevo: las preguntas antes de programar | `docs/especificacion.md` |
| [`analizar-existente`](skills/analizar-existente/SKILL.md) | Entender código que ya existe antes de tocarlo | `docs/diagnostico.md` |
| [`extraer-reglas`](skills/extraer-reglas/SKILL.md) | Sacar las reglas de negocio de un sistema viejo antes de migrarlo | `docs/reglas.md` |
| **Trabajar en paralelo** | | |
| [`reparto`](skills/reparto/SKILL.md) | Dividir el trabajo en tareas que no tocan los mismos archivos | `docs/reparto.md` |
| [`contrato`](skills/contrato/SKILL.md) | Fijar cómo se comunican las partes antes de separarlas | `docs/contrato.md` |
| [`worktrees`](skills/worktrees/SKILL.md) | Una copia del repo por agente, lista para trabajar | Un worktree y una rama por tarea |
| [`archivos-compartidos`](skills/archivos-compartidos/SKILL.md) | Qué hacer cuando dos tareas necesitan el mismo archivo | Un dueño por archivo |
| [`antes-del-merge`](skills/antes-del-merge/SKILL.md) | La revisión de cada tarea antes de juntarla | Una rama lista para integrar |
| [`integrar`](skills/integrar/SKILL.md) | Juntar todas las ramas sin tocar `main` hasta que apruebes | La rama `integracion` y un reporte |
| **Calidad** | | |
| [`tests-primero`](skills/tests-primero/SKILL.md) | Ejemplos y tests antes que el código | Tests y el código mínimo |
| [`revision-independiente`](skills/revision-independiente/SKILL.md) | Una revisión con ojos frescos, en un chat nuevo | Hallazgos y una decisión |
| **Cada día** | | |
| [`bitacora`](skills/bitacora/SKILL.md) | Cerrar la sesión: qué se hizo, qué queda y qué sigue mañana | `docs/bitacora.md` y `docs/pendientes.md` |

Las que dirigen el trabajo (todas menos `archivos-compartidos`, `antes-del-merge` y `tests-primero`) **solo se
activan cuando tú las llamas**. Las otras tres el agente puede usarlas solo cuando vienen al caso.

## En qué orden

**Proyecto nuevo:** `desde-cero` → `reparto` → `contrato` → `worktrees` → en cada worktree, `tests-primero` →
`revision-independiente` → `antes-del-merge` → `integrar`.

**Código que ya existe:** igual, pero empieza con `analizar-existente`.

**Migración de un sistema viejo:** `extraer-reglas` → revisa las dudas con quien conoce el negocio →
`desde-cero` con `docs/reglas.md` → y sigue igual.

**Al final de cada día:** `bitacora`.

En cada punto donde decides tú (el alcance, quién es dueño de qué, el orden de integración, si algo llega a
`main`), la skill **se detiene y te pregunta**.

**Si tu equipo no commitea directo en `main`** (trabaja con pull requests o tiene `main` protegida): cuando una
skill diga «commitea en `main`», usa la rama de la que vas a crear los worktrees y lleva los cambios a `main` con
un pull request, como siempre.

## Instalación

Primero, descarga el repositorio una vez:

```bash
git clone https://github.com/kandygallon/kandypedia-skills.git ~/kandypedia-skills
```

Después, cópialas a la carpeta de tu herramienta:

### Claude Code

```bash
mkdir -p ~/.claude/skills && cp -R ~/kandypedia-skills/skills/* ~/.claude/skills/
```

Se usan con `/nombre`, por ejemplo `/reparto`.

### Codex

```bash
mkdir -p ~/.agents/skills && cp -R ~/kandypedia-skills/skills/* ~/.agents/skills/
```

Se usan con `$nombre`, por ejemplo `$reparto`, o eligiéndolas con `/skills`.

### Cursor

Cursor lee las skills de `~/.cursor/skills/`, y **también** de `~/.agents/skills/` y `~/.claude/skills/`.
**Si ya las instalaste para Claude Code o Codex, Cursor ya las ve:** no las instales otra vez, o te saldrán
repetidas. Si solo usas Cursor:

```bash
mkdir -p ~/.cursor/skills && cp -R ~/kandypedia-skills/skills/* ~/.cursor/skills/
```

Se usan con `/nombre`, por ejemplo `/reparto`.

### Solo en un proyecto

Copia `skills/*` dentro del proyecto en vez de en tu carpeta personal: `.claude/skills/` (Claude Code),
`.agents/skills/` (Codex y Cursor) o `.cursor/skills/` (Cursor). Si las commiteas, tu equipo las tiene también.

### Para actualizarlas

```bash
cd ~/kandypedia-skills && git pull
```

Y vuelve a copiarlas con el mismo comando de tu herramienta.

## Cómo está hecha cada skill

Cada una es una carpeta con un `SKILL.md` corto (máximo 120 líneas), que es lo único que el agente lee al
usarla. Los ejemplos largos están aparte, en `references/ejemplo.md`, y el agente solo los abre si los
necesita. El formato completo está en [docs/formato.md](docs/formato.md).

## Inspiración

Inspirado en [Matt Pocock](https://github.com/mattpocock/skills) y su forma de entender las skills: pequeñas,
fáciles de adaptar y que se pueden combinar.

## Licencia

[MIT](LICENSE): úsalas, cámbialas y compártelas.

## Hecho por Kandy Gallón

👩‍💻 [kandypedia.com](https://kandypedia.com) · Programo desde 2006 y cuento lo que construyo con agentes,
errores incluidos.
