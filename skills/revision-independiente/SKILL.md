---
name: revision-independiente
description: Revisa los cambios de una rama con ojos nuevos, sin tocar el código, y da hallazgos con gravedad y una decisión de merge. Úsala al terminar una tarea con los tests en verde, en un chat nuevo distinto del que escribió el código, antes de /antes-del-merge.
disable-model-invocation: true
---

# Revisión independiente

## Cuándo usarla
- Terminaste de implementar una tarea y los tests pasan.
- Quieres una revisión objetiva antes de integrar la rama.
- Te pasan un diff o una lista de archivos para revisar contra `docs/contrato.md` o `docs/reglas.md`.

**No la uses** si la tarea sigue a medias o los tests no pasan todavía.
Tampoco para corregir: esta skill solo reporta.

## Pasos
1. Comprueba si el código que vas a revisar se escribió o se modificó en este mismo chat.

**DETENTE** si es así. Pide a la persona que abra un chat nuevo en su herramienta, idealmente con un modelo distinto
del que escribió el código, y que le pase solo:
- el diff o la lista de archivos,
- `docs/contrato.md` y `docs/reglas.md` si existen,
- esta skill.

Sin historial de implementación, sin decisiones del chat original y sin revisiones previas.
Pedir «ignora el contexto anterior» en el mismo chat no da independencia.

2. Obtén los cambios. Si no te pasaron el diff, compara la rama con `main`:

   ```bash
   git diff --stat main...HEAD
   git diff main...HEAD
   ```

3. Lee `docs/contrato.md` y `docs/reglas.md` si existen. Son la fuente de verdad, junto con el código y los tests.
   No tengas en cuenta revisiones, comentarios ni conclusiones anteriores.
4. Revisa en este orden:
   1. **Correctitud.** ¿Hace lo que pide el requisito? ¿Cumple el contrato (entradas, salidas, errores)?
      ¿Implementa todas las reglas marcadas como «Mantener» o «Cambiar»? ¿Qué casos frontera fallarían?
   2. **Errores y contrato.** ¿Los códigos de respuesta y los tipos coinciden con el contrato?
      ¿Se validan nulos y vacíos? ¿Los mensajes de error son claros?
   3. **Seguridad.** Entradas sin validar, secretos en el código, permisos sin comprobar, inyección (SQL, XSS…).
   4. **Tests.** ¿Qué comportamiento importante no está cubierto? ¿Hay test por cada regla?
      ¿Se prueban errores y casos frontera?
   5. **Legibilidad.** Solo si afecta de verdad: duplicación, funciones demasiado largas, nombres confusos.
5. Asigna a cada hallazgo una gravedad:
   - **Alta:** viola el contrato o las reglas, abre una vulnerabilidad, rompe un caso común o puede corromper datos.
   - **Media:** falta un test importante, caso frontera poco probable sin manejar, inconsistencia menor con el
     contrato, problema de rendimiento que nota quien usa la app.
   - **Baja:** legibilidad, optimización sin efecto visible, estilo (si no hay linter), comentario que falta.
6. Escribe el informe con el formato de abajo. Cita `ruta:línea` en cada hallazgo.
7. Da la decisión final. Cualquier hallazgo de gravedad alta bloquea el merge.

### Reglas
- **No cambies código.** No edites archivos ni escribas la solución: solo reporta.
- Evalúa con objetividad: ni defiendas ni ataques el código, compáralo con el contrato, las reglas y los tests.
- Que pasen el build y los tests no basta: si algo solo se puede comprobar usando la app, dilo como hallazgo.

## Qué produce
Un informe en el chat, sin cambios en el repositorio:

```markdown
## Hallazgos
1. [Gravedad: alta] ruta/archivo.ts:45: problema. Por qué importa. Sugerencia concreta.
2. [Gravedad: media] ruta/archivo.ts:12: …

## Decisión
No lo aprobaría para merge: <motivo en una línea>.
```

Si no hay hallazgos de gravedad alta: «Lo aprobaría para merge», con el motivo en una línea.

## Siguiente
- La persona o el agente que escribió el código corrige los hallazgos de gravedad alta. Los de media y baja, según
  prioridad.
- `/antes-del-merge` para la comprobación final.
- `/integrar` para llevar la rama a `main`.

Si necesitas un ejemplo completo, lee [references/ejemplo.md](references/ejemplo.md).
