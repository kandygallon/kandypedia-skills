# Plantilla: REQUESTS.md

Cada tarea no-owner crea este archivo en su branch:

```markdown
# Solicitudes de cambios en archivos compartidos

## [nombre del archivo]

**Razón:**
[Por qué necesitas este cambio]

**Cambio solicitado:**
\`\`\`[lenguaje]
[código a agregar o modificar]
\`\`\`

**Owner:**
[Tarea X]

---

## [otro archivo si aplica]

**Razón:**
[...]

**Cambio:**
\`\`\`
[...]
\`\`\`
```

## Ejemplo completo

```markdown
# Solicitudes de cambios en archivos compartidos

## src/routes.js

**Razón:**
Necesito agregar la ruta de productos para que la UI pueda navegar.

**Cambio solicitado:**
\`\`\`js
import ProductList from './pages/ProductList';

<Route path="/products" component={ProductList} />
\`\`\`

**Owner:**
Tarea 1 (setup)

---

## package.json

**Razón:**
Necesito axios para hacer llamadas HTTP a la API.

**Cambio solicitado:**
\`\`\`json
{
  "dependencies": {
    "axios": "^1.6.0"
  }
}
\`\`\`

**Owner:**
Tarea 1 (setup)
```
