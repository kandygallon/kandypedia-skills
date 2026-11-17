# Ejemplo: la bitácora después de tres días

## `docs/bitacora.md`

```markdown
# Bitácora

## 2026-11-12 · jueves

**Hecho**
- Página de precios montada en la rama `precios`. Se ve bien en móvil.
- Formulario de contacto arreglado: la API rechazaba el origen `precios`.

**Pendiente**
- Decidir si el precio lleva o no impuestos incluidos.
- Probar un pago real de prueba.

**Mañana:** el pago de prueba.

## 2026-11-13 · viernes

**Hecho**
- Pago de prueba de punta a punta: reservar, pagar y reprogramar funcionan.
- El reembolso no es automático: anotado cómo hacerlo a mano.

**Pendiente**
- Decidir si el precio lleva o no impuestos incluidos.
- Subir la rama `precios` a main (el martes).

**Mañana:** empezar el proyecto nuevo; lo de precios es entre semana.

## 2026-11-14 · sábado

**Hecho**
- Proyecto creado y abierto en el teléfono.
- Repositorio privado en GitHub.

**Pendiente**
- Pantalla principal con un solo agente.
- Decidir si el precio lleva o no impuestos incluidos.

**Mañana:** la pantalla principal.
```

## `docs/pendientes.md`

```markdown
# Pendientes

- [ ] Pantalla principal con un solo agente
- [ ] Subir la rama `precios` a main (martes)
- [ ] Decidir si el precio lleva o no impuestos incluidos  ← lleva 3 días: ¿sigue en pie?
```

## Si el proyecto ya tiene bitácora

El paso 1 encuentra `BITACORA.md`, una tabla con una fila por día. La entrada nueva es otra fila **con sus
columnas**, no una sección `## 2026-11-14`:

```markdown
| # | Día | ✓ | Hecho | Mañana |
|---|---|---|---|---|
| 73 | lun 16 nov | ✅ | Video revisado y subido como no listado. Página del regalo lista en su rama | Subir la rama a main |
| 74 | mar 17 nov | ✅ | Todo en producción, comprobado. Artículo publicado con el enlace del video | Publicar el video |
```

No se crea `docs/bitacora.md` al lado: habría dos bitácoras, y cada día una quedaría a medias.

## Lo que muestra el ejemplo

- **Lo hecho es comprobable.** «Pago de prueba de punta a punta», no «avancé con los pagos».
- **«Mañana» es una sola cosa.** La primera que hay que hacer, no la lista entera.
- **Un pendiente que se arrastra varios días se señala**, para decidir si sigue en pie o se suelta.
- **La bitácora que ya existe manda:** se escribe en ella y con su formato.
