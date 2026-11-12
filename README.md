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

## Skills disponibles (11 total)

> 📖 **[Ver referencia completa de comandos](docs/comandos.md)** con ejemplos de uso, flujos típicos e instalación en Cursor.

### 📋 Planificación (antes del código)

#### [`/analizar-existente`](skills/planificacion/analizar-existente/SKILL.md)
Para entender código o flujos existentes antes de modificarlos. Mapea el sistema actual, identifica riesgos y reglas de negocio implícitas.

**Úsala cuando:** vas a modificar código que ya existe.

#### [`/desde-cero`](skills/planificacion/desde-cero/SKILL.md)
Para proyectos nuevos desde cero. Hace las preguntas correctas antes de escribir código y produce una especificación mínima.

**Úsala cuando:** comienzas un proyecto completamente nuevo.

#### [`/extraer-reglas`](skills/planificacion/extraer-reglas/SKILL.md)
Para extraer reglas de negocio de servicios legacy durante migraciones. El código legacy es la fuente de verdad para las reglas, no para el diseño.

**Úsala cuando:** migras un servicio legacy a una nueva arquitectura y necesitas documentar todas sus reglas de negocio.

---

### 🔀 Trabajo Paralelo

#### [`/reparto`](skills/trabajo-paralelo/reparto/SKILL.md)
Divide un proyecto en tareas independientes que pueden ejecutarse en paralelo por distintos agentes.

**Úsala cuando:** el trabajo es demasiado grande para un solo agente y puede paralelizarse.

#### [`/contrato`](skills/trabajo-paralelo/contrato/SKILL.md)
Define el contrato exacto (entradas, salidas, errores) de cada punto de unión antes de crear worktrees.

**Úsala cuando:** después de `/reparto` y antes de `/worktrees`, necesitas especificar cómo se comunican las partes.

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

### ✅ Calidad

#### [`/tests-primero`](skills/calidad/tests-primero/SKILL.md)
Define ejemplos de entrada/salida, escribe tests que fallan, y luego implementa lo mínimo para que pasen.

**Úsala cuando:** vas a implementar código en tu worktree y quieres asegurar que funciona correctamente.

#### [`/revision-independiente`](skills/calidad/revision-independiente/SKILL.md)
Revisa el código con ojos frescos en un chat nuevo con modelo diferente, como si fueras el único revisor.

**Úsala cuando:** terminaste tu implementación y quieres una revisión objetiva antes de mergear.

---

## Orden recomendado

### Para código existente:
1. [`/analizar-existente`](skills/planificacion/analizar-existente/SKILL.md) → diagnóstico del código actual
2. [`/reparto`](skills/trabajo-paralelo/reparto/SKILL.md) → dividir en tareas
3. [`/contrato`](skills/trabajo-paralelo/contrato/SKILL.md) → definir contratos entre partes
4. [`/worktrees`](skills/trabajo-paralelo/worktrees/SKILL.md) → crear espacios de trabajo
5. [`/archivos-compartidos`](skills/trabajo-paralelo/archivos-compartidos/SKILL.md) → gestionar overlaps
6. En cada worktree: [`/tests-primero`](skills/calidad/tests-primero/SKILL.md) → implementar con tests
7. [`/revision-independiente`](skills/calidad/revision-independiente/SKILL.md) → revisar cada tarea
8. [`/antes-del-merge`](skills/trabajo-paralelo/antes-del-merge/SKILL.md) → verificar cada tarea
9. [`/integrar`](skills/trabajo-paralelo/integrar/SKILL.md) → mergear todo

### Para proyectos nuevos:
1. [`/desde-cero`](skills/planificacion/desde-cero/SKILL.md) → especificación mínima
2. [`/reparto`](skills/trabajo-paralelo/reparto/SKILL.md) → dividir en tareas
3. [`/contrato`](skills/trabajo-paralelo/contrato/SKILL.md) → definir contratos entre partes
4. [`/worktrees`](skills/trabajo-paralelo/worktrees/SKILL.md) → crear espacios de trabajo
5. [`/archivos-compartidos`](skills/trabajo-paralelo/archivos-compartidos/SKILL.md) → gestionar overlaps
6. En cada worktree: [`/tests-primero`](skills/calidad/tests-primero/SKILL.md) → implementar con tests
7. [`/revision-independiente`](skills/calidad/revision-independiente/SKILL.md) → revisar cada tarea
8. [`/antes-del-merge`](skills/trabajo-paralelo/antes-del-merge/SKILL.md) → verificar cada tarea
9. [`/integrar`](skills/trabajo-paralelo/integrar/SKILL.md) → mergear todo

### Para migraciones de legacy:
1. [`/extraer-reglas`](skills/planificacion/extraer-reglas/SKILL.md) → documentar reglas del legacy en `reglas.md`
2. Revisar reglas con experto de dominio → resolver dudas
3. [`/desde-cero`](skills/planificacion/desde-cero/SKILL.md) con `@reglas.md` → especificar nuevo servicio
4. [`/reparto`](skills/trabajo-paralelo/reparto/SKILL.md) → dividir implementación
5. [`/contrato`](skills/trabajo-paralelo/contrato/SKILL.md) → definir contratos
6. [`/worktrees`](skills/trabajo-paralelo/worktrees/SKILL.md) → espacios de trabajo
7. [`/archivos-compartidos`](skills/trabajo-paralelo/archivos-compartidos/SKILL.md) → gestionar overlaps
8. En cada worktree: [`/tests-primero`](skills/calidad/tests-primero/SKILL.md) con tests por cada regla
9. [`/revision-independiente`](skills/calidad/revision-independiente/SKILL.md) → revisar cada tarea
10. [`/antes-del-merge`](skills/trabajo-paralelo/antes-del-merge/SKILL.md) → verificar
11. [`/integrar`](skills/trabajo-paralelo/integrar/SKILL.md) → mergear todo

---

## Instalación

### En Cursor, para todos tus proyectos (recomendado)

```bash
git clone https://github.com/kandygallon/kandypedia-skills.git ~/.cursor/skills/kandypedia
```

Cursor busca skills en `~/.cursor/skills/` y entra en las subcarpetas, así que las 11 aparecen solas.
Escribe `/` en el chat del agente para verlas. Si no aparecen, reinicia Cursor.

Para actualizarlas:

```bash
git -C ~/.cursor/skills/kandypedia pull
```

### Solo en un proyecto

```bash
git clone https://github.com/kandygallon/kandypedia-skills.git /tmp/kandypedia-skills
mkdir -p .cursor/skills
cp -R /tmp/kandypedia-skills/skills/*/* .cursor/skills/
```

### Una sola skill

Copia la carpeta de esa skill (la que contiene el `SKILL.md`) dentro de `~/.cursor/skills/`.

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
├── docs/
│   └── comandos.md              # Referencia completa de comandos
└── skills/
    ├── planificacion/
    │   ├── analizar-existente/
    │   │   └── SKILL.md
    │   ├── desde-cero/
    │   │   └── SKILL.md
    │   └── extraer-reglas/
    │       └── SKILL.md
    ├── trabajo-paralelo/
    │   ├── reparto/
    │   │   └── SKILL.md
    │   ├── contrato/
    │   │   └── SKILL.md
    │   ├── worktrees/
    │   │   └── SKILL.md
    │   ├── archivos-compartidos/
    │   │   └── SKILL.md
    │   ├── antes-del-merge/
    │   │   └── SKILL.md
    │   └── integrar/
    │       └── SKILL.md
    └── calidad/
        ├── tests-primero/
        │   └── SKILL.md
        └── revision-independiente/
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
