# Rol 3 — Xavier: API y Plataforma (06-oct-2026)

Lee antes: `00-LEEME-PILOTO`, `01-INTEGRACION`, `02-REPO-FORJA-OPEN-CODE`. El detalle
completo está en `docs/base/09-rol3-api-plataforma.md`. Este documento manda.

---

## Tu misión en una frase

Exponer Sentinel a agentes de IA por una API segura y observable, y sostener toda la
plataforma: CI, logging, secretos, despliegue y el orquestrador del agente on-demand.

## Dueño de

- Esqueleto del Worker (Hono), pipeline CI/CD (RNF-11) y patrón de logging estructurado con
  correlación (RNF-13) — **lo que los otros tres heredan desde su primer commit**.
- API REST con filtros combinables (RF-18) y formato legible-para-agente (RF-19).
- Autenticación por API key con scope solo-lectura por defecto (RF-22) — **middleware
  reutilizable, lo consume Mauricio para su API de recuperación**.
- RBAC en API y base, nunca solo en pantalla (RF-31, RNF-09); rate-limiting (RNF-08);
  secretos (RNF-06); TLS (RNF-07).
- Orquestación: Cron Triggers, circuit breaker por fuente (RF-03), salud por fuente (RNF-14).
- Alertas: reglas por umbral, canales email/webhook/panel, registro (RF-23, RF-24) — fase 4.
- Backup fuera de Cloudflare, restaurado de verdad (RNF-05) — fase 4, con Iván.
- **Fase 5:** orquestrador del agente on-demand en Durable Objects — entrada única (RF-34),
  plan visible (RF-35), adaptador de búsqueda (RF-36 sobre la interfaz de Iván), trabajo
  asíncrono con estados (RF-39), traza completa (RF-41), límites y presupuesto (RF-42),
  aislamiento/prompt injection (RNF-26), latencia (RNF-23).
- **Integración con `quant24-platform`:** service binding `SENTINEL`, publicación de
  `[paquete interno]` en el registry npm de la forja, API key de integración para la
  plataforma, ruta `sentinel.quant24.io`.

## Qué construye AHORA (semana 9 → MVP 18-oct)

1. **Fase 0 completa (deuda del Gantt: tareas 1.1 y 1.4):** monorepo, Worker con
   `[archivo interno]` (`compatibility_date` 2026-08-11, `nodejs_compat`, observability,
   `workers_dev: false`), recursos `sentinel-*`, migraciones 0001 con las 15 entidades, CI
   (test + typecheck + deploy de master), patrón de logging, "hola mundo" punta a punta.
2. **Middleware de auth (RF-22) + RBAC (RF-31/RNF-09)** diseñado para ser importado, no
   privado.
3. **`GET /v1/news` (RF-18)** con filtros combinables (fecha, símbolo, categoría, nivel,
   fuente) y **serializador legible-para-agente (RF-19)**.
4. **Circuit breaker por fuente (RF-03) + salud (RNF-14)** — tarea 2.4 atrasada del Gantt.
5. **Rate-limit por API key (RNF-08)** y envolvente de errores tipada.
6. **Recursos Cloudflare y ruta `sentinel.quant24.io`** — solo tú creas recursos.

## Estado al 06-oct (noche)

Los puntos 1-5 del listado anterior **ya están construidos y probados**: 47 tests en vitest,
typecheck estricto verde, CI en la forja. El detalle pieza por pieza está en
`07-ESTADO-CODIGO-2026-10-06.md` (no publicado; ver README). Además, **Fase 0 y
despliegue completados el 06-oct**: recursos `sentinel-*` creados, Worker en vivo en
`sentinel.quant24.io`, migración 0001 aplicada a la D1 remota (sin seed), primera clave
admin creada (`key_prod_admin_01`, texto plano solo en la máquina de Xavier). Queda de tu lado:

1. Fase 4: alertas (RF-23/24) y backup fuera de Cloudflare (RNF-05).
2. Fase 5: orquestador on-demand en Durable Objects.
3. Crear las claves de integración para Warren y `quant24-platform` por `POST /v1/keys`.
4. Pendiente de acceso: publicar `[paquete interno]` en el registry de la forja y crear
   los usuarios de la forja para Iván, Mauricio y Nicolás (requiere recuperar el admin de Gitea).

## Dependencias

| Con quién | Qué |
|---|---|
| Iván | Llama sus conectores desde el cron; usa su `[adaptador]`/`DomainPolicy` en fase 5 |
| Mauricio | Le prestas el middleware de auth; su capa 2 se engancha a tu pipeline |
| Nicolás | Le das la API con datos (reales o fixture) y el paquete de contratos; su UI consume tu binding |

## Enmiendas al documento base

- Repo en la forja; sin panel propio; en su lugar, service binding + paquete de contratos +
  API key de integración para `quant24-platform`.
- El Gantt te asigna además 2.4 (cron + circuit breaker), atrasado — se recupera esta semana.

## Frase para tu agente OpenCode

> "Lee docs/00-LEEME-PILOTO-2026-10-06.md, docs/01-INTEGRACION..., docs/02-... y
> docs/05-ROL-3-XAVIER-API-PLATAFORMA-2026-10-06.md. Soy Xavier, Rol 3 — API y Plataforma.
> Construye el esqueleto del Worker en [ruta interna] con Hono, [archivo interno], CI, migraciones
> de D1 en [ruta interna], middleware de auth por API key y GET /v1/news con filtros y
> formato para agente. Tests con dobles estrictos. No toques conectores ni el motor de riesgo."
