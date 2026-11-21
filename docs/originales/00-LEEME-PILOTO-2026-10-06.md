# Sentinel — LEEME del piloto (06-oct-2026)

**Vigente sobre el entregable del 02-sep-2026.** Los documentos en `docs/base/` siguen siendo
la referencia de detalle (requisitos completos, contexto técnico), pero **este conjunto de
documentos los enmienda** donde diga. Si algo aquí contradice `docs/base/`, vale esto.

---

## 1. Qué es Sentinel, en cinco líneas

Quant24 razona sobre estrategias con una jerarquía de agentes de IA. Esos agentes necesitan
contexto de mercado actualizado y ya evaluado. Sentinel hace ese trabajo de forma continua:
ingiere noticias de 4 fuentes abiertas, las normaliza, las categoriza por nivel y tipo de
riesgo con justificación auditable y lo expone por API. En la versión 1.1 suma **investigación
on-demand** (un agente investiga un enlace o palabra clave) y una **capa RAG** que vuelve
todo el corpus recuperable en lenguaje natural. Es la puerta de datos de Warren AI.

## 2. Decisiones nuevas del 06-oct-2026 (aprobadas por Xavier)

Estas tres decisiones cambian el entregable de septiembre. Ninguna es propuesta: están aprobadas.

1. **El panel no es una app aparte.** El frontend de Sentinel vive dentro de
   `quant24-platform` (el repo que sirve `quant24.io`), como secciones de `/app`.
   Ya no existe un React+Vite en Pages con autenticación propia. Detalle completo:
   [`01-INTEGRACION-QUANT24-PLATFORM-2026-10-06.md`](01-INTEGRACION-QUANT24-PLATFORM-2026-10-06.md).
2. **Dominio público de la API: `sentinel.quant24.io`.** Uso interno del equipo y de agentes
   Quant24 (Warren y cia.). `workers_dev: false` desde el primer día; el acceso humano va
   por API key, el del panel pasa por la plataforma.
3. **El repositorio vive en la forja propia, no en GitHub:** `forja.quant24.io/Quant24inc/quant24-sentinel`.
   La forja está detrás de Cloudflare Access (PIN por correo `@quant24.io`). Detalles de
   acceso y flujo de trabajo: [`02-REPO-FORJA-OPEN-CODE-2026-10-06.md`](02-REPO-FORJA-OPEN-CODE-2026-10-06.md).

## 2½. Entorno de desarrollo (08-oct-2026, aprobado por Xavier)

Ningún cambio vuelve a subir directo a producción para poder probarse:

| Entorno | URL | Cómo se despliega |
|---|---|---|
| **dev** | `https://[worker redactado]` | CI de la forja, en cada push a `master` con tests en verde |
| **producción** | `https://sentinel.quant24.io` | Promoción manual: `pnpm deploy` (solo Xavier) |

El aislamiento es por construcción (mismo patrón que Conserje y Escriba): Worker propio,
`[recurso interno]` (D1), `[recurso interno]` (KV), `[recurso interno]` y `[recurso interno]`
(R2), `[recurso interno]` (Queues) y **secretos propios** — nada compartido con producción.
El cron de ingesta (`*/15`) está vivo para ensayar conectores end-to-end; el de backup NO:
dev no escribe nunca al repo de backups de producción. Detalle de qué existe y cómo se
estrenó: `07-ESTADO-CODIGO-2026-10-06.md` (no publicado; ver README), sección
«Entorno de desarrollo».

## 3. Arquitectura en un diagrama

```
Fuentes externas (cron) ──┐
                          ├─> Ingesta ─> Normalización ─> Motor de riesgo ─> D1/R2
Enlace o palabra clave ───┘   (Iván)      (Iván)          (Mauricio)          │
   (agente on-demand)                                                        v
                                                          API (Worker Hono, Xavier)
                                                          ├─ /v1/news ...  <── API key ── Warren / agentes
                                                          └─ binding interno <── quant24-platform (Xavier sirve, Nicolás dibuja)
                                                                                    /app/riesgo, /app/admin/riesgo
```

Dos puntos de entrada (programado y on-demand), un solo pipeline. Nadie lee el estado interno
de otro: todos hablan por `NewsItem` y por los contratos de `[ruta interna]`.

## 4. El equipo y quién es quién

