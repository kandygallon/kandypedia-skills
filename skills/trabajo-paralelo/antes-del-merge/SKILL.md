---
name: antes-del-merge
description: Usa esta skill antes de mergear tu branch. Verifica que tu tarea está completa, no rompiste nada, y respetaste las fronteras asignadas.
---

# Antes del Merge

## ¿Cuándo usar esta skill?

Úsala cuando terminaste tu tarea y estás listo para mergear.

**No la uses** cuando todavía estás trabajando.

## Filosofía

Mergear sin verificar rompe el proyecto. Este checklist asegura calidad antes de integrar.

## Checklist rápido

```markdown
- [ ] Mi tarea funciona (probada manual o con tests)
- [ ] Los tests pasan
- [ ] El linter no se queja de mi código
- [ ] Solo modifiqué archivos asignados
- [ ] Todos mis cambios están commiteados
- [ ] Mis commits tienen mensajes claros
- [ ] Mi branch está actualizada con main
- [ ] Documenté cambios importantes
- [ ] Limpié código temporal (console.log, TODOs)
- [ ] Hice push de mi branch
```

## Verificación detallada

Ver [checklist-detallado.md](checklist-detallado.md) con explicación de cada punto y cómo verificar.

### Puntos críticos

**1. La tarea funciona**
```bash
npm test  # o el comando de tests
# Prueba manual si no hay tests
```

**2. Tests pasan**
```bash
npm test
```
Regla: Si rompiste un test que antes pasaba, arréglalo.

**3. Linter pasa**
```bash
npm run lint
# O autofix: npm run lint -- --fix
```

**4. Respetaste fronteras**
```bash
git diff --name-only origin/main
```
Compara con tu lista de archivos asignados del `/reparto`.

**5. Todo commiteado**
```bash
git status  # Debe estar limpio
```

**6. Branch actualizada**
```bash
git fetch origin
git log HEAD..origin/main  # Si hay commits, mergea
git merge origin/main
```

## Script automatizado

Ver [script-automatizado.md](script-automatizado.md) con script bash opcional que ejecuta todas las verificaciones.

## Qué hacer si algo falla

- **Tests fallan**: identifica qué test, arréglalo
- **Linter se queja**: ejecuta autofix o corrige manual
- **Modificaste archivos incorrectos**: deshaz con `git checkout` o usa protocolo de archivos compartidos
- **Hay conflictos con main**: mergea main y resuelve

## Entregables

Al terminar:

1. ✅ Branch que pasa todos los checks
2. ✅ Commits claros y pusheados
3. ✅ Confianza de que no romperá la integración

## Transición

Después de pasar:
1. Notifica que tu tarea está lista
2. Si eres owner de archivos compartidos, revisa REQUESTS.md
3. Espera `/integrar`

## Referencias

- [checklist-detallado.md](checklist-detallado.md): Explicación completa de cada verificación
- [script-automatizado.md](script-automatizado.md): Script opcional para automatizar checks

## Notas

- **No es opcional**: si lo saltas, causarás problemas
- **No merges tú mismo** si hay coordinador: solo notifica
- **Sé honesto**: si algo no está bien, dílo
- **10 minutos aquí ahorran horas después**
