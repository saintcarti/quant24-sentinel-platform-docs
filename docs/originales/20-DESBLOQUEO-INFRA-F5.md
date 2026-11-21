# Desbloqueo de infraestructura Fase 5

## Precondiciones

Cuenta Quant24: `6fd14e...`. Ejecutar desde [ruta interna], con pnpm exec wrangler. Verificar `whoami` antes de crear cualquier recurso. La sesión local de esta ejecución no está autenticada. No usar una cuenta temporal de preview.

Recursos existentes deben leerse, no recrearse. Índices ajenos no se modifican. Antes de desplegar registrar versión actual para rollback. Ninguna promoción a master mientras falte CI exact-head y prueba real dev.

## Recursos y orden

1. `wrangler vectorize list` y get de índices existentes.
2. Crear únicamente los ausentes:
   - `wrangler vectorize create [recurso interno] --dimensions 1024 --metric cosine`
   - `wrangler vectorize create [recurso interno] --dimensions 1024 --metric cosine`
3. Para cada índice, consultar metadata indexes y crear los ausentes con `create-metadata-index <índice> --[función interna] <campo> --type <tipo>`:
   - [slot]:string, [slot]:number, [slot]:number, [slot]:string, [slot]:string, [slot]:number.
4. Leer de vuelta configuración, dimensiones, métrica y los seis índices. Nada de upserts antes de ese readback.
5. Prueba desechable $in en índice propio [recurso interno]: crear índice/metadata, insertar fixture sintético acotado, esperar visibilidad, consultar con128/200/255valores y filtros combinados menores2048bytes, verificar inclusión y exclusión, borrar solo recurso de ensayo y leer desaparición. No declarar $in aceptado solo porque HTTP respondió200.
6. Agregar bindings AI y vectorize ARRAY solo después de verificar recursos. [binding]/[binding] separados. Refuerzo dev1 solo tras verificar AI; prodapagado.
7. Para activar on-demand definir namespace [identificador interno] y migración [función interna] [tipo interno] por entorno, revisar despliegue y contratos externos primero. No basta el binding: actual runtime cierra fail-closed sin proveedores/transporte/R2/canonización seguros.

## Migraciones y prueba dev

Aplicar migraciones remotas exclusivamente [recurso interno]. Los tests locales aplicaron0001–0006 en base desechable, no en Cloudflare remota. No usar [archivo interno] remoto.

Verificar ruta aprobación con clave writeanalyst/admin, nueva noticia pending, aprobar, cron/reconciliar, vectores visibles, buscarcon filtros, recalificarscore, descartar y comprobar ausente en búsqueda y working-set. Forzar retry seguro solo confixturepropio. Verificar cleanupdurable sin ghosts.

Ejecutar [script de evaluación] con clave read local autorizada. Primera dorada necesita corpus representativo y revisión de resultados; no dorar índice vacío ni resultados ficticios.

## Credenciales

Mauricio: tokennuevo WorkersAIEdit + D1Edit + VectorizeEdit, limitado [función interna] y90días; D1Edit permite modificar base. Guardar bajo [ubicación interna] modo0600; no chat/repo/log. La autorización OAuth de Wrangler no supone capacidad de emitir tokens delegados.

No crear clave integrationwarren: producto retirado. Consumersnuevos nombrados explícitamente cuando existan.
