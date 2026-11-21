# 08 — Portada de la Fase 1 de Iván al mainline (2026-10-09)

Autor: Xavier (CTO). Rama: `feat/portada-fase1-ingesta_X_L`.

## Qué pasó y por qué esto existe

Iván (Rol 1) construyó la Fase 1 completa — los 4 conectores, la normalización, la
resiliencia y la persistencia con deduplicación — en la rama `feature/fase-1-ingesta`
(tip `9a6a1a8`), que no comparte merge-base con el mainline y cuyo código está en
español/[función interna] sobre un esquema v1.1 propio. Mantener dos árboles vivos era un riesgo
de pérdida, así que:

1. **Rescate** (ya hecho): bundle completo en `rescate-sentinel/` (fuera del repo) con los
   19 patches, y tag `rescate/fase-1-ingesta-2026-10-09` en la forja. El trabajo original
   sigue vivo y verificable aunque la rama se borre.
2. **Portada incremental** (este documento): portar el código funcional al mainline,
   adaptándolo a sus convenciones (inglés en datos/columnas, `Ejecutor`, snake_case),
   etapa por etapa, con tests en cada una.

## Decisiones de la portada

| Decisión | Fundamento |
|---|---|
| El **mainline posee** breaker autoritativo, cron, cola, `sources`, `source_runs` | Ya existen y están probados; reimplantarlos duplicaría la fuente de verdad (RNF-03: idempotencia por diseño, no por código extra) |
| `ProcesadorIngesta.procesar(fuente, entorno)` — se añade `entorno` | El conector necesita `db`, `kv`, `traer` (fetch inyectable para tests) y `logger`, que son por-invocación en el mainline. Un solo argumento de más en un contrato nuevo no tiene costo |
| Tras una corrida exitosa el consumidor llama `[función interna](entorno)` | Cerraba el hueco del pipeline (RF-40): sin ese wiring las noticias inges­tas no tenían `[identificador interno]` y no aparecían en `/v1/news`. En try/catch propio: un fallo del motor no invalida la ingesta |
| `[campo interno]` + `[campo interno]` en la capa 1 | El tono GDELT (RF-11) ya venía en `[tipo interno]`; la capa 1 de Mauricio ya lo acepta (`[umbral interno]`); faltaban la columna y el SELECT |
| Migración `[migración interna]`: `[campos internos]` | La deduplicación de Iván (URL normalizada UNIQUE + ventana de contenido) y su evidencia de lote necesitan casa; en inglés y STRICT como el resto del esquema |
| `connector_type` = `SourceId` del catálogo (`sec-edgar`, `central-bank-rss`, `google-news-rss`, `gdelt`) | La tabla `sources` ya existía con `connector_type` libre; se fija al catálogo de `[paquete interno]` para que el registro y la configuración efectiva no diverjan. El `[archivo interno]` dev se actualizó; las fuentes de producción se ajustan con `PUT /v1/sources/:id` |
| `[campo interno]` = fila de `sources`, no id del catálogo | La FK lo exige; el wrapper `[archivo interno]` hace la traducción catálogo→fila |
| NO se portó: `[archivo interno]`, `[archivo interno]`, users/[identificador interno]/audit de su árbol, su breaker, `[archivo interno]`, scripts que dependen de su pipeline | El mainline ya los posee o la decisión de diseño es que vivan en otro rol; portarlos crearía duplicados |

## Etapas (commits de esta rama)

| # | Contenido | Estado |
|---|---|---|
| 0 | Rescate: bundle + tag en forja | ✅ hecho antes de esta rama |
| 1 | Contratos en `[paquete interno]`: `[adaptador]`, `[tipo interno]`, `SourceConfig`, políticas (retry/rate/circuit) y `FUENTES_DEFAULT` de las 4 fuentes | ✅ `feat(contracts)` |
| 2 | Resiliencia: `[archivo interno]` (backoff + clasificación de errores), `[archivo interno]` (token bucket en KV), `[archivo interno]` (HealthTracker), dobles de test (`RelojVirtual`, `RedDoble`) | ✅ 29 tests |
| 3 | Los 4 conectores: `[adaptador]`, `[adaptador]` (multi-feed, fallo parcial), `[adaptador]` (fail-closed sin términos), `[adaptador]` (sin reintento de 429, con evidencia), parser XML, `[ruta interna]` + fixtures reales de la prueba 72 h | ✅ 32 tests + 3 skipped (por fixtures) |
| 4 | Normalización (`[ruta interna]`), `RepositorioIngesta` (dedup atómica con las trampas de D1), wrapper `[archivo interno]`, wiring del consumidor + motor (tono), migración 0003, seed dev, vars de entorno, test E2E cola→conector→D1→motor con el fixture real de la Fed | ✅ esta rama |
| 5 | Aceptación: re-corrido del protocolo de 72 h en `sentinel-api-dev` (doc 10) | ⏳ requiere despliegue + 72 h reales |

## Estado de la prueba de aceptación (doc 10)

Criterios 1–11 verificados en los tests de la rama (dedup, fallo parcial, breaker con
falla simulada vía `SIMULAR_FALLA_FUENTES`, relevancia, idioma, entidades). **Pendiente de
correr contra `sentinel-api-dev`**: los 4 conectores en su frecuencia real durante 72 h
continuas, con `USER_AGENT_EMAIL` definido (SEC cierra honestamente hasta entonces) y
`GOOGLE_NEWS_TERMS` con el universo de términos (pendiente de definir por Quant24, doc 03).

## Vars de entorno nuevas (prod y dev, vacías por defecto)

| Var | Para | Quién la define |
|---|---|---|
| `USER_AGENT_EMAIL` | User-Agent identificable de SEC EDGAR (RNF-16) | Quant24 — pendiente |
| `GOOGLE_NEWS_TERMS` | Array JSON o términos por línea; el conector cierra sin ellos | Quant24 — universo pendiente (doc 03) |
| `SIMULAR_FALLA_FUENTES` | Solo para la prueba de aceptación del breaker | Vacía en operación |

## Referencias

- Docs portados de la rama de Iván: `09-F1-BUILD-STATUS-IVAN-2026-10-09.md`,
  `10-F1-PRUEBA-72H-IVAN-2026-10-09.md`, `11-F1-PROPUESTA-DLQ-IVAN-2026-10-09.md`.
- Misión de Iván: `03-ROL-1-IVAN-INGESTA-2026-10-06.md`.
- Estado general del código: `07-ESTADO-CODIGO-2026-10-06.md` (actualizado por esta portada).
