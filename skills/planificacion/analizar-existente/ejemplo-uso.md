# Ejemplo de Uso Completo

**Usuario dice:**
> "Necesito agregar un campo `telefono` al formulario de registro de usuarios."

**Agente (usando esta skill):**

## 1. Investiga primero (sin preguntar)

Busca el formulario de registro:
```bash
grep -r "registro" app/views/
```

Encuentra el controlador que procesa el registro, revisa el modelo `Usuario` y su validación, busca dónde se guarda en la base de datos, revisa si hay tests del registro, verifica si hay APIs que también crean usuarios.

## 2. Pregunta lo que necesita saber

- "¿El teléfono es obligatorio u opcional?"
- "¿Hay que validar el formato (solo números, con código de país)?"
- "¿Hay otros lugares donde se crean usuarios que también necesiten el campo?"
- "¿La columna ya existe en la base de datos o hay que crearla?"

## 3. Produce diagnóstico

```markdown
# Diagnóstico: Agregar teléfono al registro

## Estado actual
- Formulario: `src/components/RegistroUsuario.tsx`
- Controlador: `usuarios.controller.ts`
- Modelo: modelo Usuario con validaciones de email/password
- Base de datos: tabla usuarios (columna telefono ya existe pero no se usa)

## Flujo actual
1. Usuario llena formulario (nombre, email, password)
2. POST a `/usuarios/registrar`
3. Validación en modelo
4. INSERT en tabla usuarios
5. Email de bienvenida

## Áreas de riesgo
- El email de bienvenida usa una plantilla que menciona "solo email"
- Hay un endpoint API `/api/usuarios` que también crea usuarios (hay que modificarlo también)

## Próximos pasos
1. Agregar campo al formulario con validación
2. Actualizar modelo y validaciones
3. Modificar endpoint API
4. Actualizar plantilla de email
5. Agregar tests
```

## 4. Lista dudas

```markdown
## Dudas abiertas
1. ¿El teléfono debe ser único en la base de datos?
2. ¿Hay que agregar el teléfono al email de bienvenida?
```