| Persona | Rol | Dueño de | Documento |
|---|---|---|---|
| **Iván** (v.rocco@...) | Ingesta y datos | Conectores, normalización, filtro de relevancia, esquema D1, adquisición/extracción, política de scraping | [`03-ROL-1-IVAN-INGESTA-2026-10-06.md`](03-ROL-1-IVAN-INGESTA-2026-10-06.md) |
| **Mauricio** (m.gatica@...) | Motor de riesgo y RAG | Reglas de clasificación, LLM de refuerzo, explicabilidad, troceado, Vectorize, API de recuperación | [`04-ROL-2-MAURICIO-MOTOR-RAG-2026-10-06.md`](04-ROL-2-MAURICIO-MOTOR-RAG-2026-10-06.md) |
| **Xavier** (xavier, CTO) | API y plataforma | API REST, auth por API key, RBAC, eventos, CI/CD, orquestador on-demand, backup | [`05-ROL-3-XAVIER-API-PLATAFORMA-2026-10-06.md`](05-ROL-3-XAVIER-API-PLATAFORMA-2026-10-06.md) |
| **Nicolás** (n.vasquez@...) | Frontend | **En `quant24-platform`**: feed, detalle, recalificación, pantallas de investigación y aprobación | [`06-ROL-4-NICOLAS-FRONTEND-PLATFORM-2026-10-06.md`](06-ROL-4-NICOLAS-FRONTEND-PLATFORM-2026-10-06.md) |

## 5. Dónde estamos en el cronograma (semana 9)

El Gantt (`quant24-sentinel/Carta Gantt.xlsx`) planeaba las fases 1-2 cerradas al llegar aquí.
**Realidad al 06-oct (noche): la parte de Xavier (Rol 3) ya existe y está probada.** El estado
exacto del código — qué está hecho, qué falta y qué NO existe — está en
`07-ESTADO-CODIGO-2026-10-06.md` (no publicado; ver README); léelo antes de construir
nada para no duplicar trabajo. Resumen del plan para el MVP:

| Ventana | Qué debe existir |
|---|---|
| **06–11 oct** (semana 9) | ✅ Monorepo, Worker, CI, migración 0001, API `/v1` con auth/RBAC/breaker/rate-limit (Xavier, ya hecho) · primer conector SEC EDGAR + normalización (Iván) · capa 1 de reglas esbozada (Mauricio) · contratos `dominio` completos (✅ ya están) |
| **12–18 oct** (semana 10, fecha del MVP) | 4 conectores al menos en esbozo con datos fluyendo (Iván), explicabilidad y versionado (Mauricio), feed `/app/riesgo` consumiendo la API real (Nicolás) |
| **19–25 oct** (fase 4) | Alertas, auditoría, backup verificado fuera de Cloudflare (Xavier), configuración de fuentes/umbrales en el panel (Nicolás) |
| **26 oct–22 nov** (fase 5) | Agente on-demand en Durable Objects (Xavier), búsqueda y extracción (Iván), RAG completo (Mauricio), pantallas de investigación y aprobación (Nicolás) |

Atraso declarado, sin disfraz: las fases 1-2 del Gantt se recuperan dentro de estas ventanas.

## 6. Reglas de la casa (aplican a los cuatro)

- **pnpm, nunca npm.** Hay hook que bloquea `npm install`.
- **`master` no se toca directo.** Ramas `[función interna]/tema`, PR en la forja, revisión, merge. Commits con
  autor `githubquant@quant24.io` (configurar `git config user.email` en el clone el día uno).
- **Todo recurso de Cloudflare con prefijo `sentinel-`**: `[recurso interno]` (D1), `[recurso interno]`
  y `[recurso interno]` (R2), `[recurso interno]` (Vectorize), `[recurso interno]` (KV),
  `[recurso interno]` (Queues). Nada comparte binding, base ni bucket con otro programa.
- **Secretos solo por `wrangler secret put`**, nunca en el código ni en el repo. El Worker se
  niega a servir (503) si falta uno.
- **Cómo se prueba:** tests con dobles en vitest (el doble debe ser al menos tan estricto como
  el real) más **un banco de ensayo por proveedor** (`[script interno]`) que golpea el
  proveedor real. Cada llamada real de esta casa ha encontrado un fallo que los tests no vieron.
- **Trampas ya pagas** (D1: 100 parámetros por consulta, `fetch.bind`, AI Gateway fuera del
  crédito, SEC EDGAR exige User-Agent, puertos 8787-8799 ocupados): detalle en
  `docs/base/05-arranque-tecnico.md` sección 4.

## 7. Por dónde empieza cada uno hoy

1. Todos: leer este archivo + `07-ESTADO-CODIGO` (qué ya existe — no lo reconstruyas) +
   `02-REPO-FORJA-OPEN-CODE` + el propio documento de rol.
2. Xavier: ✅ recursos y despliegue listos (06-oct: API en vivo en `sentinel.quant24.io`,
   migración aplicada, primera clave admin creada). Sigue con alertas y backup (fase 4).
3. Iván: conector SEC EDGAR (sin API key, formato estable) contra el contrato de `dominio`.
4. Mauricio: capa 1 de reglas sobre fixtures mientras no hay flujo real.
5. Nicolás: secciones `/app/riesgo` en `quant24-platform` contra la API con datos de fixture
   (o contra la API local: `wrangler dev` + `[archivo interno]`).
