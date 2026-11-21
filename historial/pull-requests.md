# Pull requests documental — forja

Fuente: API de la forja para `Quant24inc/quant24-sentinel` y `Quant24inc/quant24-platform`, consulta del 20-nov-2026. **50 pull requests**: 31 de Sentinel y 19 de Platform. Se registra metadata, no código ni diffs. Los enlaces requieren acceso autorizado.

A diferencia del historial de commits, cada pull request sí identifica a quien lo creó. La fusión la realiza el administrador del repositorio (`qfd-admin`) conforme al flujo del equipo: pull request como único canal de cambio, revisión y CI verde antes de fusionar, detrás de Cloudflare Access. La atribución individual del trabajo está en [Aporte por integrante](../docs/06-aporte-por-integrante.md).

Fechas UTC en formato año-mes-día. «Cerrado» significa cerrado sin fusión.

## Sentinel

31 pull requests: 26 fusionados y 5 cerrados sin fusión.

| PR | Autor | Creado | Fusionado | Estado | Título |
|---|---|---|---|---|---|
| [#1](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/1) | `qfd-admin` | 2026-10-07 | 2026-10-07 | fusionado | rol3/alertas-backup |
| [#2](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/2) | `qfd-admin` | 2026-10-07 | 2026-10-07 | fusionado | rol3/hotfix-fetch |
| [#3](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/3) | `qfd-admin` | 2026-10-07 | 2026-10-07 | fusionado | ci: quitar version fija de pnpm/action-setup |
| [#4](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/4) | `qfd-admin` | 2026-10-07 | 2026-10-07 | fusionado | rol3/consumidor-fuentes |
| [#5](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/5) | `qfd-admin` | 2026-10-08 | 2026-10-08 | fusionado | ci: quitar version fija de pnpm/action-setup - manda [función interna] del package.json |
| [#6](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/6) | `qfd-admin` | 2026-10-08 | 2026-10-08 | fusionado | feat: canal email de alertas con Resend (RF-23) y redespacho de todos los canales - 98 tests |
| [#7](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/7) | `qfd-admin` | 2026-10-08 | 2026-10-08 | fusionado | feat: paquete contracts publicable - build tsc a dist y [función interna] al registry de la forja |
| [#8](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/8) | `qfd-admin` | 2026-10-08 | 2026-10-08 | fusionado | ci: deploy de master a sentinel-api - se omite sin CLOUDFLARE_API_TOKEN en la forja |
| [#9](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/9) | `qfd-admin` | 2026-10-08 | 2026-10-08 | fusionado | feat: entorno de desarrollo sentinel-api-dev |
| [#10](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/10) | `MauricioQ24` | 2026-10-08 | 2026-10-09 | fusionado | feat: motor de riesgo capa 1 (reglas + tono GDELT) con suite de regresión - 115 tests |
| [#11](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/11) | `qfd-admin` | 2026-10-10 | 2026-10-10 | fusionado | feat/portada-fase1-ingesta_X_L |
| [#12](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/12) | `MauricioQ24` | 2026-11-17 | 2026-11-17 | fusionado | feat: capa RAG P1 - contratos, troceado chunking-v1 y repositorio de trozos - 16 tests |
| [#13](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/13) | `MauricioQ24` | 2026-11-17 | — | cerrado | feat: capa 2 del motor - refuerzo LLM (RF-11) con interruptor y defensas RNF-26 - 16 tests |
| [#14](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/14) | `MauricioQ24` | 2026-11-17 | — | cerrado | feat: enrutamiento del cron por event.cron - listo para el working set (P4) - 4 tests |
| [#15](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/15) | `MauricioQ24` | 2026-11-17 | — | cerrado | feat: rag P2 - indexación del corpus en Vectorize con candado RF-51 (23 tests) |
| [#16](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/16) | `qfd-admin` | 2026-11-17 | 2026-11-17 | fusionado | integrate/sentinel-open-prs |
| [#17](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/17) | `qfd-admin` | 2026-11-17 | 2026-11-17 | fusionado | develop |
| [#18](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/18) | `MauricioQ24` | 2026-11-17 | 2026-11-18 | fusionado | feat: rag P3 - API de recuperación con filtros RF-45 y working set (18 tests) |
| [#19](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/19) | `MauricioQ24` | 2026-11-17 | 2026-11-18 | fusionado | feat: rag P4 - curación del working set con cron RF-47 (8 tests) |
| [#20](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/20) | `MauricioQ24` | 2026-11-17 | 2026-11-18 | fusionado | feat: rag P5 - evaluación RNF-28 con set versionado y script de dorada (9 tests) |
| [#21](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/21) | `Roccoer1` | 2026-11-19 | 2026-11-19 | fusionado | feat: habilitar ingesta real en dev - vars de conectores y seed de fuentes (rol 1) |
| [#22](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/22) | `MauricioQ24` | 2026-11-19 | 2026-11-19 | fusionado | fix: doble de Indice del working-set implementa consultar - typecheck develop |
| [#23](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/23) | `MauricioQ24` | 2026-11-19 | — | cerrado | feat(rag): feedback RF-48 + herramienta MCP [herramienta interna] RF-46 - 18 tests |
| [#24](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/24) | `qfd-admin` | 2026-11-19 | 2026-11-19 | fusionado | feat: desbloqueos Fase 5 — aprobaciones, RAG seguro e infraestructura dev |
| [#25](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/25) | `qfd-admin` | 2026-11-19 | — | cerrado | feat: desbloqueos Fase 5 — aprobaciones, RAG seguro e infraestructura dev |
| [#26](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/26) | `qfd-admin` | 2026-11-19 | 2026-11-19 | fusionado | fix(ci): evitar deploy verde sin ejecutar por filtro de paquete incorrecto |
| [#27](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/27) | `qfd-admin` | 2026-11-19 | 2026-11-19 | fusionado | fix(ingesta): corregir FK con entidades legacy detectada en ensayo real dev |
| [#28](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/28) | `qfd-admin` | 2026-11-19 | 2026-11-19 | fusionado | develop |
| [#29](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/29) | `MauricioQ24` | 2026-11-19 | 2026-11-20 | fusionado | feat(rag): feedback RF-48 + herramienta MCP [herramienta interna] RF-46 - 18 tests (re-presenta el 23) |
| [#30](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/30) | `MauricioQ24` | 2026-11-20 | 2026-11-20 | fusionado | feat(rag): primera corrida dorada RNF-28 contra el dev real - E2E F5 |
| [#31](https://forja.quant24.io/Quant24inc/quant24-sentinel/pulls/31) | `qfd-admin` | 2026-11-20 | 2026-11-20 | fusionado | develop |

Autores: `qfd-admin` 18, `MauricioQ24` 12, `Roccoer1` 1.

## Platform

19 pull requests: 16 fusionados y 3 abiertos.

| PR | Autor | Creado | Fusionado | Estado | Título |
|---|---|---|---|---|---|
| [#1](https://forja.quant24.io/Quant24inc/quant24-platform/pulls/1) | `Nicolas` | 2026-10-08 | 2026-10-08 | fusionado | F8: feed de riesgo /app/riesgo con recalificación de administración |
| [#2](https://forja.quant24.io/Quant24inc/quant24-platform/pulls/2) | `qfd-admin` | 2026-10-08 | — | abierto | el 2-oct-2026 la preview servia un bundle con la site key REAL inlineada: el shell que la |
| [#3](https://forja.quant24.io/Quant24inc/quant24-platform/pulls/3) | `qfd-admin` | 2026-10-08 | — | abierto | plan/f8-auditoria-integral |
| [#4](https://forja.quant24.io/Quant24inc/quant24-platform/pulls/4) | `qfd-admin` | 2026-10-08 | 2026-10-08 | fusionado | F8 T7-b: la sonda auth del smoke abre APP_SIGNIN_OPEN ella misma |
| [#5](https://forja.quant24.io/Quant24inc/quant24-platform/pulls/5) | `qfd-admin` | 2026-10-08 | — | abierto | sentinel: binding SENTINEL y proxy /app/api/riesgo con RBAC de dos capas |
| [#6](https://forja.quant24.io/Quant24inc/quant24-platform/pulls/6) | `qfd-admin` | 2026-11-17 | 2026-11-17 | fusionado | plan/opennext-next15-build |
| [#7](https://forja.quant24.io/Quant24inc/quant24-platform/pulls/7) | `qfd-admin` | 2026-11-17 | 2026-11-17 | fusionado | Regenerar tipos de Worker con Wrangler actualizado |
| [#8](https://forja.quant24.io/Quant24inc/quant24-platform/pulls/8) | `qfd-admin` | 2026-11-17 | 2026-11-17 | fusionado | Formatear integración del feed de riesgo |
| [#9](https://forja.quant24.io/Quant24inc/quant24-platform/pulls/9) | `qfd-admin` | 2026-11-17 | 2026-11-17 | fusionado | Actualizar cifra de fronteras cliente medida |
| [#10](https://forja.quant24.io/Quant24inc/quant24-platform/pulls/10) | `qfd-admin` | 2026-11-17 | 2026-11-17 | fusionado | Usar HTTPS para instalar deps de Chromium |
| [#11](https://forja.quant24.io/Quant24inc/quant24-platform/pulls/11) | `qfd-admin` | 2026-11-17 | 2026-11-17 | fusionado | Quitar sudo del ajuste de mirrors apt |
| [#12](https://forja.quant24.io/Quant24inc/quant24-platform/pulls/12) | `qfd-admin` | 2026-11-17 | 2026-11-17 | fusionado | fix/pnpm-test-generated-assets |
| [#13](https://forja.quant24.io/Quant24inc/quant24-platform/pulls/13) | `qfd-admin` | 2026-11-17 | 2026-11-17 | fusionado | Evitar apt HTTP al instalar Chromium para QA |
| [#14](https://forja.quant24.io/Quant24inc/quant24-platform/pulls/14) | `qfd-admin` | 2026-11-17 | 2026-11-17 | fusionado | Instalar Stripe CLI sin sudo en QA |
| [#15](https://forja.quant24.io/Quant24inc/quant24-platform/pulls/15) | `qfd-admin` | 2026-11-17 | 2026-11-17 | fusionado | Elegir Stripe CLI según arquitectura del runner |
| [#16](https://forja.quant24.io/Quant24inc/quant24-platform/pulls/16) | `qfd-admin` | 2026-11-18 | 2026-11-18 | fusionado | Fix consent a11y and stabilize QA page state |
| [#17](https://forja.quant24.io/Quant24inc/quant24-platform/pulls/17) | `qfd-admin` | 2026-11-18 | 2026-11-18 | fusionado | fix/qa-integral-workerd |
| [#18](https://forja.quant24.io/Quant24inc/quant24-platform/pulls/18) | `qfd-admin` | 2026-11-18 | 2026-11-18 | fusionado | fix: cerrar procesos QA y subir evidencia por origen interno de Forja |
| [#19](https://forja.quant24.io/Quant24inc/quant24-platform/pulls/19) | `qfd-admin` | 2026-11-18 | 2026-11-19 | fusionado | CI: verificar primero el transporte de artefactos |

Autores: `qfd-admin` 18, `Nicolas` 1.

## Notas de lectura

- Los PR #13, #14 y #15 de Sentinel figuran cerrados sin fusión porque su contenido se integró a `develop` mediante el PR #16 (integración de capa 2, cron e indexación). El informe final los cuenta como fusionados por integración.
- El PR #23 se cerró por conflicto de fusión y se re-presentó como #29. El PR #25 es una variante del #24 cerrada sin fusión.
- Los PR #2, #3 y #5 de Platform permanecen abiertos como ramas mantenidas (auditoría integral y proxy de riesgo). El binding SENTINEL verificado en producción se documenta en [docs/03-integracion.md](../docs/03-integracion.md) y [docs/04-estado-verificado.md](../docs/04-estado-verificado.md).

## Nota sobre el corte del informe

El informe final del Capstone registra, al cierre de su redacción, 30 pull requests de Sentinel «revisados bajo un único flujo (26 fusionados y 4 cerrados por integración o sustitución)». Esta tabla, consultada después, muestra 31: el PR #31 —la promoción final `develop`→`master` del 20-nov-2026, 03:12 UTC— quedó fusionada con posterioridad a ese corte del informe.
