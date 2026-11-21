# Aporte por integrante

Corte de consulta: 20-nov-2026. Este documento atribuye el trabajo individual del Capstone APT (Duoc UC, noviembre de 2026) y resume la evidencia verificable por integrante. Complementa [Trabajo, commits y evidencia](05-trabajo-y-evidencia.md); la tabla completa de los 50 pull requests está en [historial/pull-requests.md](../historial/pull-requests.md).

## Cómo se atribuye el trabajo

El equipo trabajó con dos reglas operativas que hacen que el autor de un commit no identifique al autor del trabajo:

1. **Identidad de commits compartida.** La mayoría de los commits de los repositorios de producto se firmó con la identidad técnica compartida `Quant24 <githubquant@quant24.io>` o con la cuenta administrativa `qfd-admin`, no con la identidad personal de cada integrante.
2. **Integración centralizada.** Todo cambio entra por pull request y lo fusiona el administrador del repositorio, detrás de Cloudflare Access, solo con CI verde.

La unidad que sí identifica autoría individual es el **pull request**: quien lo crea queda registrado como autor en la forja. Cuando el PR lo abrió el administrador, el informe final del Capstone consigna la equivalencia en su anexo 8.1 — «qfd-admin (Xavier)», «Roccoer1 (Iván)» — y describe el trabajo real de cada rama en su anexo 8.3. Este documento sigue esa misma atribución.

Cada integrante firmó además documentos de avance en el repositorio de Sentinel (`docs/00` a `docs/23`), con fecha y rol en el nombre del archivo; son la evidencia de avance con fecha que cita el informe.

## Resumen por integrante

