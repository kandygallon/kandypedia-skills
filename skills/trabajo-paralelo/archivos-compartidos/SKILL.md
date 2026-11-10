---
name: archivos-compartidos
description: Usa esta skill para gestionar archivos que varios agentes necesitan modificar, evitando conflictos y sobrescrituras.
---

# Archivos Compartidos

## ¿Cuándo usar esta skill?

Úsala cuando dos o más tareas necesitan modificar el mismo archivo.

**No la uses** cuando cada tarea tiene archivos completamente independientes.

## Filosofía

Los **archivos compartidos** son el mayor riesgo en trabajo paralelo. Si dos agentes modifican el mismo archivo, uno sobrescribe al otro o hay conflicto de git.

Reglas para minimizar riesgo:
- **Owner claro**: un solo agente es dueño
- **Protocolo de cambios**: otros piden, no hacen directamente
- **Integración temprana**: mergear owners primero

## Tipos comunes

Ver [ejemplos-tipos.md](ejemplos-tipos.md) para:
- Configuración de rutas (routes.js, router.ts)
- Configuración global (config.js, package.json, .env)
- Estilos globales (global.css, variables.scss)
- Types compartidos (types.ts, interfaces.py)
- Tests de integración

## Protocolo: Owner único (recomendado)

### 1. Asigna owner en el plan de reparto

```markdown
## Archivos Compartidos
- `src/routes.js` → Owner: Tarea 1 (setup)
- `package.json` → Owner: Tarea 1 (setup)
```

### 2. Tareas no-owner documentan necesidades

Crea `REQUESTS.md` en tu branch:

```markdown
# Cambios en archivos compartidos

## src/routes.js
Agregar ruta:
\`\`\`js
<Route path="/products" component={ProductsPage} />
\`\`\`

## package.json
Agregar: "axios": "^1.6.0"
```

Ver [plantilla-requests.md](plantilla-requests.md).

### 3. Owner aplica cambios antes de mergear

Owner lee `REQUESTS.md` de otras branches y aplica cambios en su branch.

### 4. Integrar: Owner primero

Branch del owner se mergea primero. Otras no tienen conflictos.

## Alternativas

Ver [estrategias.md](estrategias.md) para:
- Secciones delimitadas
- Merge temprano y rebase

## Entregables

Al terminar:

1. ✅ Lista de archivos compartidos con owner
2. ✅ Protocolo claro
3. ✅ Archivo `REQUESTS.md` en branches no-owner
4. ✅ Orden de merge priorizando owners

## Transición

- Owners revisan `REQUESTS.md` y aplican cambios
- Usa `/integrar` mergeando owners primero

## Relación con otras skills

- **Después de `/reparto`**: identifica archivos compartidos
- **Antes de `/worktrees`**: asigna owners
- **Referenciado en `/integrar`**: mergea owners primero

## Referencias

- [ejemplos-tipos.md](ejemplos-tipos.md): Tipos comunes de archivos compartidos
- [plantilla-requests.md](plantilla-requests.md): Formato para pedir cambios
- [estrategias.md](estrategias.md): Alternativas al owner único

## Notas

- **Menos archivos compartidos = mejor**
- **Owner no es monopolio**: solo coordina
- **Documenta en REQUESTS.md**: evita malentendidos
- **Mergear owner primero**: evita conflictos
