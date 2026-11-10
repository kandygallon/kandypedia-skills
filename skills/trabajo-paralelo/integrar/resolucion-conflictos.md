# Guía de Resolución de Conflictos

## Cuando hay conflicto

Git marca el archivo con:

```js
<<<<<<< HEAD
// Código que ya estaba en main
const port = 3000;
=======
// Código que viene de la branch que estás mergeando
const port = 5000;
>>>>>>> task-2-products
```

## Cómo resolver

### 1. Entiende qué hace cada versión

- `HEAD`: código actual en tu branch (main después de merges previos)
- `task-2-products`: código de la branch que estás mergeando

### 2. Decide qué quieres

- ¿Solo una versión? Elimina la otra y los marcadores
- ¿Ambas? Combínalas de forma lógica
- ¿Ninguna? Escribe algo nuevo

### 3. Edita el archivo

```js
// Resolución: usa el puerto de la config
const port = process.env.PORT || 3000;
```

### 4. Marca como resuelto

```bash
git add <archivo-resuelto>
```

### 5. Termina el merge

```bash
git commit  # Sin -m, git genera mensaje
```

### 6. Verifica que funciona

```bash
npm test
npm start
```

## Si no sabes cómo resolver

- Consulta a los autores de ambas branches
- Lee el historial: `git log --oneline --graph branch1 branch2`
- Prueba ambas versiones en worktrees temporales

## Tipos de conflictos comunes

### "both modified <archivo>"
Causa: Ambas branches modificaron las mismas líneas.
Solución: Resuelve manual eligiendo o combinando.

### "both added <archivo>"
Causa: Ambas branches crearon archivo con mismo nombre.
Solución: Si deben ser uno, combina contenido. Si son diferentes, renombra uno.

### Tests pasan individualmente pero fallan juntos
Causa: Conflicto lógico (ej: mismo puerto, variable global).
Solución: Identifica y cambia uno para que no colisione.
