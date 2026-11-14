---
name: bitacora
description: Cierra la sesión de trabajo. Registra qué se hizo y qué quedó pendiente en docs/bitacora.md, pasa lo pendiente a docs/pendientes.md y deja escrito qué sigue mañana. Úsala al terminar el día o cuando digas «cerramos por hoy».
disable-model-invocation: true
---

# Bitácora

> **De dónde sale.** Desde el 1 de septiembre de 2026 cierro cada sesión igual: qué hice, qué quedó pendiente y
> qué sigue mañana, en una línea por día. Setenta días después, esa bitácora es lo que me dice dónde estoy,
> qué se me olvidaba y cuántos días seguidos llevo. Lo hacía a mano cada noche. Ahora es un comando.

## Cuándo usarla
- Al terminar una sesión de trabajo, aunque haya sido corta.
- Cuando vas a dejar el proyecto unos días y quieres retomarlo sin pensar.

**No la uses** a mitad de una tarea: primero termínala o déjala anotada como pendiente.

## Pasos

1. **Reúne lo que pasó hoy:**
   - Lo que se hizo y se decidió en esta conversación.
   - Los commits del día en el repositorio:
     ```bash
     git log --since=midnight --oneline
     ```
   - Los archivos que cambiaron y siguen sin commitear: `git status --short`.
2. **Separa en dos listas:**
   - **Hecho:** solo lo que se comprobó (hay un commit, un archivo, una prueba que pasó o algo que se vio
     funcionando). Si no se comprobó, no está hecho.
   - **Pendiente:** lo que se empezó y no terminó, lo que se decidió hacer y no se hizo, y lo que alguien tiene
     que hacer fuera de aquí.
3. Lee `docs/pendientes.md` si existe: tacha lo que hoy quedó hecho y conserva lo demás.
4. **DETENTE.** Muestra las dos listas y lo que propones para mañana. Pregunta si falta algo o si algo no fue
   así. Lo que la persona corrija manda sobre lo que tú recuerdes.
5. **Escribe en `docs/bitacora.md`** (créalo si no existe) una entrada nueva **al final**, con este formato:
   ```markdown
   ## 2026-11-14 · sábado

   **Hecho**
   - …

   **Pendiente**
   - …

   **Mañana:** una sola cosa, la primera.
   ```
   Si el archivo lleva una cuenta de días seguidos, actualízala: un día cuenta si se hizo algo, aunque sea poco.
6. **Actualiza `docs/pendientes.md`:** una lista de casillas (`- [ ]`), con lo más urgente arriba. Quita lo
   hecho. No borres pendientes viejos sin preguntar: si uno lleva semanas ahí, pregunta si sigue en pie.
7. **Commitea solo esos dos archivos**, por su nombre:
   ```bash
   git add docs/bitacora.md docs/pendientes.md
   git commit -m "Bitácora: 2026-11-14"
   ```
   No hagas push si la persona no lo pide.
8. **Responde corto:** lo hecho, lo pendiente y la primera cosa de mañana. Sin resumen de la sesión entera.

## Qué produce
- `docs/bitacora.md`: una entrada por día, la más nueva al final.
- `docs/pendientes.md`: la lista viva de lo que falta.

## Siguiente
- Mañana, al empezar: lee `docs/pendientes.md` y la última entrada de `docs/bitacora.md` antes de nada.

Si necesitas ver cómo quedan los dos archivos después de varios días, lee
[references/ejemplo.md](references/ejemplo.md).
