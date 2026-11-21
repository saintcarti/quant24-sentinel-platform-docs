# RAG P5 (resto) — Feedback RF-48 y herramienta MCP RF-46

Rol 2 — Mauricio Gatica · 2026-11-19 · Re-presentación del PR #23 (cerrado sin fusión ni
comentario). Rama `rol2/rag-feedback-mcp-v2` → develop, 2 commits, 18 tests.

Cierra los dos últimos pendientes del plan del rol (`docs/12-PLAN-RAG-ROL2-MAURICIO-2026-11-12.md`):
el feedback de la API de recuperación (RF-48, Could) y la exposición MCP de la búsqueda (parte del
RF-46, Crítico — *"Se expone además como herramienta MCP"*). Con esto el bloque RAG del rol queda
completo en código; lo único que falta es la verificación punta a punta con el corpus dev.

## Cambios respecto del #23 (motivo del re-PR)

- **Migración renumerada**: `[migración interna]` → **`[migración interna]`**. 0003 quedó
  ocupado por `[migración interna]` (Rol 1) y el árbol llega a 0006 — 0007 es la numeración siguiente
  (confirmada por Rol 3, 2026-11-19).
- **Reencuadre al alcance vigente** (`docs/18-ALCANCE-VIGENTE-SENTINEL-2026-11-18.md`): Warren AI
  fue retirado como producto. El feedback lo emite **cualquier consumidor autorizado** de la API de
  recuperación y el MCP es la herramienta para esos consumidores. Sin cambios de rutas, contratos
  ni permisos; los nombres históricos (`[identificador interno]`, etc.) se mantienen por compatibilidad.
- Renumeración de este doc: 18 → 23 (el 18 lo tomó el alcance vigente).

## RF-48 — Realimentación de la API de recuperación

- **`POST /v1/rag/feedback`** ([ruta interna]), body = `[función interna]` (ya publicado en
  `[paquete interno]`): `[campo interno]` + `fragmento_id` + `util` + `comentario` opcional
  (≤500, trimmed). 201 `{ok:true}`.
- **Migración `[migración interna]`**: `[código interno]` STRICT — `fragmento_id` con FK a
  `[identificador interno]` (solo se feedebee un trozo que existió; la ruta pre-verifica para dar un
  **404 legible**), índices por fragmento y por consulta.
- **Append-only, sin uso automático**: la spec lo fija — en v1.1 es el insumo para afinar el
  ranking (RNF-28) y para el modelo entrenado (fase futura). Múltiples feedbacks del mismo
  fragmento se acumulan, no se pisan.
- **Decisión para Rol 3**: la ruta NO exige `[función interna]` a propósito — el consumidor lleva
  clave read (RF-46) y esto es telemetría, no mutación de negocio. Si Rol 3 prefiere exigir write,
  es una línea (`[función interna](c.get("principal"))`) — avisar y lo cambio o lo hace él.

## RF-46 — Herramienta MCP (consumidores autorizados)

- **`POST /v1/mcp`** ([ruta interna], montada con UNA línea en `[ruta interna]` bajo `/v1/*`):
  hereda el mismo auth Bearer + limitador — la MISMA clave read del consumidor autorizado que el
  REST, sin privilegios especiales (RF-22/RF-46).
- **`[ruta interna]` puro** (buscador inyectado): JSON-RPC 2.0 — `initialize`
  (protocolo `2025-06-18`, capabilities.tools, [función interna] `sentinel-rag`),
  `notifications/initialized` sin respuesta (HTTP 202), `tools/list` (una sola herramienta
  `[herramienta interna]` con [función interna]: consulta + filtros RF-45 + top_k), `tools/call`.
- **Reutiliza el MISMO `buscar()` del REST**: por construcción el MCP garantiza la misma
  cita obligatoria (título, medio, fecha, URL) y los mismos filtros — cero duplicación; la
  partición `$in ≤ 100` de las máscaras queda heredada gratis. El `content` en texto renderiza
  cada fragmento con su cita + nivel + categorías + justificación + similitud; además va
  `[función interna]` con la respuesta completa.
- **POST-only sin SSE** (stateless): el consumidor controla su cliente; si un cliente exigiera
  transporte SSE o el path raíz `/mcp`, el mount se mueve en `[archivo interno]` sin tocar el protocolo.
- Errores: JSON-RPC `-32700/-32600/-32601/-32602`; el fallo de la BÚSQUEDA no es error de
  protocolo — sale como `result.[función interna]: true` con el code de dominio en el content
  (especificación MCP; un agente se fija en el code).

## Tests (18 nuevos)

- `[archivo interno]` (7): registro 201, acumulación sin pisar, comentario opcional,
  fragmento fantasma 404, `util` ausente 400, comentario >500 → 400, 401 sin clave.
- `[archivo interno]` (11): initialize, notificación sin respuesta, tools/list, tools/call
  con cita completa en el texto, filtros pasados al servicio, fallo de búsqueda como
  `[función interna]`, herramienta desconocida/args inválidos → -32602, método desconocido → -32601
  (notificaciones desconocidas se silencian), JSON roto → -32700, y 2 de ruta real
  (401 sin clave; `tools/call` sin bindings → `[función interna]: [código interno]`).

## Estado del rol después de este PR

RF-10 a RF-14, RF-40, RF-44 a RF-48: **todo implementado**. Pendiente único del rol:
verificación punta a punta + primera corrida dorada (RNF-28) — en curso sobre el corpus dev
(36 pending / 1 discarded / 0 approved a la fecha) con la infra verificada de Rol 3
(`docs/22-INFRA-VERIFICADA-CLOUDFLARE.md`).
