# kandypedia-skills

**Skills para trabajar con agentes de IA en paralelo**

Una colección de skills (archivos SKILL.md) para usar con Cursor y otros agentes de IA, enfocadas en trabajo paralelo, planificación antes de código, y coordinación de múltiples agentes.

## Filosofía

Nunca empieces un proyecto escribiendo código. Primero:

1. **Entiende** (si existe código) o **planea** (si es nuevo)
2. **Divide** el trabajo en tareas independientes
3. **Ejecuta en paralelo** con múltiples agentes
4. **Integra** al final

Estos skills te guían paso a paso en ese proceso.

## Skills disponibles

### 📋 Planificación (antes del código)

#### [`/analizar-existente`](skills/planificacion/analizar-existente/SKILL.md)
Para entender código o flujos existentes antes de modificarlos. Mapea el sistema actual, identifica riesgos y reglas de negocio implícitas.

**Úsala cuando:** vas a modificar código que ya existe.

#### [`/desde-cero`](skills/planificacion/desde-cero/SKILL.md)
Para proyectos nuevos desde cero. Hace las preguntas correctas antes de escribir código y produce una especificación mínima.

**Úsala cuando:** comienzas un proyecto completamente nuevo.

---

### 🔀 Trabajo Paralelo

#### [`/reparto`](skills/trabajo-paralelo/reparto/SKILL.md)
Divide un proyecto en tareas independientes que pueden ejecutarse en paralelo por distintos agentes.

**Úsala cuando:** el trabajo es demasiado grande para un solo agente y puede paralelizarse.

#### [`/worktrees`](skills/trabajo-paralelo/worktrees/SKILL.md)
Configura worktrees de git (uno por tarea) para que varios agentes trabajen sin pisarse.

**Úsala cuando:** vas a lanzar varios agentes trabajando al mismo tiempo.

#### [`/archivos-compartidos`](skills/trabajo-paralelo/archivos-compartidos/SKILL.md)
Gestiona archivos que varios agentes necesitan modificar, evitando conflictos y sobrescrituras.

**Úsala cuando:** dos o más tareas necesitan tocar el mismo archivo.

#### [`/antes-del-merge`](skills/trabajo-paralelo/antes-del-merge/SKILL.md)
Checklist de calidad antes de mergear tu branch: verifica que tu tarea está completa, no rompiste nada, y respetaste las fronteras asignadas.

**Úsala cuando:** terminaste tu tarea y estás listo para mergear.

#### [`/integrar`](skills/trabajo-paralelo/integrar/SKILL.md)
Mergea todas las branches paralelas en el orden correcto, resuelve conflictos y verifica que el resultado funciona end-to-end.

**Úsala cuando:** todas las tareas paralelas terminaron y necesitas integrarlas.

---

## Orden recomendado

### Para código existente:
1. [`/analizar-existente`](skills/planificacion/analizar-existente/SKILL.md) → diagnóstico del código actual
2. [`/reparto`](skills/trabajo-paralelo/reparto/SKILL.md) → dividir en tareas
3. [`/worktrees`](skills/trabajo-paralelo/worktrees/SKILL.md) → crear espacios de trabajo
4. [`/archivos-compartidos`](skills/trabajo-paralelo/archivos-compartidos/SKILL.md) → gestionar overlaps
5. Trabajar en paralelo (cada agente en su worktree)
6. [`/antes-del-merge`](skills/trabajo-paralelo/antes-del-merge/SKILL.md) → verificar cada tarea
7. [`/integrar`](skills/trabajo-paralelo/integrar/SKILL.md) → mergear todo

### Para proyectos nuevos:
1. [`/desde-cero`](skills/planificacion/desde-cero/SKILL.md) → especificación mínima
2. [`/reparto`](skills/trabajo-paralelo/reparto/SKILL.md) → dividir en tareas
3. [`/worktrees`](skills/trabajo-paralelo/worktrees/SKILL.md) → crear espacios de trabajo
4. [`/archivos-compartidos`](skills/trabajo-paralelo/archivos-compartidos/SKILL.md) → gestionar overlaps
5. Trabajar en paralelo (cada agente en su worktree)
6. [`/antes-del-merge`](skills/trabajo-paralelo/antes-del-merge/SKILL.md) → verificar cada tarea
7. [`/integrar`](skills/trabajo-paralelo/integrar/SKILL.md) → mergear todo

---

## Instalación

### Opción 1: Clonar el repositorio (recomendado)

```bash
git clone https://github.com/kandygallon/kandypedia-skills.git ~/.cursor/kandypedia-skills
```

Luego, en Cursor:
1. Ve a **Settings** → **Cursor Settings** → **Features**
2. En **Skill directories**, agrega: `~/.cursor/kandypedia-skills/skills`

### Opción 2: Descargar skills individuales

Descarga los archivos `.md` que necesites y colócalos en tu directorio de skills de Cursor.

### Opción 3: Referenciar por URL

En Cursor, puedes referenciar skills directamente:

```
@https://raw.githubusercontent.com/kandygallon/kandypedia-skills/main/skills/planificacion/analizar-existente/SKILL.md
```

---

## Uso

### En Cursor

Una vez instalado, puedes invocar las skills en tu chat con el agente:

```
/analizar-existente
```

O simplemente mencionando el contexto:

```
Analiza este código antes de modificarlo (usa /analizar-existente)
```

El agente leerá automáticamente la skill y seguirá sus instrucciones.

### Con otros agentes

Estos skills son archivos markdown con instrucciones claras. Puedes:
- Copiar el contenido y pegarlo como prompt
- Compartir el link al archivo en GitHub
- Incluirlos en tu sistema de prompts personalizado

---

## Estructura del repositorio

```
kandypedia-skills/
├── README.md                    # Este archivo
├── LICENSE                      # MIT License
└── skills/
    ├── planificacion/
    │   ├── analizar-existente/
    │   │   └── SKILL.md
    │   └── desde-cero/
    │       └── SKILL.md
    └── trabajo-paralelo/
        ├── reparto/
        │   └── SKILL.md
        ├── worktrees/
        │   └── SKILL.md
        ├── archivos-compartidos/
        │   └── SKILL.md
        ├── antes-del-merge/
        │   └── SKILL.md
        └── integrar/
            └── SKILL.md
```

Cada skill tiene:
- **Frontmatter YAML** con `name` y `description`
- **Sección "¿Cuándo usar?"** con triggers claros
- **Proceso paso a paso** con ejemplos
- **Entregables** esperados al terminar

---

## Contribuir

¿Tienes ideas para mejorar estas skills o agregar nuevas?

1. Haz un fork del repositorio
2. Crea una branch con tu cambio
3. Abre un Pull Request

También puedes abrir un [issue](https://github.com/kandygallon/kandypedia-skills/issues) con sugerencias.

---

## Inspiración

Este repositorio está inspirado en el trabajo de [Matt Pocock](https://github.com/mattpocock/skills) y su enfoque de skills como conocimiento reutilizable para agentes de IA.

---

## Licencia

[MIT License](LICENSE) - libre para usar, modificar y distribuir.

---

## Hecho por Kandy Gallón

👩‍💻 [kandypedia.com](https://kandypedia.com)

¿Quieres aprender más sobre trabajo en paralelo con agentes de IA, Cursor, git worktrees y cloud agents?

🎓 **[Coaching 1:1](https://kandypedia.com/coaching)** - Sesiones personalizadas para dominar estas técnicas.

---

**¿Te resultó útil?** Dale una ⭐ al repositorio y compártelo con otros desarrolladores que trabajan con agentes de IA.
