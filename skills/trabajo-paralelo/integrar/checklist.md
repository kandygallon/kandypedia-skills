# Checklist de Integración

Verifica después de mergear todas las branches:

## Tests
- [ ] `npm test` pasa
- [ ] `npm run test:e2e` pasa (si existe)
- [ ] Tests específicos de integración pasan

## Linter
- [ ] `npm run lint` pasa sin errores
- [ ] No hay warnings críticos

## Ejecución
- [ ] `npm start` levanta la aplicación
- [ ] No hay errores en consola
- [ ] Puertos y configuración correctos

## Verificación manual
- [ ] Funcionalidad de tarea 1 funciona
- [ ] Funcionalidad de tarea 2 funciona
- [ ] Funcionalidad de tarea 3 funciona
- [ ] Las integraciones entre tareas funcionan

## Cleanup
- [ ] Worktrees eliminados
- [ ] Branches locales eliminadas
- [ ] Branches remotas eliminadas (opcional)

## Documentación
- [ ] Reporte de integración escrito
- [ ] Conflictos documentados
- [ ] Decisiones importantes registradas
