# Formato para Documentar Reglas

Plantilla detallada para cada regla extraída del legacy.

## Estructura básica

```markdown
### R-NNN: [Nombre descriptivo de la regla]

**Descripción (lenguaje de negocio):**
[Explica QUÉ hace la regla, no cómo está implementada. Evita tecnicismos.]

**Ubicación en código legacy:**
- Archivo: `src/services/inventory/receive.service.ts`
- Función: `processReceipt()` líneas 45-67
- También en: [otros archivos si aplica]

**Entrada esperada:**
\`\`\`json
{
  "campo1": "valor",
  "campo2": 123
}
\`\`\`

**Salida esperada:**
\`\`\`json
{
  "resultado": "valor"
}
\`\`\`

**Casos especiales / Edge cases:**
- Si campo == valor: comportamiento X
- Si condición Y: error "Mensaje"

**Estado para nuevo servicio:**
- [ ] **Mantener**: implementar igual
- [ ] **Cambiar**: implementar diferente (especificar cómo)
- [ ] **Eliminar**: ya no es necesaria
- [ ] **Por confirmar**: preguntar a experto

**Notas:**
[Bugs sospechosos, decisiones históricas, workarounds]
```

## Guías de redacción

**Descripción:**
- Usa lenguaje de negocio, no código
- Explica QUÉ hace, no CÓMO lo hace
- Evita: "El método X llama a Y"
- Prefiere: "El sistema valida que la cantidad sea positiva"

**Ubicación:**
- Archivo completo con ruta relativa
- Función/método y líneas exactas
- Múltiples ubicaciones si la regla está duplicada

**Ejemplos:**
- Usa datos realistas pero genéricos
- Include casos exitosos Y de error
- Muestra entrada completa, no fragmentos

**Estado:**
- Mantener: regla vigente, implementar igual
- Cambiar: regla vigente, pero con mejoras (documenta qué cambia)
- Eliminar: regla obsoleta o incorrecta
- Por confirmar: dudas sobre vigencia o implementación
