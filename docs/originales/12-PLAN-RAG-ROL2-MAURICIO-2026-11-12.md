# 12 · Plan de trabajo — Capa RAG del motor (Fase 5, Rol 2)

Autor: Mauricio Gatica (motor de riesgo + RAG) · Creado: 2026-11-12 · Última actualización: 2026-11-16
Requisitos: RF-44 a RF-48, RF-51, RNF-28 de `03-Especificacion-v1.1-ondemand-y-RAG.md`;
alcance del rol en `08-Rol-Motor-de-Riesgo-y-RAG.md`.

## 1. Alcance y candados

- Trabajar solo en `[ruta interna]` y el módulo RAG/motor en `[ruta interna]`.
  Sin conectores, API pública existente ni auth (los endpoints nuevos de RAG se
  registran en `[ruta interna]` con el visto bueno de Rol 3).
- Cada paso = una rama `rol2/rag-*` y un PR a `develop` (develop = preview;
  master = producción, promoción explícita).
- RFs del plan: RF-44 (troceado y embeddings), RF-45 (índice Vectorize + cascada),
  RF-46 (API de recuperación; MCP al final si queda), RF-47 (working set),
  RF-51 (candado de aprobación), RNF-28 (set de evaluación). Opcional: RF-48 (feedback).

## 2. Decisiones cerradas

