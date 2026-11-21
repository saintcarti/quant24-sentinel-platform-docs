# Evidencia E2E real — Fase 5 (Rol 2) · preview dev · noche 19→20-nov-2026 (UTC)

Ejecutado 100% por HTTP contra `https://[worker redactado]`
con la clave admin del dev (la del chat de equipo — nunca en archivos). Todo quedó
trazado en `GET /v1/audit`. Ejecutor: Mauricio (Rol 2), según lo asignado en el
correo de Xavier del 19-nov (selección del corpus + ejecución/evidencia del E2E).

## TL;DR

| Frente | Resultado | Evidencia |
|---|---|---|
| RF-51 — curación (selección del corpus) | ✅ 30 aprobados + 6 descartados | audit `[identificador interno]` ×36 |
| Indexación con embeddings reales | ✅ bge-m3 1024, crons 00:30/00:45 | búsqueda semántica devuelve fragmentos |
| Recuperación semántica con citas y filtros | ✅ 3 fragmentos FOMC, similitud 0.630 | `POST /v1/rag/buscar` |
| RF-47 — working set | ✅ 30 ítems, cron 01:00:15 | `GET /v1/rag/working-set` |
| RNF-28 — primera corrida dorada | ✅ 6 consultas versionadas | **PR #30** (`rol2/dorada-rag`, `854516c`) |
| Capa 2 (score llm en ingesta nueva) | ⚠️ bloqueo de egreso datacenter, no de código | diagnóstico abajo |

## Cronología (UTC, 19→20-nov)

| Hora | Acción | Resultado |
|---|---|---|
| 00:25–00:27 | Verificación de clave + `GET /v1/news` | 36 pending (30 reales FOMC/ECB + 6 sintéticas) |
| 00:27 | Curación RF-51 por lotes (25 + 5, límite schema ≤25) + 6 descartes | 200 OK; todo auditado |
| 00:28 | Reactivación de `ensayo-ingesta` (pausada desde F1) | active |
| 00:30 | Cron `*/15`: reconcilia 25 aprobaciones → indexa con embeddings reales | verificado en vivo |
| 00:31 | `POST /v1/rag/buscar` "Decisión de tasas de la Fed" + filtro `monetary_policy` | **3 fragmentos FOMC**, similitud 0.630, nivel medium, cita completa (título + URL federalreserve.gov) |
| 00:33–00:47 | Ensayo corre OK (err24h 0.66→0.4) | 0 ítems nuevos — feeds de bancos centrales silenciosos de madrugada (dedup) |
| 00:36 | Fuente `rol2-e2e-sec-edgar` creada | err 100% → sec.gov bloquea IPs datacenter; **pausada 00:49** |
| 00:45 | Cron `*/15`: reconcilia las 11 tareas restantes (5 aprobadas + 6 descartes) | outbox de aprobaciones en 0 |
| 00:50 | Fuente `rol2-e2e-google-news` creada (la fila `google-news-rss` faltaba) | err 100% desde Workers; **200 con artículos frescos desde máquina local** |
| 01:00:15 | Cron working set (`0 * * * *`): reconcilia + `[función interna]` | **30 ítems** (snapshot `6575b78f-…`) |
| 01:04 | Clave read dedicada + `[script de evaluación] --actualizar` | `dorada.json` → **PR #30** |
| 01:10 | Estado final verificado | ensayo OK (last_ok 01:02:57, err24h 0.33); 0 ítems nuevos post-01:00 |

## Detalle por requisito

### RF-51 — curación con selección revisada
- **30 aprobados** (en 2 lotes: 25 + 5): minutos y statements FOMC, discursos y
  pronunciamientos ECB (Lane **×2** — verificados como discursos distintos: 17-nov PDF vs
  10-nov HTML —, Schnabel, Cipollone, Lagarde, Vujčić, Elderson), todos del ensayo real
  de Rol 1.
- **6 descartados** (`syn-0001..0006`, fixtures sintéticos de los tests de aceptación de
  F1), `motivo: irrelevante`.
- Auditoría: `[identificador interno]` ×36 con actor = clave admin.

### RAG — indexación y recuperación (RF-46)
- La reconciliación del outbox `[identificador interno]` (≤25 tareas/ciclo) indexó a los aprobados
  en los crons de las 00:30 y 00:45 con embeddings `@cf/baai/bge-m3` (1024 dims).
- Búsqueda semántica verificada en vivo con filtro por categoría:
  `POST /v1/rag/buscar {"consulta":"Decisión de tasas de la Fed","filtro":{"categoria":"monetary_policy"}}`
  → 3 fragmentos FOMC con cita completa (título + URL `federalreserve.gov`),
  similitud top **0.630**, nivel `medium`.

