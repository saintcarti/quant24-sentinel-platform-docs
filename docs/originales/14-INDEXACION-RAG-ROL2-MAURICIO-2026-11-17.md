# 14 · RAG P2 — Indexación del corpus en Vectorize (RF-44/RF-45/RF-51)

Autor: Mauricio Gatica (motor de riesgo + RAG) · Fecha: 2026-11-17
Estado al enviar este PR: **implementado con dobles, no-op sin bindings** (espera que
Rol 3 cree los índices `[recurso interno]`/`[recurso interno]` + los 6 metadata indexes
y agregue los bindings `[binding]`/`[binding]`; los embeddings van sobre el binding `AI`).

> **Apilado sobre PR #12** (`rol2/rag-troceado`): esta rama parte del head de P1, así
> que hasta que #12 se fusione el diff muestra P1+P2. Orden de merges: **#12 → este PR**.
> Tras el merge de #12, el diff de este PR queda reducido a los archivos de P2.

## 1. Qué resuelve

RF-44 + RF-45: el corpus aprobado se trocea (`chunking-v1`), se embebe (`bge-m3`, 1024)
y entra al índice vectorial con **metadata filtrable por trozo**, para que la búsqueda
de Warren (RF-46) filtre por símbolo/nivel/categorías/fuente/origen/fecha en la MISMA
operación de consulta. **RF-51 como candado**: nada entra sin `[campo interno]='approved'`,
y `desindexar()` deja lista la cascada para el flujo de descarte.

## 2. Decisiones

| # | Tema | Decisión |
|---|---|---|
| 1 | Máscara `cats` (pedido de Xavier, 17-nov) | **Semántica fijada en los contratos**: `BITS_CATEGORIA` (bit i = `[identificador interno][i]`, bit 0 = la primera) + `[función interna]()` (OR de bits, orden del array libre). Si el catálogo cambia ⇒ reindexar completo |
| 2 | Metadata por trozo (RF-45) | Contrato `MetadataTrozo` validado con zod ANTES de tocar el índice: `simbolo` (primer `q24_symbol` alfabético; `""` si no hay), `nivel` (0-4 del score vigente), `fecha` (epoch seg de `published_at`), `fuente` (`source_id`), `origen`, `cats` |
| 3 | `[campo interno]` | **`[campo interno] = [campo interno]`**, e ids deterministas `[id interno]`: la reindexación PISA en lugar de duplicar, y la cascada D1 → vector es directa |
| 4 | Reindexación | El selector elige approved SIN trozos o con trozos de otra versión (`embedding_model != @cf/baai/bge-m3` o `embedding_version != chunking-v1`). Cambiar modelo o troceado ⇒ reindex automático por ítem |
| 5 | Orden de escritura (a prueba de fallos) | vectores nuevos upsert PRIMERO, filas D1 DESPUÉS; si D1 falla, **compensación** borrando los vectores recién subidos ⇒ el ítem queda pendiente y la ronda siguiente reintenta el ítem entero. Las filas D1 son la fuente de verdad |
| 6 | Idempotencia (RNF-03) | Segunda ronda = 0 ítems; `desindexar()` dos veces = no-op |
| 7 | Presupuesto | Límite 25 ítems/ronda (máx 100); embeddings por lotes de 20 textos (medición del Paso 0); gasto estimado ~$0.0006/ronda |
| 8 | Sin bindings | `[binding interno]`/`[binding interno]` null ⇒ no-op logueado (`[código interno]`). NO se toca `[archivo interno]`: los bindings los agrega Rol 3 cuando los índices existan |
| 9 | Dónde corre | Enganche en `[archivo interno]` tras el motor (extensión de la convergencia RF-40): cada ingesta exitosa indexa lo aprobado pendiente; el working set (P4) completará lo faltante cada hora |

## 3. Archivos

- `[ruta interna]`: `BITS_CATEGORIA`, `[función interna]()`,
  `[función interna]` + `MetadataTrozo` (re-exportados por el índice del paquete).
- `[ruta interna]`: `[interfaz interna]` inyectable, adapter
  `[función interna]()` (valida la forma de la respuesta del catálogo),
  `MODELO_EMBEDDINGS`/`DIMENSION_EMBEDDINGS`/`LOTE_EMBEDDINGS`.
- `[ruta interna]`: `[interfaz interna]` inyectable (`upsert`/`borrar`), adapter
  `[función interna]()` con particionado por el tope del binding (100) y validación de
  metadata antes de enviar.
- `[ruta interna]`: `[función interna]()` (selector RF-51 +
  reindexación por versión, metadata, órden de escritura con compensación) y
  `desindexar()` (cascada vectores + filas D1).
- `[ruta interna]`: enganche tras `[función interna]()` con try/catch.
- `[ruta interna]`: `[binding interno]` + `[binding interno]` (null = no-op).
- `[ruta interna]`: bindings `AI` (compartido con capa 2, misma firma que su
  PR), `[binding]` (prod gana) + `[binding]` (dev) → adapters reales.
- `[ruta interna]`: **23 tests**.
- `docs/14-INDEXACION-RAG-ROL2-MAURICIO-2026-11-17.md`: este doc.

## 4. Para Rol 3 (Xavier)

Cuando crees los índices y los 6 metadata indexes (ANTES del primer upsert):

1. Agrega a `[archivo interno]` los bindings vectorize: `[binding]` → `[recurso interno]`
   (prod) e `[binding]` → `[recurso interno]` (dev), y el binding `AI` si aún no
   está (el del PR de la capa 2).
2. La primera ronda real indexa los approved existentes en el dev (seed manual de
   `[campo interno]='approved'` si los fixtures siguen `pending`).
3. Verificación del `$in` (~300 vectores, índice desechable): el filtro de categorías
   traduce a un `$in` de máscaras precomputadas con `BITS_CATEGORIA` — la constante ya
   está exportada y testeada.

## 5. Verificación

- Typecheck 2/2 (contracts + api).
- Suite completa: **242 tests en verde** (219 de la base P1 + 23 nuevos). Dobles
  deterministas de `[interfaz interna]` e `[interfaz interna]` + migraciones reales: nada toca la red.
- La verificación real (bindings + índice) queda para el preview de Xavier, como el
  resto de la fase.