| # | Tema | Decisión | Fecha |
|---|---|---|---|
| 1 | Modelo de embeddings (bloqueante #5) | Medir 1.000 embeddings reales antes de fijar modelo y dimensión del índice | 2026-10-09 |
| 2 | Arranque de la construcción | Después de Fase 2 estable (capa 1 + RF-40 + PR #10 fusionado) | 2026-10-09 |
| 3 | Forma del MCP | Al final, si queda tiempo; primero API REST | 2026-10-09 |
| 4 | Modelo de embeddings fijado | `bge-m3/v1`, 1024 dimensiones (sin truncado a 512; $0.012/M tokens) | 2026-10-09 |
| 5 | Metadata filtrable (RF-45) | 6 slots de 10: `[slot]` ($in), `[slot]` ($gte, 0-4), `[slot]` ($gte/$lte, epoch seg), `[slot]` ($in), `[slot]` ($eq), `[slot]` (máscara de bits, bit i = [identificador interno][i]; filtro = $in de máscaras precomputadas). Sin índice: `[identificador interno]` (cascada por D1 → [campo interno]) y `[campo interno]` (siempre `approved`, RF-51). Todos los metadata indexes se crean ANTES del primer upsert | 2026-11-12 |
| 6 | Índices | Dos: `[recurso interno]` (prod) + `[recurso interno]` (dev), 1024/cosine. `vectorize` en wrangler es ARRAY. Los 3 índices ajenos de la cuenta (otros productos) no se tocan | 2026-11-12 |
| 7 | Riesgo único del diseño | No hay límite documentado del tamaño de `$in` en Vectorize → verificar empíricamente en un índice desechable ANTES de crear los definitivos | 2026-11-12 |

## 3. Estado de dependencias (2026-11-16)

- PR #10 (capa 1 del motor) fusionado (`08bb5ee`); dev sirviendo capa 1.
- PR #11 (Fase 1 ingesta completa, 203 tests) fusionado (`7afdbaa`). **RF-40 quedó
  hecho**: `[función interna]()` enganchada en `[archivo interno]` tras cada ingesta exitosa.
- Producción: master en `878b5b9` → prod sin capa 1 ni ingesta hasta el promote (Rol 3).
- Bindings `ai` + `[binding]` (prod y dev) y el cron horario `0 * * * *` **siguen
  pendientes** en `[archivo interno]` (respuesta a Rol 3 enviada el 2026-11-16).
  Capa 2 NO está bloqueada por token (OAuth del equipo ya tiene `ai:write`).
- Token del equipo en `[ubicación interna]/` (Vectorize:Edit + Workers AI:Edit + D1:Edit):
  pendiente de que Rol 3 lo deje ahí; se necesita para verificar P2/P3 contra la
  cuenta del equipo.
- Compuerta RF-51 (approve/discard): sin endpoint aún → mientras tanto el índice
  nace vacío con datos reales; para la demo, seed manual de `[campo interno]='approved'`
  en dev (los fixtures `syn-*` necesitan ese seed para probar la indexación).
- Crédito: $10.000, cubre Workers AI y Vectorize (no AI Gateway, que no se usa);
  expira 11-ago-2027.

## 4. Paso 0 — Medición del costo de embeddings (CERRADO 2026-10-09)

| Modelo | Dimensión | 1.000 embeddings | Costo |
|---|---|---|---|
| `@cf/baai/bge-m3` | **1024 (confirmada)** | 39,3 s | **~$0.01** ($0.012/M tokens) |
| `@cf/baai/bge-base-en-v1.5` | 768 (trunca a 512 tokens) | 28,9 s | ~$0.034 ($0.067/M) y pierde el 36% del trozo de RF-44 |

Escenario máximo del dimensionamiento (261.000 embeddings/mes ≈ 212M tokens): **~$2.5/mes**,
muy por debajo del rango 0/12/90 presupuestado. Franquicia: 10.000 neurons/día gratis.
**Decisión: `bge-m3/v1`, dimensión 1024** (multilingüe ES/EN, hasta 8.192 tokens).

## 5. Construcción — un PR por paso (a `develop`)

### P1 · Contratos y troceado — rama `rol2/rag-troceado` — **ESTE PR (2026-11-16)**
- `[ruta interna]`: contratos RAG publicables por `[paquete interno]`
  (consulta con filtros RF-45, fragmento con cita completa obligatoria, respuesta,
  working set, feedback RF-48; `VERSION_CHUNKING`).
- `api/[ruta interna]`: `trocear()` pura — ~800 tokens (heurística chars/4
  documentada), 15% de solape alineado a palabra, respeta límites de párrafo
  (párrafo → oraciones → corte duro, solo como último recurso), título dentro de
  cada trozo, body vacío → title+summary, idempotente (chunking-v1).
- `api/[ruta interna]`: `RepositorioTrozos` sobre `[identificador interno]`
  (`[función interna]` en un `batch()` respetando el tope de 100 parámetros de D1,
  `[función interna]`, `[función interna]`, `[función interna]` — deja lista la cascada para P2).
- 16 tests nuevos (suite total 219). Todo con [ruta interna] reales locales:
  no necesita el índice ni bindings.

### P2 · Indexación (RF-44/45) — rama `rol2/rag-indexacion`
- Interfaces inyectables `[interfaz interna]` e `[interfaz interna]` + adapters finos para Workers AI y Vectorize.
- `servicios/[archivo interno]`: `[función interna]()` (approved sin trozos o con versión vieja →
  trocear → embed → upsert en Vectorize con `[campo interno] = [campo interno]` + filas en D1, por
  lotes, idempotente), `desindexar()` (cascada trozos + vectores, lista para cuando
  exista borrado/descarte), metadata filtrable por trozo (decisión #5 de arriba).
- Candado RF-51: solo `[campo interno]='approved'` entra; `discarded` → desindexar si existía.
- ~12 tests con dobles.

### P3 · API de recuperación (RF-46 REST) — rama `rol2/rag-api`
- `[ruta interna]`: `POST /v1/rag/buscar` (embed de la consulta → índice con filtros →
  D1 → fragmentos con extracto, resumen, nivel, justificación, entidades y cita
  completa) y `GET /v1/rag/working-set`.
- Una línea en `[ruta interna]` para registrarlas (Rol 3 lo aprueba en el PR). Auth Bearer
  y limitador ya aplican bajo `/v1/*`.
- ~10 tests (filtros, citas completas, top_k, errores 400/401).

### P4 · Working set (RF-47) — rama `rol2/rag-working-set`
- `[función interna]()`: top-100 por recencia + relevancia, completa indexación faltante,
  materializa registro en `[identificador interno]` (entradas/salidas vs set anterior + motivo).
- Cron `0 * * * *` (Rol 3 lo agrega a `[archivo interno]`): `scheduled()` se ramifica por
  `event.cron` — `0 * * * *` → solo working set; `*/15 * * * *` → ingesta + motor;
  `0 3 * * *` → backup. ~6 tests.

### P5 · Evaluación y cierre — rama `rol2/rag-evaluacion`
- RNF-28: set fijo de consultas + resultados esperados versionado en el repo; script
  que corre contra el preview real al cambiar troceado, modelo o ranking.
- Si queda: RF-48 (feedback: endpoint + migración) y herramienta MCP mínima
  (JSON-RPC, una sola herramienta `[herramienta interna]`).

## 6. Estrategia de tests (~40 en la fase)

- Estilo casa: `node:sqlite` + migraciones reales + dobles estrictos.
- Fake embedder determinista + fake índice en memoria → P1-P4 corren en CI sin bindings.
- La integración real (ai/Vectorize) solo se verifica en el preview de Rol 3
  (local sin sesión CF no tiene bindings).

## 7. Coordinación con el equipo

- **Rol 3 (Xavier)**: respuesta del 2026-11-16 — crear los 2 índices + los 6 metadata
  indexes antes del primer upsert, verificación empírica del `$in` en índice desechable,
  bindings `ai`/`[binding]` (array en wrangler) en prod y dev, cron `0 * * * *`,
  token en `[ubicación interna]/` con Vectorize:Edit. Clave read-only para Warren
  (`POST /v1/keys`, integration "warren") cuando P3 esté.
- **Rol 4 (Nicolás)**: su integración (proxy + feed + admin fuentes/umbrales) vive en el
  develop del platform y apunta a `sentinel-api-dev`; falta la compuerta RF-51
  (approve/discard) — sin ella el índice nace vacío con datos reales.
- **Rol 1 (Iván)**: ingesta fluyendo para que exista corpus que aprobar.

## 8. Riesgos

- RF-51 tarde → índice vacío en la demo (mitigar con seed manual de approvals en dev).
- Cambiar de modelo después = reindexar todo y duplicar vectores durante la transición
  (por eso se midió primero y se fija `bge-m3/v1`).
- Sin límite documentado del tamaño de `$in` → verificación empírica antes de crear
  los índices definitivos (riesgo de diseño RF-45).
- Crédito CF: cubre Workers AI y Vectorize; expira 11-ago-2027.

## 9. Definición de hecho (del rol)

1. Nada entra al índice sin una aprobación registrada (RF-51).
2. Todo fragmento lleva cita completa (título, medio, fecha, URL).
3. Working set dentro del tamaño, con registro de entradas/salidas y motivo.
4. Set de evaluación corriendo al cambiar troceado, modelo o ranking (RNF-28).

## 10. Seguimiento

- [x] P0: medición de embeddings y decisión `bge-m3/v1` (1024) — 2026-10-09
- [x] PR #10 fusionado a develop (`08bb5ee`) — capa 1 sirviendo en dev
- [x] PR #11 fusionado a develop (`7afdbaa`) — ingesta completa; **RF-40 hecho**
- [x] Decisión de metadata RF-45 cerrada con Rol 3 — 2026-11-12
- [x] Respuesta a Rol 3 (metadata + ajustes) enviada — 2026-11-16
- [x] **P1 contratos y troceado — ESTE PR (16 tests, suite 219)**
- [ ] Rol 3: índices `[recurso interno]`/`[recurso interno]` + metadata indexes + bindings + cron horario + token
- [ ] Verificación empírica del `$in` (índice desechable)
- [ ] Capa 2 (LLM refuerzo) — único candado propio que falta antes del RAG real
- [ ] P2 indexación
- [ ] P3 API de recuperación
- [ ] P4 working set
- [ ] P5 evaluación (+ RF-48 y MCP si queda)

## 11. Registro de avance

- **2026-10-09**: plan creado. Medición del bloqueante #5 ejecutada (2.000 embeddings
  reales): bge-m3 1024/39s vs bge-base-en 768/29s pero trunca a 512 tokens y cuesta
  5,6×. Decisión: `bge-m3/v1`, 1024. Costo cerrado (~$0.01/1.000; máx ~$2.5/mes).
- **2026-11-12**: respuesta de Rol 3 analizada. Decisión de metadata RF-45 cerrada:
  máscara de bits + tabla de 6 propiedades con tipos y filtros; metadata sin índice
  (`[identificador interno]`, `[campo interno]`); verificación del `$in` en índice desechable;
  token con Vectorize:Edit; ramificación del cron por `event.cron` para P4.
- **2026-11-16**: **P1 construido y a PR** (`rol2/rag-troceado`): contratos RAG en
  `[ruta interna]` (re-exportados desde el índice del paquete), `trocear()` pura
  (chunking-v1: ~800 tokens chars/4, solape 15% alineado a palabra, límites de
  párrafo, título dentro del trozo, body vacío → title+summary), `RepositorioTrozos`
  (batch transaccional con particionado por el tope de parámetros de D1 y cascada de
  borrado lista para P2). 16 tests nuevos; suite total 219 en verde; typecheck 2/2.
