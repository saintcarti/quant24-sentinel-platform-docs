> **Portado** al mainline el 2026-10-09 desde la rama `feature/fase-1-ingesta` de Iván (tip `9a6a1a8`,
> tag `rescate/fase-1-ingesta-2026-10-09`). Ver `08-PORTADA-FASE1-IVAN-2026-10-09.md`.

# Propuesta: Dead Letter Queue para `[recurso interno]` — PENDIENTE DE APROBACIÓN

**Estado:** propuesta. **No implementada** y no incluida en `[archivo interno]`.

## 1. ¿La contempla la arquitectura aprobada?

No. Revisada la documentación original (2026-10-09):

- Arranque técnico §1 y §2.3: los recursos de Sentinel son `[recurso interno]`, `[recurso interno]`, `[recurso interno]`,
  `[recurso interno]`, `[recurso interno]` y `[recurso interno]`. No aparece ninguna DLQ.
- Requisitos §7 / spec v1.1: "Queues para lotes, Cron Triggers…, Workflows para el pipeline multi-paso con
  reintentos duraderos". Los reintentos duraderos se asignan a **Workflows**, no a una DLQ.
- No hay ninguna mención a *dead letter*, DLQ ni cola de fallidos.

Por eso, siguiendo la regla de no ampliar la arquitectura sin aprobación, **no se incorporó**.

## 2. Riesgo observado

En el ensayo local en workerd (2026-10-09, `[ruta interna]`), un lote de 25
ítems falló 3 veces seguidas por un defecto (el límite de 5 términos por SELECT compuesto de D1, ya
corregido). Agotó `max_retries: 3` y **Cloudflare Queues lo descartó**, porque no hay DLQ: ese mensaje
no queda en ningún sitio.

En ese caso concreto, los 25 comunicados se recuperaron en el ciclo siguiente, porque el feed de la
Fed y el del ECB devuelven siempre sus últimos 15 ítems. **Esto no garantiza la recuperación en
general**:

- GDELT consulta una ventana móvil (1 h, y como máximo 250 artículos). Lo que sale de la ventana no se
  vuelve a pedir.
- El feed `getcurrent` de SEC muestra los últimos 100 filings; en horas de mucho volumen, un filing
  puede salir del feed antes del siguiente ciclo exitoso.
- Un mensaje que falla por un defecto persistente fallará igual en cada reingesta, hasta que se
  corrija el defecto, y para entonces el ítem puede haber salido de la fuente.

Impacto: pérdida silenciosa de ítems ante un fallo persistente de D1 o un defecto del consumidor. Hoy
solo queda el log `lote_fallido` de cada intento.

## 3. Propuesta mínima

1. Crear una cola `[recurso interno]` (con el prefijo de la casa).
2. En el consumidor de `[recurso interno]`, añadir `"dead_letter_queue": "[recurso interno]"` en
   `[archivo interno]`.
3. **Sin consumidor** de la DLQ en la Fase 1: los mensajes quedan retenidos (4 días por defecto en
   Queues) para inspección y reproceso manual. El reproceso puede hacerse con un consumidor temporal o
   reenviando los mensajes a `[recurso interno]`. El mensaje ya es JSON versionado (`v: 1`) e idempotente,
   así que reprocesarlo no duplica nada.
4. Documentar en el runbook cómo inspeccionarla (`wrangler queues` / panel) y añadir al verificador de
   72 h la comprobación "DLQ vacía".

Cambios de código: **ninguno** (sólo configuración y un recurso nuevo).

## 4. Impacto y dependencias

| Aspecto | Impacto |
|---|---|
| Recursos | +1 Queue (`[recurso interno]`), dentro del crédito de Cloudflare |
| Costo | Despreciable: solo recibe mensajes cuando un lote agota sus reintentos |
| Código | Ninguno en la Fase 1 |
| Operación | Alguien tiene que mirar la DLQ: sin vigilancia, solo convierte una pérdida en un retraso |
| Dependencias | Acceso a la cuenta de Cloudflare (el mismo bloqueo que el resto de los recursos) |

## 5. Alternativas

| Alternativa | Pros | Contras |
|---|---|---|
| **A. DLQ (esta propuesta)** | Mecanismo nativo de Queues; cero código | Un recurso más; requiere vigilancia |
| B. Subir `max_retries` y `retry_delay` | Solo configuración, sin recursos nuevos | Retrasa la pérdida, no la evita, ante un fallo persistente |
| C. Guardar el lote fallido en R2 (`[recurso interno]`) desde el consumidor | Sin recursos nuevos | Código nuevo; el TTL de 30 días de ese bucket no está pensado para esto |
| D. Workflows ("reintentos duraderos" de la arquitectura) | Alineado con el texto del documento | Rediseño del consumidor; excede la Fase 1 |
| E. No hacer nada y confiar en la reingesta | Nada que construir | Pérdida no garantizada como recuperable (§2) |

**Recomendación:** A, junto con un `retry_delay` explícito en el consumidor. Requiere tu aprobación.
