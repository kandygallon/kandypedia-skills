# Tipos Comunes de Archivos Compartidos

## 1. Configuración de rutas

**Ejemplos:** `routes.js`, `router.ts`, `urls.py`, `App.jsx`

**Por qué es compartido:** Cada tarea agrega sus rutas

**Solución:** Owner: tarea setup. Otros: REQUESTS.md

## 2. Configuración global

**Ejemplos:** `config.js`, `settings.py`, `.env`, `package.json`

**Por qué es compartido:** Cada tarea puede agregar dependencias o configuración

**Solución:** Owner: setup inicial. Otros: documentan qué necesitan

## 3. Estilos globales

**Ejemplos:** `global.css`, `variables.scss`, `theme.js`, `tailwind.config.js`

**Por qué es compartido:** Cada tarea puede agregar variables

**Solución:** Owner: UI base. Otros: usan existentes o piden nuevas

## 4. Types compartidos

**Ejemplos:** `types.ts`, `models.ts`, `interfaces.py`

**Por qué es compartido:** Varias partes necesitan los mismos tipos

**Solución:** Owner: tarea de modelos. Otros: solo lectura

## 5. Tests de integración

**Ejemplos:** `integration.test.js`, `e2e.spec.ts`

**Por qué es compartido:** Cada tarea puede agregar tests end-to-end

**Solución:** Owner: tarea de testing. Otros: documentan qué tests faltan

## Estrategias por tipo

| Tipo | Estrategia |
|------|-----------|
| Rutas | Owner único + REQUESTS.md |
| package.json | Owner único + REQUESTS.md |
| Estilos globales | Owner único + REQUESTS.md |
| Types | Owner único + solo lectura |
| Config global | Owner único + REQUESTS.md |
| Tests e2e | Owner único + merge al final |
