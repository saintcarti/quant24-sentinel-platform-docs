# Estado verificado

Corte de consulta: **20-nov-2026, alrededor de 03:15 UTC**. Los datos son una fotografía y deben volver a medirse. No representan rendimiento financiero.

## Platform

- Rama remota `main`: `5272b25e3d928217246ca677ca2d7cb7f47840ca`.
- Worker `q24-production`: versión activa `3e2c9571-e554-4134-aea0-a04ce6ad60fe` al 100%.
- Build publicado verificado: `H6cO6KPEx3_EwzGaMC2BE`.
- En el control posterior al despliegue: home y health 200; rutas `/app` y `/app/riesgo` redirigen al login; API de riesgo 401 JSON sin sesión.
- Binding a `sentinel-api` y nombres de secretos de integración/admin/Turnstile preservados.
- CI del commit publicado: verificación bloqueante aprobada; calidad del sitio falló. No se presenta como suite íntegramente verde.
- Feed con sesión y Turnstile interactivo no fueron comprobados en esa verificación.

## Sentinel dev

- Head `develop` consultado: `31e045d1f87ef7032f200c8f916a8cf40979245c`.
- Versión activa leída: `16e90b15-8642-408d-88b4-45ccae626175` al 100%. La lectura de versión por sí sola no atribuye su código a ese head.
- D1: 30 aprobados, 7 descartados; sin grupo pending en el conteo.
- Migraciones registradas: 0001 a 0007, incluida `[migración interna]`.
- Scores persistidos: 37 de reglas, 5 manuales, **1 llm**. Este último actualiza la observación anterior de cero llm; no demuestra estabilidad ni cobertura de la capa 2.
- Último working set leído: `2026-11-20T03:00:15.463Z`, 30 ítems.
- En el control anterior del E2E: 30 chunks correspondientes a 30 noticias; consulta Fed con filtro monetary_policy respondió 200 y tres citas FOMC con similitud 0.63037163.
- PR #29 fue confirmado fusionado. PR #30 estaba abierto y mergeable en la consulta anterior: no se afirma aquí fusionado.

## Sentinel producción

- Head `master` consultado: `df66ab4b4aac655032e492871c63b0b65bf04584`.
- Versión activa: `d25cf3a4-0e24-4ca1-a985-bf9bf13ebcef` al 100%; corresponde al despliegue previamente corroborado, no se supone promovido el último head.
- Consulta de corpus: cero noticias y cero scores.
- Working set horario: última fila `2026-11-20T03:00:17.144Z`, cero ítems.
- `d1_migrations` registra solo 0001, aunque existen tablas antiguas creadas por otras vías. Hay que reconciliar esquema e historial; no aplicar migraciones a ciegas.
- Refuerzo apagado y términos/contacto de ingesta vacíos en la configuración previamente leída.

## Límites de la evidencia

La dorada inicial de bancos centrales sirve como baseline de ese corpus. Los casos high vacíos no prueban recuperación positiva high; vecinos de bancos centrales para consultas de ciberseguridad no demuestran relevancia temática.

No se declara completado todo el on-demand, todos los conectores, la entrega de un token delegado ni la limpieza de credenciales de aceptación. Los fallos de adquisición de una fuente no equivalen automáticamente a fallos de inferencia.
