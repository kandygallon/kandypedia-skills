# Plantilla de Diagnóstico

Usa esta estructura para documentar el resultado del análisis:

```markdown
# Diagnóstico: [nombre del flujo o módulo]

## Estado actual
- Qué hace el código hoy
- Puntos de entrada principales
- Tecnologías/frameworks usados

## Flujo de datos
- Entrada → procesamiento → salida
- Formato de los datos en cada paso

## Dependencias
- Servicios o módulos de los que depende
- Módulos que dependen de este código

## Áreas de riesgo
- Lógica compleja o frágil
- Falta de tests
- Dependencias externas inestables
- Partes legacy que no deben tocarse

## Reglas de negocio implícitas
- Restricciones no documentadas que descubriste en el código
- Validaciones o checks que parecen importantes

## Próximos pasos sugeridos
- Qué debe modificarse
- Qué debe quedar igual
- Orden de cambios recomendado
```

## Dudas abiertas

Lista aparte de preguntas específicas y accionables:

```markdown
## Dudas abiertas

1. ¿[Pregunta específica sobre comportamiento]?
2. ¿[Pregunta sobre restricciones técnicas]?
3. ¿[Pregunta sobre casos edge]?
```
