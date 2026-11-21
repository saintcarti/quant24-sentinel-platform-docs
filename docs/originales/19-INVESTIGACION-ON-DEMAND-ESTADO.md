# Investigación on-demand — implementación y límites

## Implementado localmente

- Repositorio D1 de trabajos, candidatos y trazas; migración aditiva 0005.
- Reservas de trabajos/URLs por clave API autenticada (no por X-On-Behalf-Of); cancelar libera concurrencia, no cuota diaria.
- [tipo interno] con alarmas, checkpoints por paso, reintentos acotados, deadline y cancelación durable.
- Rutas para crear trabajo (202), consultar progreso y cancelar, cableadas al entrypoint con namespace opcional.
- Prueba local real con workerd y las seis migraciones: creación 202, alarmas hasta cierre `completed_partial/search_config_missing`, cancelación persistida. No prueba buscador ni adquisición remotos.
- Núcleo de adquisición con URL/destino verificados, robots, denylist, redirecciones manuales, timeout y límite streaming.

## No declarar completo RF34–43

Sin un proveedor configurado se declara `search_config_missing`; sin transporte SSRF verificado se declara `ssrf_config_missing`. No se sustituye por resultados sintéticos ni se descarga con fetch sin controles.

La adquisición admite texto plano y HTML mediante linkedom y Mozilla Readability, con título, texto principal y metadata disponible. La revisión exige presupuesto de bytes/nodos/profundidad antes del DOM, deadline antes/después de parsing y normalización consistente de percent-encoding en robots; sus regresiones se incluyen en los tests. Faltan Browser Rendering como fallback, caché compartida robots con TTL y coordinador global por dominio. La verificación DNS debe cubrir rebinding en el transporte; un booleano tras resolver DNS no garantiza que fetch use esa IP.

Los callbacks de guardado R2 y normalización canónica deben ser idempotentes por candidato. No se ha conectado adquisición real a R2, ni normalización on-demand al pipeline: el normalizador actual aún fija origen scheduled; no debe reutilizarse ciegamente. El DO exige comprobar fila canónica on_demand/pending/job antes de aceptar. Lo pendiente de aprobación no entra al RAG.

La cuota implementada reserva trabajos y URLs, no estima contabilidad monetaria ni inventa consumo de tokens/neuronas. No es cierre de RF42 monetario.

## Infraestructura y pruebas

No hay despliegue on-demand ni proveedores remotos verificados. Los tests de SQLite y doubles DO prueban contrato, persistencia y recuperación. La verificación integrada actual ejecutó 512 pruebas aprobadas y 3 omitidas, typecheck de ambos paquetes y lockfile frozen. La prueba workerd local se repitió después de los cambios: salud 200, creación 202, cierre parcial por buscador ausente y cancelación persistida; no sustituye pruebas con proveedores. Sin namespace configurado, la API pública debe responder 503 explícito al crear trabajos.

Warren AI está retirado. Este módulo pertenece a Sentinel y no crea una integración para Warren. Las tablas legadas se conservan por compatibilidad.
