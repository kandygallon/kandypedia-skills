# Ejemplos de Integración

## Ejemplo 1: App de Inventario (3 tareas)

**Contexto:**
- Tarea 1: Autenticación
- Tarea 2: API de productos
- Tarea 3: UI de productos

**Orden de merge:**
```bash
git merge task-1-auth      # Sin conflictos
git merge task-2-products  # Sin conflictos
git merge task-3-ui        # Conflicto en routes.js
```

**Resolución del conflicto:**
Ambas tareas agregaron rutas. Solución: combinar ambas en orden alfabético.

## Ejemplo 2: Con archivos compartidos

**Si usaste protocolo de REQUESTS.md:**

1. Owner ya aplicó los cambios solicitados
2. Merge del owner primero
3. Otras branches no tienen conflictos

```bash
git merge task-1-setup  # Owner, tiene todos los cambios
git merge task-2-api    # Sin conflictos (no tocó shared files)
git merge task-3-ui     # Sin conflictos
```

## Ejemplo 3: Con dependencias

**Contexto:**
- Tarea A: sin dependencias
- Tarea B: sin dependencias
- Tarea C: depende de A
- Tarea D: depende de B y C

**Orden:**
```bash
git merge task-a  # Primero
git merge task-b  # Primero (paralelo con A)
git merge task-c  # Segundo (después de A)
git merge task-d  # Último (después de B y C)
```
