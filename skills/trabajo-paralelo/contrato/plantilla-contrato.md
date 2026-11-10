# Plantilla: Contrato de Trabajo Paralelo

```markdown
# Contrato: [Nombre de la tarea]

## Objetivo
[Describe en 2 líneas qué se va a construir y para qué]

## División de trabajo

### Agente 1 (worktree: ../proyecto-task-1)
[Qué implementa este agente]

### Agente 2 (worktree: ../proyecto-task-2)
[Qué implementa este agente]

## Puntos de unión

### 1. [Nombre del punto de unión]

**Tipo**: [Endpoint HTTP / Función / Tipo TypeScript / Evento / Tabla]

**Responsable**: [Qué agente lo implementa]

**Entrada:**
\`\`\`typescript
{
  campo1: string;           // obligatorio, descripción
  campo2?: number;          // opcional, descripción
  campo3: "A" | "B" | "C";  // valores posibles
}
\`\`\`

**Salida exitosa:**
\`\`\`json
{
  "id": 123,
  "status": "created",
  "data": {
    "campo1": "valor"
  }
}
\`\`\`

**Errores:**

- **400 Bad Request**: campo obligatorio faltante
  \`\`\`json
  {
    "error": "MISSING_FIELD",
    "message": "campo1 es obligatorio",
    "field": "campo1"
  }
  \`\`\`

- **404 Not Found**: recurso no existe
  \`\`\`json
  {
    "error": "NOT_FOUND",
    "message": "Recurso no encontrado"
  }
  \`\`\`

**Ejemplo completo:**
\`\`\`bash
curl -X POST /api/recurso \
  -H "Content-Type: application/json" \
  -d '{"campo1": "test"}'

# Respuesta: 201 Created
{
  "id": 123,
  "status": "created"
}
\`\`\`

---

### 2. [Otro punto de unión]
[...mismo formato...]

## Decisiones tomadas

1. [Decisión técnica que afecta a todos]
2. [Otra decisión]

## Fuera de alcance

1. [Cosa que NO se implementará]
2. [Otra cosa fuera]

## Preguntas abiertas

1. ¿[Pregunta que el usuario debe decidir]?
2. ¿[Otra pregunta]?
```
