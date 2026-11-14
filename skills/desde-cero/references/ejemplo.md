# Ejemplo: desde cero

## Plantilla de `docs/especificacion.md`

```markdown
# Especificación: [nombre del proyecto]

## Problema
[Qué problema resuelve, en 2 o 3 frases]

## Usuario
[Quién lo usará, qué sabe, qué necesita]

## Versión mínima
- Funcionalidad 1
- Funcionalidad 2
- Funcionalidad 3

## Restricciones
- Stack: [tecnologías que hay que usar]
- Tiempo: [si hay límite]
- Dependencias: [qué se puede y qué no se puede usar]

## Fuera de alcance (para después)
- Cosa 1 que no se hará
- Cosa 2 que no se hará

## Definición de hecho
"Alguien puede [acción] y obtiene [resultado], lo que resuelve [problema]."

## Arquitectura sugerida
**Opción 1:** [descripción breve + pros y contras]
**Opción 2:** [descripción breve + pros y contras]

Recomendación: [cuál y por qué]
```

## Buenas y malas respuestas

| Pregunta | Buena | Mala |
|---|---|---|
| Problema | «Quienes trabajan con varios agentes pierden cambios porque se pisan en el mismo repositorio» | «Quiero una app» |
| Usuario | «Desarrolladores que ya programan con un agente, pero solo con uno a la vez» | «Cualquiera» |
| Versión mínima | «Un comando que crea un worktree por tarea y muestra el estado de cada uno» | «Autenticación, paneles, notificaciones, integraciones, reportes…» |
| Restricción | «Debe funcionar con cualquier repo git, sin dependencias externas» | «Debe ser perfecto» |
| Definición de hecho | «Corro `split plan.md`, obtengo 3 worktrees con sus ramas y al final integro sin conflictos» | «Que esté bien hecho» |

## Conversación completa

**Persona:** «Quiero crear una herramienta para trabajar en paralelo con agentes».

1. **Agente:** «¿Qué problema quieres resolver? ¿Qué pasa hoy cuando trabajas con agentes que te
   gustaría que fuera distinto?»
   **Persona:** «Cuando lanzo varios agentes en el mismo repo, se pisan. Uno modifica un archivo y otro
   lo sobrescribe».
2. **Agente:** «¿Quién lo va a usar? ¿Gente que ya conoce git y worktrees, o gente que nunca los usó?»
   **Persona:** «Desarrolladores que programan con agentes, saben git básico y nunca usaron worktrees».
3. **Agente:** «¿Cuál es la versión más pequeña que resuelve el problema? Si lo lanzaras mañana, ¿qué
   debe hacer?»
   **Persona:** «Crear un worktree por tarea, decirme dónde está cada uno y al final integrarlos sin
   conflictos».
4. **Agente:** «¿Hay restricciones? ¿Sistema operativo, tecnologías?»
   **Persona:** «Un script simple, sin dependencias raras. Bash o Node, lo que sea más fácil».
5. **Agente:** «¿Qué queda fuera de esta primera versión?»
   **Persona:** «Nada de interfaz gráfica, ni sincronización en tiempo real, ni nube. Solo local».
6. **Agente:** «¿Cómo sabemos que está terminado? Dame un ejemplo de uso».
   **Persona:** «Corro `parallel-work split tasks.txt` y me crea 3 worktrees con sus ramas. Trabajo en
   cada uno con un agente distinto. Al terminar, corro `parallel-work merge` y queda todo integrado en
   mi rama principal».

## Especificación resultante

```markdown
# Especificación: parallel-work

## Problema
Cuando varios agentes trabajan a la vez en el mismo repositorio git, se pisan porque modifican los
mismos archivos. Eso provoca conflictos y pérdida de trabajo.

## Usuario
Desarrolladores que programan con agentes y conocen git básico (commit, push, merge), pero no han
usado worktrees. Trabajan en proyectos medianos y quieren repartir tareas entre agentes.

## Versión mínima
- `split`: lee un archivo de tareas y crea un worktree y una rama por cada una
- `status`: muestra el estado de cada worktree (qué archivos cambió)
- `merge`: integra todos los worktrees en la rama principal y resuelve los conflictos obvios
- `clean`: elimina los worktrees y las ramas temporales

## Restricciones
- Script simple (Bash o Node.js)
- Sin dependencias externas complejas
- Solo local: ni nube ni servidores

## Fuera de alcance
- Interfaz gráfica
- Sincronización en tiempo real
- Hosting o servicios remotos
- Integración con CI/CD

## Definición de hecho
Alguien corre `parallel-work split tasks.txt` con 3 tareas. El script crea 3 worktrees en carpetas
separadas, cada uno con su rama. La persona trabaja en cada worktree con un agente distinto. Al
terminar, corre `parallel-work merge` y todos los cambios quedan integrados en su rama principal sin
resolver conflictos a mano, siempre que las tareas no hayan tocado las mismas líneas.

## Arquitectura sugerida
**Opción 1: script Bash**
- Pros: cero dependencias, funciona en cualquier Unix.
- Contras: difícil de mantener, difícil de testear.

**Opción 2: script Node.js**
- Pros: más legible, testeable y multiplataforma.
- Contras: requiere Node instalado.

Recomendación: **Node.js**. Es más fácil de mantener y de ampliar después, y quien programa con
agentes suele tener Node instalado.
```

Después, el agente presenta la especificación y espera la aprobación antes de commitearla y pasar a
`/reparto`.
