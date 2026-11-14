# Ejemplo: analizar lo existente

## Plantilla de `docs/diagnostico.md`

```markdown
# Diagnóstico: [flujo o módulo]

## Estado actual
- Qué hace el código hoy
- Puntos de entrada principales
- Tecnologías y frameworks

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
- Restricciones no documentadas encontradas en el código
- Validaciones o comprobaciones que parecen importantes

## Próximos pasos sugeridos
- Qué debe modificarse
- Qué debe quedar igual
- Orden recomendado

## Dudas abiertas
1. ¿La tabla `usuarios` tiene índice en `email`? (afecta al rendimiento de la búsqueda)
2. ¿El endpoint `/legacy/import` sigue en uso o se puede ignorar?
3. ¿Algún cliente externo consume este JSON directamente?
```

## Caso completo

**Persona:** «Necesito añadir un campo `telefono` al formulario de registro».

### 1. El agente investiga, sin preguntar todavía

- Busca el formulario de registro: `grep -rn "registro" src/`
- Encuentra el controlador que procesa el registro.
- Revisa el modelo `Usuario` y sus validaciones.
- Busca dónde se guarda en la base de datos.
- Comprueba si hay tests del registro.
- Busca otros sitios que también crean usuarios (por ejemplo, una API).

### 2. El agente pregunta, de una en una

- «¿El teléfono es obligatorio u opcional?»
- «¿Hay que validar el formato (solo números, con código de país)?»
- «¿Hay otros sitios donde se crean usuarios que también necesiten el campo?»
- «¿La columna ya existe en la base de datos o hay que crearla?»

### 3. El agente escribe el diagnóstico

```markdown
# Diagnóstico: añadir teléfono al registro

## Estado actual
- Formulario: `src/components/RegistroUsuario.tsx`
- Controlador: `usuarios.controller.ts`
- Modelo: `Usuario`, con validaciones de email y contraseña
- Base de datos: tabla `usuarios` (la columna `telefono` ya existe pero no se usa)

## Flujo de datos
1. La persona rellena el formulario (nombre, email, contraseña)
2. POST a `/usuarios/registrar`
3. Validación en el modelo
4. INSERT en la tabla `usuarios`
5. Email de bienvenida

## Áreas de riesgo
- La plantilla del email de bienvenida da por hecho que solo hay email
- El endpoint `/api/usuarios` también crea usuarios: hay que cambiarlo igual

## Próximos pasos sugeridos
1. Añadir el campo al formulario, con validación
2. Actualizar el modelo y sus validaciones
3. Modificar el endpoint de la API
4. Actualizar la plantilla del email
5. Añadir tests

## Dudas abiertas
1. ¿El teléfono debe ser único en la base de datos?
2. ¿Hay que incluir el teléfono en el email de bienvenida?
```

### 4. El agente se detiene

Presenta el diagnóstico y las dudas, espera las respuestas, las incorpora y, con la aprobación,
commitea `docs/diagnostico.md` en `main`. Solo después se escribe código.