### RF-47 — working set
- `GET /v1/rag/working-set` → snapshot `6575b78f-8937-4b1e-900d-386ce157cbee`,
  ejecutado por el cron a las 01:00:15, **30 ítems**.
- Criterios del snapshot: relevancia (nivel 0-4) + recencia (≤24h=3, ≤48h=2, ≤7d=1),
  desempate por `published_at` desc.

### RNF-28 — primera corrida dorada (set v1, 6 consultas)
| Consulta | Filtro | Resultado |
|---|---|---|
| `q-fed-completa` / `q-fed-corta` | `monetary_policy` | 5 fragmentos; top-1 = statements FOMC; similitudes 0.55–0.63 |
| `q-hack-completa` / `q-hack-corta` | sin filtro | 5 fragmentos; top-1 = ítem de euro digital (0.41–0.46) |
| `q-sec-investigacion` / `q-sec-completa` | `min_level: high` | **0 fragmentos — esperado y honesto**: el corpus real aprobado no tiene ítems high (los únicos eran los sintéticos descartados); el filtro funciona y la dorada lo registra como baseline |

- Versiones: `@cf/baai/bge-m3` + `chunking-v1` (en sync con `MODELO_EMBEDDINGS`/`VERSION_CHUNKING`).
- Artefacto revisado antes de commitear (como exige el script):
  `[ruta interna]` — rama `rol2/dorada-rag`, commit
  `854516c` → **PR #30** (mergeable).
- Corrida hecha con clave read dedicada `rol2-e2e-dorada` (minteo auditado como
  `[identificador interno]`), no con la admin.

### Capa 2 (score llm) — bloqueo de egreso, no de código
- Los 36 ítems del corpus fueron scored **antes** de `REFUERZO_ACTIVO=1` (solo capa 1,
  rules); el score llm solo aparece en ingesta nueva. Los intentos de forzarla:
  1. `ensayo-ingesta` reactivada → corre OK, pero los feeds de bancos centrales están
     silenciosos de madrugada → 0 ítems nuevos (dedup).
  2. `rol2-e2e-sec-edgar` → sec.gov bloquea IPs datacenter (err24h 1.0) → pausada.
  3. `rol2-e2e-google-news` → err24h 1.0 desde el Worker, pero **desde máquina local el
     mismo feed responde 200 con artículos de hoy** → el bloqueo es del egreso datacenter
     de Workers (mismo patrón que los 429 de GDELT que vio Xavier).
- Diagnóstico exprés: una línea de `wrangler tail` en el dev filtrando la fuente
  `6cc9efd4-a59d-416c-9ab2-ea68d0b1c2e0` muestra el error exacto.
- Defensa defendible: REFUERZO habilitado (verificado en el jsonc dev) + motor con
  fallback a capa 1. La ejecución llm requiere un ítem nuevo por una fuente no bloqueada:
  los `.gov` de bancos centrales **sí** funcionan desde Workers (el ensayo lo demuestra);
  Google News/SEC/GDELT no.
- Dejé `rol2-e2e-google-news` **activa** por si el colo transitorio la deja pasar, y el
  ensayo **activo** para la mañana: los discursos ECB (desde ~07:00 UTC) entrarían con
  score llm y la evidencia es visible sin aprobar: `GET /v1/news/:id` →
  `score_history` con `layer: "llm"`.

## PRs abiertos (ambos mergeable, esperando qfd-admin)
- **#29** — RF-48 feedback RAG + MCP re-encuadrado a "consumidores autorizados"
  (`rol2/rag-feedback-mcp-v2`; 18 tests nuevos; suite completa 549 passed | 3 skipped;
  migración renumerada `[migración interna]`; doc `docs/23`).
- **#30** — primera dorada RNF-28 (`rol2/dorada-rag`, `854516c`).

## Limpieza pendiente (tras la defensa — decisión de Xavier)
1. **Rotar/borrar la clave admin del dev** (transitó por chat).
2. Borrar la clave read `rol2-e2e-dorada`:
   `DELETE /v1/keys/2f3f3f04-853d-4beb-be7a-873947d8dc02`.
3. Fuentes de prueba: `rol2-e2e-google-news` (activa, bloqueada desde Workers) y
   `rol2-e2e-sec-edgar` (pausada) — borrar o conservar a criterio.
   `ensayo-ingesta` quedó **activa** (estaba pausada desde F1): es la mejor oportunidad
   de capturar score llm antes de la defensa; pausarla de nuevo después si se prefiere
   el estado anterior.

## Números para la defensa
30 aprobados · 6 descartados · 36 tareas de aprobación reconciliadas por el outbox ·
búsqueda semántica top similitud 0.630 · working set 30 ítems · dorada 6 consultas
(4 con top-5 estable, 2 vacías por filtro `high` — honesto) · 549 tests verdes.
