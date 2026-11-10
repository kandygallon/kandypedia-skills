---
name: reparto
description: Usa esta skill para dividir un proyecto o especificación en tareas independientes que pueden ejecutarse en paralelo por distintos agentes.
---

# Reparto

## ¿Cuándo usar esta skill?

Úsala cuando:
- Ya tienes especificación o diagnóstico
- El trabajo es grande para un solo agente
- Hay partes que pueden hacerse en paralelo

**No la uses** cuando el proyecto es muy pequeño o todo depende de todo.

## Filosofía

El trabajo en paralelo acelera proyectos grandes, pero solo con buena división. Mala división causa conflictos, duplicación y retrabajos.

## Criterios para buena división

Una tarea paralela debe ser:
1. **Independiente**: puede empezar sin esperar otra
2. **Completa**: produce algo funcional
3. **Limitada**: alcance claro
4. **Verificable**: puedes probar que funciona

## Proceso

### 1. Analiza la especificación

Identifica:
- Funcionalidades principales
- Capas o módulos (frontend/backend, UI/lógica/datos)
- Dependencias obvias

### 2. Identifica fronteras naturales

**Por tipo:**
- Frontend vs Backend
- UI vs lógica vs datos
- Componentes vs servicios

**Por funcionalidad:**
- Autenticación vs Dashboard vs Reportes
- Crear vs Editar vs Eliminar

### 3. Define cada tarea

Para cada una especifica:
- Nombre y objetivo
- Archivos que posee (exclusivos)
- Archivos que lee (solo lectura)
- Dependencias
- Entregable verificable

Ver [plantilla-plan.md](plantilla-plan.md) con estructura completa y [ejemplos-division.md](ejemplos-division.md) con casos reales.

### 4. Asigna orden de ejecución

```
Tarea A (sin dependencias) --> paralelo inmediato
Tarea B (sin dependencias) --> paralelo inmediato
Tarea C (depende de A) --> después de A
```

## Entregables

Al terminar:

1. ✅ Plan con tareas numeradas
2. ✅ Archivos asignados sin overlap
3. ✅ Dependencias explícitas
4. ✅ Rondas de ejecución
5. ✅ Entregables verificables

## Transición

Después del reparto:
1. `/contrato` para definir contratos
2. `/worktrees` para crear espacios
3. Asignar cada tarea a un agente
4. `/archivos-compartidos` para gestionar overlaps
5. `/antes-del-merge` antes de terminar
6. `/integrar` para mergear

## Referencias

- [plantilla-plan.md](plantilla-plan.md): Estructura completa del plan de reparto
- [ejemplos-division.md](ejemplos-division.md): Casos de división por tipo de proyecto

## Notas

- **No fuerces paralelización**
- **Archivos compartidos son el riesgo mayor**
- **Una tarea debe verificarse sola**
- **Pocas tareas grandes mejor que muchas chicas**
