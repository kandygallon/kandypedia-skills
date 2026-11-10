# Formato de Ejemplos

## Estructura base

```markdown
### Ejemplo N: [Descripción] [(Referencias a reglas)]

**Entrada:**
\`\`\`json
{
  "campo": "valor"
}
\`\`\`

**Salida esperada:**
\`\`\`json
{
  "resultado": "valor"
}
\`\`\`

**O si es error:**
Error [código]: "Mensaje"
```

## Tipos de ejemplos requeridos

1. **Caso exitoso**: entrada válida, salida correcta
2. **Casos de error**: validaciones que fallan
3. **Casos frontera**: límites, nulos, vacíos

## Con referencia a reglas

Si existe `docs/reglas.md`, referencia IDs:

```markdown
### Ejemplo 1: Caso exitoso (R-001, R-010)
[Implementa reglas R-001 y R-010]
```

## Mínimo requerido

**Al menos 3 ejemplos:**
1. Caso exitoso
2. Caso de error
3. Caso frontera

Más ejemplos = mejor cobertura.
