---
name: Analizar Existente
description: Usa esta skill cuando necesites entender un código o flujo existente antes de modificarlo. Mapea el sistema actual, identifica riesgos y reglas de negocio implícitas.
---

# Analizar Existente

## ¿Cuándo usar esta skill?

Úsala cuando:
- Vas a modificar un código que ya existe
- Necesitas agregar una funcionalidad a un sistema en producción
- Te piden "arreglar", "mejorar" o "cambiar" algo que ya funciona
- No conoces la base de código o lleva tiempo sin modificarse

**No la uses** cuando estés empezando un proyecto desde cero (usa `/desde-cero` en ese caso).

## Filosofía

Nunca modifiques código sin entenderlo primero. El código existente tiene contexto, restricciones y razones que no siempre están documentadas. Cambiar sin investigar es la forma más rápida de romper algo en producción.

## Qué hace esta skill

Te guía para:
1. **Investigar antes de preguntar**: lee el código, sigue el flujo, encuentra los puntos de entrada
2. **Hacer las preguntas correctas**: qué no debe romperse, quién lo usa, qué partes son legacy, cómo se prueba
3. **Producir un diagnóstico escrito**: estado actual, dependencias, áreas de riesgo, reglas de negocio implícitas
4. **Listar dudas abiertas**: lo que no puedes saber sin preguntarle a alguien

## Proceso paso a paso

### 1. Mapeo inicial (sin preguntar al usuario todavía)

Antes de hacer cualquier pregunta, investiga:

**Puntos de entrada:**
- ¿Qué archivo o función inicia el flujo que vas a modificar?
- ¿Cómo se llama (HTTP, evento, comando, interfaz)?
- ¿Hay rutas, controladores o handlers involucrados?

**Flujo de datos:**
- ¿De dónde vienen los datos (base de datos, API, archivo)?
- ¿Qué transformaciones sufren?
- ¿A dónde van (pantalla, archivo, otra API)?

**Dependencias:**
- ¿Qué otros módulos o servicios usa este código?
- ¿Qué otros módulos dependen de este código?
- ¿Hay librerías de terceros críticas?

**Estado actual:**
- ¿Cuándo se modificó por última vez (`git log`)?
- ¿Hay comentarios o TODOs relevantes?
- ¿Hay tests para este flujo?

**Áreas de riesgo:**
- ¿Qué partes parecen frágiles o complejas?
- ¿Hay lógica condicional enredada?
- ¿Hay manejo de errores o está ausente?

Usa herramientas de búsqueda (`grep`, `find`, `git log`, `git blame`) para mapear el código. Lee archivos relacionados. No adivines.

### 2. Preguntas al usuario (después de investigar)

Ahora que entiendes el código, pregunta lo que no puedes saber leyendo:

**Contexto de negocio:**
- ¿Qué problema resuelve este código actualmente?
- ¿Quién lo usa (usuarios finales, otros sistemas, procesos internos)?
- ¿Qué restricciones de negocio existen (regulaciones, contratos, políticas)?

**Restricciones técnicas:**
- ¿Qué **no debe romperse** bajo ninguna circunstancia?
- ¿Hay partes que son legacy pero siguen en uso?
- ¿Hay deploys o migraciones de datos involucradas?

**Testing y deploy:**
- ¿Cómo se prueba esto actualmente (manual, tests automatizados, staging)?
- ¿Cómo se despliega (CI/CD, manual)?
- ¿Hay rollback rápido si algo sale mal?

**Historial:**
- ¿Por qué se hizo así (decisiones pasadas que debes respetar)?
- ¿Hay intentos anteriores de cambiar esto que fallaron?

Haz las preguntas **una por una**, espera la respuesta, y ajusta las siguientes según lo que aprendas. No lances todas las preguntas de golpe.

### 3. Diagnóstico escrito

Produce un documento corto (markdown) con:

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

### 4. Dudas abiertas

Lista aparte de preguntas que no pudiste resolver:

```markdown
## Dudas abiertas

1. ¿La tabla `usuarios` tiene algún índice en `email`? (afecta performance de la búsqueda)
2. ¿El endpoint `/legacy/import` sigue en uso o podemos ignorarlo?
3. ¿Hay algún cliente externo que consuma este JSON directamente?
```

Estas dudas deben ser **específicas y accionables**, no genéricas.

## Ejemplo de uso

**Usuario dice:**
> "Necesito agregar un campo `telefono` al formulario de registro de usuarios."

**Agente (usando esta skill):**

1. **Investiga primero** (sin preguntar):
   - Busca el formulario de registro (`grep -r "registro" app/views/`)
   - Encuentra el controlador que procesa el registro
   - Revisa el modelo `Usuario` y su validación
   - Busca dónde se guarda en la base de datos
   - Revisa si hay tests del registro
   - Verifica si hay APIs que también crean usuarios

2. **Pregunta lo que necesita saber**:
   - "¿El teléfono es obligatorio u opcional?"
   - "¿Hay que validar el formato (solo números, con código de país)?"
   - "¿Hay otros lugares donde se crean usuarios que también necesiten el campo?"
   - "¿La columna ya existe en la base de datos o hay que crearla?"

3. **Produce diagnóstico**:
   ```markdown
   # Diagnóstico: Agregar teléfono al registro

   ## Estado actual
   - Formulario: `app/views/usuarios/registro.ctp`
   - Controlador: `UsuariosController::registrar()`
   - Modelo: `Usuario.php` con validaciones de email/password
   - Base de datos: tabla `usuarios` (columna `telefono` ya existe pero no se usa)

   ## Flujo actual
   1. Usuario llena formulario (nombre, email, password)
   2. POST a `/usuarios/registrar`
   3. Validación en modelo
   4. INSERT en tabla `usuarios`
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

4. **Lista dudas**:
   ```markdown
   ## Dudas abiertas
   1. ¿El teléfono debe ser único en la base de datos?
   2. ¿Hay que agregar el teléfono al email de bienvenida?
   ```

## Entregables

Al terminar de usar esta skill, debes tener:

1. ✅ Un **diagnóstico escrito** con el estado actual, flujo, dependencias y riesgos
2. ✅ Una **lista de dudas** específicas para el usuario
3. ✅ **Próximos pasos** claros y ordenados

**No escribas código todavía.** El objetivo es entender primero.

## Transición a las siguientes skills

Después de este diagnóstico:
- Si el trabajo es grande → usa `/reparto` para dividirlo en tareas paralelas
- Si es pequeño → implementa directamente, pero respetando el diagnóstico

## Notas importantes

- **Investiga antes de preguntar**: el usuario aprecia que ya conozcas el código
- **Sé específico en tus preguntas**: "¿Qué hace X?" es vago, "¿La tabla Y tiene índice en Z?" es útil
- **No asumas**: si no estás seguro de algo, pregunta o investiga más
- **Respeta las decisiones pasadas**: el código "raro" a veces tiene razones válidas