| Integrante | Rol | Usuario forja | PRs creados | Documentos de avance |
|---|---|---|---|---|
| Xavier Liberona | Rol 3 — API, plataforma e infraestructura; administrador del repositorio | `qfd-admin` | 18 en Sentinel, 18 en Platform | docs 18–22 |
| Nicolás Vásquez | Rol 4 — panel y frontend de la plataforma | `Nicolas` | 1 en Platform (#1) | — |
| Mauricio Gatica | Rol 2 — motor de riesgo y capa RAG | `MauricioQ24` | 12 en Sentinel | docs 12–17 y 23 |
| Iván Rocco | Rol 1 — ingesta y datos | `Roccoer1` | 1 en Sentinel (#21) | docs 08–11 |

Nicolás no tiene documentos de avance en Sentinel porque su entrega vivió en la plataforma Quant24 (rama de riesgo y PR #1); ver su sección.

## Xavier Liberona — Rol 3: API, plataforma e infraestructura

Administrador del repositorio: creó sus PR con la cuenta `qfd-admin` y fusionó los del resto del equipo tras la revisión.

API v1 con RBAC y auditoría, alertas por correo con Resend, paquete contracts publicable, entorno de desarrollo y CI/CD (PRs #1 a #9); fusión de la portada de la Fase 1 —trabajo de Iván— vía #11; integración de la capa 2, el cron y la indexación (#16/#17); desbloqueos de la Fase 5: aprobaciones RF-51, RAG seguro e infraestructura dev con índices Vectorize, bindings y crons (#24, con la variante #25 cerrada sin fusión); correcciones de producción detectadas por el ensayo real (#26 y #27); promoción a producción (#28) y promoción final `develop`→`master` posterior al corte del informe (#31); runtime del agente de investigación (migración 0005). En la plataforma: binding SENTINEL y pasada de calidad integral.

### Pull requests de Sentinel (cuenta `qfd-admin`)

| PR | Fusión | Contenido |
|---|---|---|
| #1–#9 | 07–08/10 | API con RBAC y auditoría; alertas con Resend (RF-23); paquete contracts; entorno `sentinel-api-dev`; CI/CD |
| #11 | 10/10 | Fusión de la portada de la Fase 1 (trabajo de Rol 1; migración 0003) |
| #16/#17 | 17/11 | Integración de capa 2, cron e indexación; sincronización develop |
| #24 | 19/11 | Desbloqueos Fase 5: aprobaciones RF-51, RAG seguro, infraestructura dev |
| #25 | — | Variante del #24, cerrada sin fusión |
| #26/#27 | 19/11 | Correcciones de producción del ensayo real (filtro de CI, FK legacy) |
| #28 | 19/11 | Promoción develop → master (producción) |
| #31 | 20/11 | Promoción final posterior al corte del informe |

### Pull requests de Platform (cuenta `qfd-admin`)

| PR | Fusión | Contenido |
|---|---|---|
| #4 | 08/10 | Sonda auth del smoke (APP_SIGNIN_OPEN) |
| #5 | abierto | Binding SENTINEL y proxy `/app/api/riesgo` con RBAC de dos capas |
| #6–#19 | 17–19/11 | Pasada de calidad integral: build OpenNext/Next15, tipos con Wrangler, integración del feed de riesgo, fronteras cliente, CI/QA (Chromium, Stripe CLI, sudo, workerd), consentimiento y accesibilidad, evidencia por origen interno |

La pasada de calidad contó con la participación de Nicolás (accesibilidad, consentimiento, estabilización), conforme al anexo 8.3 del informe. Los PR #2 y #3 (auditoría integral) permanecen abiertos como ramas mantenidas.

### Otros registros

- Documentos 18 a 22 en Sentinel: alcance vigente, investigación on-demand, desbloqueo de infra F5, handoff F5, infra verificada en Cloudflare.
- Migración 0005 (runtime del agente de investigación).

## Nicolás Vásquez — Rol 4: panel y frontend de la plataforma

Feed de riesgo y administración de fuentes y umbrales en la plataforma Quant24, con recalificación, tras el proxy con RBAC de dos capas; consumo del entorno de desarrollo de Sentinel en vista previa; estabilización de accesibilidad, consentimiento y CI en la pasada de calidad integral de la plataforma.

### Pull requests

| Repo | PR | Fusión | Contenido |
|---|---|---|---|
| Platform | #1 | 08/10 | F8: feed de riesgo `/app/riesgo` con recalificación de administración |

Su trabajo en la pasada de calidad de noviembre (accesibilidad, consentimiento, estabilización de CI) entró por los PR #6–#19 de Platform, creados desde la cuenta administrativa. Referencia adicional del informe: rama de riesgo de la plataforma.

## Mauricio Gatica — Rol 2: motor de riesgo y capa RAG

Capa 1 del motor con 26 reglas ES/EN y tono GDELT (#10); plan RAG P0–P5 con medición de mil embeddings; capa 2 con refuerzo LLM, interruptor y defensas (#13); enrutamiento del cron (#14); indexación en Vectorize con candado RF-51 (#15); API de recuperación con filtros RF-45 y working set (#18); curación del working set con cron RF-47 (#19); evaluación RNF-28 con set versionado y script de dorada (#20); feedback RF-48 y herramienta MCP `[herramienta interna]` (#23, re-presentado como #29 con migración 0007); primera corrida dorada contra el dev real (#30). Además: curación del corpus real y verificación E2E.

### Pull requests de Sentinel (usuario `MauricioQ24`)

| PR | Estado | Contenido |
|---|---|---|
| #10 | fusionado 09/10 | Motor capa 1: reglas + tono GDELT, suite de regresión |
| #12 | fusionado 17/11 | RAG P1: contratos, troceado chunking-v1, repositorio de trozos |
| #13 | cerrado; integrado vía #16 | Capa 2: refuerzo LLM (RF-11) con interruptor y defensas RNF-26 |
| #14 | cerrado; integrado vía #16 | Enrutamiento del cron por event.cron |
| #15 | cerrado; integrado vía #16 | RAG P2: indexación en Vectorize con candado RF-51 |
| #18 | fusionado 18/11 | RAG P3: API de recuperación con filtros RF-45 y working set |
| #19 | fusionado 18/11 | RAG P4: curación del working set con cron RF-47 |
| #20 | fusionado 18/11 | RAG P5: evaluación RNF-28 con set versionado y script de dorada |
| #22 | fusionado 19/11 | Fix del doble índice del working set |
| #23 | cerrado; sustituido por #29 | Feedback RF-48 + herramienta MCP [herramienta interna] |
| #29 | fusionado 20/11 | Re-presentación del #23 (migración 0007) |
| #30 | fusionado 20/11 | Primera corrida dorada RNF-28 contra el dev real (E2E F5) |

### Otros registros

- Documentos 12 a 17 (plan RAG, capa 2, indexación, P3 API, P4 working set, P5 evaluación) y 23 (feedback RAG + MCP).
- 12 commits firmados con su nombre personal en `develop`; el resto de su trabajo entró con la identidad compartida del equipo.
- Set de evaluación dorado: `consultas.json` y `dorada.json` (PR #30; copias publicadas en `docs/evidencia/evaluacion-rag/`).

## Iván Rocco — Rol 1: ingesta y datos

Los cuatro conectores con fixtures reales, normalización al esquema canónico, deduplicación atómica y resiliencia (reintentos con Retry-After, limitador de tasa, salud por corrida), entregados tras el rescate como la portada de la Fase 1 (migración 0003, fusionada en #11); habilitación de la ingesta real en dev (PR #21: variables de conectores y sembrado de fuentes); fuente de ensayo con RSS de bancos centrales que llenó el corpus real de la verificación; tag de rescate.

### Pull requests

| Repo | PR | Estado | Contenido |
|---|---|---|---|
| Sentinel | #21 | fusionado 19/11 | Ingesta real en dev: vars de conectores y sembrado de fuentes (rol 1) |
| Sentinel | #11 | fusionado 10/10 vía `qfd-admin` | Portada de la Fase 1: su rama `feat/portada-fase1-ingesta` con la migración 0003 |

### Otros registros

- Documentos 08 a 11: portada de la Fase 1, estado de build, protocolo de 72 horas, propuesta de DLQ.
- Migración 0003 (ingesta) y tag de rescate.
- La fuente de ensayo con RSS de bancos centrales alimentó el corpus real usado en la verificación E2E.

## Lectura honesta de los números

- La identidad de commits compartida y la fusión centralizada implican que el conteo de commits por persona no es evidencia de trabajo individual; por eso este repositorio publica los pull requests con autor.
- Dos integrantes tienen un único PR con usuario personal (Nicolás, Iván). El resto de su trabajo entró por las ramas descritas en el informe (anexo 8.3) y por documentos con fecha firmados con su rol.
- Los PR cerrados sin fusión no son trabajo perdido: #13, #14 y #15 se integraron vía #16, y #23 se re-presentó como #29.
