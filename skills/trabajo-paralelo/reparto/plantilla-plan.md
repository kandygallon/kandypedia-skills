# Plantilla: Plan de Reparto

```markdown
# Plan de Reparto: [nombre del proyecto]

## Resumen
[Breve descripción de cómo se dividió y por qué]

## Tareas en Paralelo

### Ronda 1 (sin dependencias)

#### Tarea 1: [Nombre]
- **Objetivo**: [Qué produce]
- **Archivos que posee**:
  - `path/to/file1.ext`
  - `path/to/file2.ext`
- **Archivos que lee**:
  - `path/to/shared-config.ext`
- **Dependencias**: Ninguna
- **Entregable**: [Qué debe funcionar]
- **Verificación**: [Cómo probar]

#### Tarea 2: [Nombre]
[...mismo formato...]

### Ronda 2 (dependen de Ronda 1)

#### Tarea 3: [Nombre]
- **Objetivo**: [Qué produce]
- **Archivos que posee**:
  - `path/to/file3.ext`
- **Archivos que lee**:
  - `path/to/file1.ext` (de Tarea 1)
- **Dependencias**: Tarea 1 debe estar completa
- **Entregable**: [Qué debe funcionar]
- **Verificación**: [Cómo probar]

## Archivos Compartidos

- `config/routes.js` → Owner: [tarea]
- `styles/global.css` → Owner: [tarea]

## Integración Final

1. Tarea 1 y Tarea 2 (sin conflictos esperados)
2. Tarea 3 (puede tener conflictos, resolver manual)
```
