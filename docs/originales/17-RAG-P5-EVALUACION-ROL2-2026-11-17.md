# 17 · RAG P5 — Evaluación de la recuperación (RNF-28)

Autor: Mauricio Gatica (motor de riesgo + RAG) · Fecha: 2026-11-17
Estado al enviar este PR: **implementado con dobles y CI-verificable**. La
corrida contra el preview real queda lista para cuando existan los bindings de
Rol 3 — el script ya está en el repo y solo pide una clave read.

**Dependencia**: este PR está apilado sobre #18 (`rol2/rag-api`): la evaluación
ejercita `buscar()` (RF-46). Merge order: #18 → este. Los archivos no se
pisan con #19 (P4, independiente).

## 1. Qué resuelve

RNF-28: "conjunto fijo de consultas de evaluación con resultados esperados,
versionado junto al índice, que se ejecuta al cambiar troceado, modelo de
embeddings o ranking. Sin esto, todo cambio en el RAG es a ciegas."

Dos modos de ejecución sobre el MISMO set versionado
(`[ruta interna]`):

1. **CI (esta suite)**: corpus de prueba de 3 tópicos disjuntos + dobles
   deterministas. El título de cada ítem del corpus ES la consulta canónica
   del set (el título va dentro de cada trozo ⇒ alineación exacta). El set
   completo se corre por la tubería real `trocear → embed → índice → buscar →
   ranking`: 6/6 consultas recuperan su relevante en el top-1, MRR = 1.
   Cambiar troceado o ranking y degradar esto ⇒ suite roja.
2. **Preview (`[ruta interna]`)**: corre el set contra la API real y
   compara con la **corrida dorada** (`dorada.json`), que se genera con
   `--actualizar` una vez aprobado el resultado y se versiona en el repo
   junto a las versiones del índice (`modelo` + `chunking`). Cambios
   posteriores se miden contra esa referencia: el script reporta
   `cambios_top1` y `jaccard_promedio` de los top-5 y falla (exit 1) si hay
   regresión. Si las versiones cambian, avisa regenerar (no es regresión, es
   un índice nuevo).

## 2. Decisiones

| # | Tema | Decisión |
|---|---|---|
| 1 | Set versionado | `consultas.json` con `version`, 6 consultas (2 por tópico: frase completa + frase corta) y 2 tipos de filtro (`min_level`, `categories`) — cubre el camino del `$in`/post-filtro de P3. Esquema validado contra `[función interna]` en un test (a la deriva del contrato no entra) |
| 2 | `relevantes` | Etiquetas simbólicas (NO ids): el modo CI las resuelve contra el corpus de prueba; el modo preview no las usa (los ids reales se conocen solo tras la primera corrida — ahí la dorada los fija) |
| 3 | Dorada | NO se genera sola en la primera corrida: el script exige `--actualizar` explícito y pide REVISAR antes de commitear (evita dorar un índice roto). La dorada guarda ids + similitudes por consulta y las versiones que representa |
| 4 | Umbral de regresión | Falla si `cambios_top1 > 0` o `jaccard_promedio < 0.5` (`RAG_EVAL_UMBRAL`). Conservador: el top-1 de una consulta fija no debería moverse sin una decisión consciente |
| 5 | Doble de embeddings | Esta suite usa un hash determinista **centrado** (valores ±48): los textos no relacionados quedan cerca de 0 y los alineados dominan — separa tópicos como un embedder real. El doble de P1-P3 (todo-positivo) sirve para igualdad exacta pero NO para medir ranking: con él, cualquier par de textos daba ~0.4 de similitud y el ruido competía con el tópico correcto |
| 6 | Métricas | `[función interna]` (hit_top1, hit_topk, MRR, similitud máxima, faltantes), `[función interna]` (resumen + fallos) y `[función interna]` (cambio_top1 + jaccard top-5 + validez por versiones) — puros, en `[ruta interna]` |
| 7 | Clave del script | `RAG_EVAL_TOKEN` o `[ubicación interna]` (clave read — la de Warren alcanza, P3 no exige write) |

## 3. Archivos

- `[ruta interna]` (nuevo): métricas puras y comparación
  contra la dorada.
- `[ruta interna]` (nuevo): el set fijo
  versionado (v1).
- `[ruta interna]` (nuevo): corredor contra la API real
  (modos comparar / --actualizar).
- `[ruta interna]`: **9 tests** (métricas, comparación,
  validez del set, evaluación end-to-end 6/6).
- `docs/17-RAG-P5-EVALUACION-ROL2-2026-11-17.md`: este doc.

## 4. Cómo correrlo contra el preview (cuando exista)

```bash
# 1. clave read en [ubicación interna] (o env RAG_EVAL_TOKEN)
# 2. preview arriba: pnpm --filter api dev (o el preview de Rol 3 en RAG_EVAL_URL)
node [ruta interna] --actualizar   # primera corrida → dorada.json
#    REVISAR dorada.json antes de commitearla (es la referencia de calidad)
node [ruta interna]                # corridas siguientes → compara
# tras cambiar troceado/modelo/ranking:
#   RAG_EVAL_MODELO/RAG_EVAL_CHUNKING nuevos → el script pide regenerar la dorada
```

## 5. Qué queda del plan (RF-48/MCP)

RF-48 (feedback) y la herramienta MCP siguen como opcional/diferido del plan —
se evalúa si quedan horas de S15 después de que #18/#19/#20 se fusionen.

## 6. Verificación

- Typecheck 2/2 (contracts + api).
- Suite completa de esta rama: **289 tests en verde** (271 de la rama P3 + 9
  nuevos); `node --check` del script OK.
- Pendiente real: primera dorada contra el preview (cuando existan bindings),
  y recurrir el set si Rol 3 cambia el modelo/ranking.
