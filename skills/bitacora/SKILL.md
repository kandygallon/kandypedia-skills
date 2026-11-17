---
name: bitacora
description: Cierra la sesión de trabajo. Registra qué se hizo y qué quedó pendiente en la bitácora del proyecto (la que ya tengas, o docs/bitacora.md si no hay ninguna), actualiza la lista de pendientes y deja escrito qué sigue mañana. Úsala al terminar el día o cuando digas «cerramos por hoy».
disable-model-invocation: true
---

# Bitácora

> **De dónde sale.** Desde el 1 de septiembre de 2026 cierro cada sesión igual: qué hice, qué quedó pendiente y
> qué sigue mañana, en una línea por día. Setenta días después, esa bitácora es lo que me dice dónde estoy,
> qué se me olvidaba y qué sigue. Lo hacía a mano cada noche. Ahora es un comando.
>
> La primera vez que la usé en mi propio repositorio quiso crear `docs/bitacora.md`, al lado de la bitácora
> que llevo desde septiembre con otro nombre. Por eso ahora busca primero.

## Cuándo usarla
- Al terminar una sesión de trabajo, aunque haya sido corta.
- Cuando vas a dejar el proyecto unos días y quieres retomarlo sin pensar.

**No la uses** a mitad de una tarea: primero termínala o déjala anotada como pendiente.

## Pasos

1. **Busca dónde se lleva ya.** Antes de crear nada, busca una bitácora o una lista de pendientes que ya
   exista, se llame como se llame:
   ```bash
   find . -maxdepth 3 -iname '*.md' -not -path '*/node_modules/*' -not -path './.git/*' \
     -not -path '*/skills/*' | grep -i -E 'bit[aá]cora|diario|journal|devlog|pendientes|todo|plan'
   ```
   - **Si hay una, esa es la bitácora:** úsala y **sigue su formato** (una tabla, una línea por día…), no el
     de esta skill. Lo mismo con la lista de pendientes.
   - **Si hay varias candidatas o no está claro**, **DETENTE** y pregunta cuál es.
   - **Si no hay ninguna**, se crean `docs/bitacora.md` y `docs/pendientes.md`. Un `CHANGELOG` no cuenta:
     es para quien usa el proyecto, no para quien lo construye.

   En adelante, «la bitácora» y «los pendientes» son los archivos que salieron de aquí.
2. **Si la bitácora es nueva**, **DETENTE** y pregunta:
   «¿Este repositorio es solo tuyo o es compartido? ¿Commiteo la bitácora o la dejo fuera de git?»
   - **Solo suyo, o quiere compartirla:** la bitácora se commitea (paso 9).
   - **Compartido y la quiere privada:** los dos archivos se quedan en `docs/`, pero fuera de git. Añádelos al
     `info/exclude` del repositorio, que los ignora **solo en esta copia** y no cambia nada que vean los
     demás. No toques el `.gitignore` del equipo. Funciona también dentro de un worktree:
     ```bash
     printf 'docs/bitacora.md\ndocs/pendientes.md\n' >> "$(git rev-parse --git-common-dir)/info/exclude"
     ```
   Las veces siguientes no preguntes: si `git check-ignore -q docs/bitacora.md` responde que está ignorado, es
   que la persona eligió dejarla fuera de git.
3. **Reúne lo que pasó hoy:**
   - Lo que se hizo y se decidió en esta conversación.
   - Los commits del día en el repositorio:
     ```bash
     git log --since=midnight --oneline
     ```
   - Los archivos que cambiaron y siguen sin commitear: `git status --short`.
4. **Separa en dos listas:**
   - **Hecho:** solo lo que se comprobó (hay un commit, un archivo, una prueba que pasó o algo que se vio
     funcionando). Si no se comprobó, no está hecho.
   - **Pendiente:** lo que se empezó y no terminó, lo que se decidió hacer y no se hizo, y lo que alguien tiene
     que hacer fuera de aquí.
5. Lee los pendientes si existen: tacha lo que hoy quedó hecho y conserva lo demás.
6. **DETENTE.** Muestra las dos listas y lo que propones para mañana. Pregunta si falta algo o si algo no fue
   así. Lo que la persona corrija manda sobre lo que tú recuerdes.
7. **Escribe en la bitácora** una entrada nueva, donde van las demás y con su mismo formato. Si es nueva,
   va **al final** y con este:
   ```markdown
   ## 2026-11-14 · sábado

   **Hecho**
   - …

   **Pendiente**
   - …

   **Mañana:** una sola cosa, la primera.
   ```
   **Sin rachas ni cuentas de días:** la bitácora cuenta qué pasó en el proyecto, no lleva la cuenta de la
   persona. Si el archivo ya tiene una línea así, déjala como está.
8. **Actualiza los pendientes:** si son nuevos, una lista de casillas (`- [ ]`), con lo más urgente arriba. Quita lo
   hecho. No borres pendientes viejos sin preguntar: si uno lleva semanas ahí, pregunta si sigue en pie.
9. **Si la bitácora va en git**, commitea solo esos dos archivos, por su nombre (los del paso 1). Si están
   fuera de git, sáltate este paso:
   ```bash
   git add docs/bitacora.md docs/pendientes.md
   git commit -m "Bitácora: 2026-11-14"
   ```
   No hagas push si la persona no lo pide.
10. **Responde corto:** lo hecho, lo pendiente y la primera cosa de mañana. Sin resumen de la sesión entera.

## Qué produce
- Una entrada por día en la bitácora que ya tenías o, si no había, en `docs/bitacora.md`.
- La lista viva de lo que falta: la tuya o `docs/pendientes.md`.

## Siguiente
- Mañana, al empezar: lee los pendientes y la última entrada de la bitácora antes de nada.

Si necesitas ver cómo quedan los dos archivos después de varios días, lee
[references/ejemplo.md](references/ejemplo.md).
