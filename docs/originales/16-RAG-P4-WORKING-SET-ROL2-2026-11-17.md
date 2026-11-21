# 16 · RAG P4 — Curación del working set (RF-47)

Autor: Mauricio Gatica (motor de riesgo + RAG) · Fecha: 2026-11-17
Estado al enviar este PR: **implementado con dobles y CI-verificable**. La curación
corre sobre D1 (funciona hoy); la indexación que completa es no-op hasta que Rol 3
agregue los bindings (`AI`/`[binding]`/`[binding]`) — el set se curar igual.

## 1. Qué resuelve

RF-47: el cron horario mantiene fresco un conjunto acotado de las noticias más
recientes y relevantes para Warren, sin que nadie lo pida:

- Selecciona el top-N (100 por defecto) del corpus **aprobado con score vigente**
  (extraído + evaluado) por una función determinista de **relevancia de riesgo +
  recencia**.
- Verifica que cada ítem esté **extraído, evaluado e indexado** y completa lo que
  falte (rondas acotadas de `[función interna]`; no re-scrapea nada).
- **Materializa SIEMPRE una fila** en `[identificador interno]` por corrida:
  `[campo interno]` (criterios legibles), `inputs` (candidatos), `outputs` (tamaño
  final) y `[identificador interno]` (el set, ordenado por ranking — el orden que Warren
  consume vía `GET /v1/rag/working-set`, P3).
- Loguea el **diff contra el set anterior**: qué entró, qué salió (listas
  recortadas a 25 ids en el log; el detalle completo queda reconstruible de las
  filas consecutivas). Definición de hecho #3 del rol.

## 2. Decisiones

| # | Tema | Decisión |
|---|---|---|
| 1 | Ranking | `puntaje = nivel(0-4) + recencia(0-3)`, ventanas: ≤24h=3, ≤48h=2, ≤7d=1, resto 0; publicado a futuro (desfase de reloj) = lo más fresco. Desempate: `published_at DESC` y luego `id DESC` (el orden del SQL, preservado por sort estable). Función pura exportada (`[función interna]`) para fijarla en tests |
| 2 | Candidatos | Solo `[campo interno]='approved'` CON score vigente (último por `created_at`; subconsulta correlacionada = una consulta sin límite de params). Tope de seguridad `TOPE_CANDIDATOS=2000` (los más viejos fuera; logueado). Un descartado jamás vuelve a entrar (RF-51) |
| 3 | Completar indexación | Rondas acotadas de `[función interna]({limite:25})` (máx 4 = 100 ítems/corrida); corta sin progreso para no ciclar fallas permanentes. Si el embedder/índice falla, la curación NO se cae: log `[código interno]` y el set se materializa igual (vive en D1). Lo que no alcance la corrida lo termina la siguiente (el selector va por recencia, la misma ventana del set) |
| 4 | Observabilidad post-set | `[función interna]()` detecta set items sin trozos del modelo/troceado vigentes (consulta en grupos de 90 ids + 2 params < 100 del límite D1); va al resultado y al log (`sin_indexar`) |
| 5 | Tamaño | `opciones.tamano` (default 100), acotado 1..100 = tope del contrato `[función interna]`. Los criterios editables desde el panel (RF-47 "mismo principio que RF-28") quedan **diferidos**: necesitan endpoint de admin + config en KV |
| 6 | Enganche | `scheduled()` ya enruta `0 * * * *` → `working_set` (PR #14); ahora llama `[función interna](entorno)` con `.catch` que loguea `cron_working_set_fallido` (la corrida fallida no tumba el cron; la próxima hora reintenta). Sin cron en `[archivo interno]` (pendiente Rol 3) el flujo simplemente no se dispara |
| 7 | Archivo | `servicios/[archivo interno]` NUEVO (no extiende `servicios/[archivo interno]`): evita el conflicto con el PR #18 (que agrega `buscar()` al final de ese archivo) y separa curación (cron) de recuperación (API) |

## 3. Archivos

- `[ruta interna]` (nuevo): `[función interna]()`,
  `[función interna]()`, `[identificador interno]`.
- `[ruta interna]`: el flujo `working_set` del cron pasa de no-op
  logueado a llamar la curación (con catch defensivo).
- `[ruta interna]`: **8 tests** — 2 del ranking puro
  (niveles + bordes exactos de las ventanas) y 6 de la corrida completa con dobles.
- `docs/16-RAG-P4-WORKING-SET-ROL2-2026-11-17.md`: este doc.

## 4. Ejemplo de corrida

```jsonc
// log [código interno]
{
  "working_set_id": "…", "tamano": 100,
  "candidatos": 342, "items": 100,
  "entradas": 12, "entradas_ids": ["…"],   // recortado a 25
  "salidas": 12,   "salidas_ids": ["…"],
  "sin_indexar": 0, "sin_indexar_ids": [],
  "indexadas": 12, "rondas": 1, "indexacion_fallida": false
}
// fila: { [campo interno], [campo interno]: [identificador interno], inputs: 342, outputs: 100, [identificador interno]: "[…orden por puntaje…]" }
```

## 5. Para Rol 3 (Xavier)

- Falta agregar `0 * * * *` (cron horario, SIN backup) a `[archivo interno]` en dev —
  ya está en el md de pendientes. Con eso el flujo corre cada hora.
- El `GET /v1/rag/working-set` del PR #18 lee la última fila: desde este PR el
  endpoint deja de dar 404 en cuanto la primera corrida materialice.
- La curación no exige bindings: en dev sin índice el set se curar igual (la
  indexación queda pendiente para cuando existan).

## 6. Verificación

- Typecheck 2/2 (contracts + api).
- Suite completa: **270 tests en verde** (262 de develop + 8 nuevos).
- Pendiente de verificación real: corrida del cron contra el preview (cuando
  exista el horario y el corpus aprobado de la compuerta RF-51).
