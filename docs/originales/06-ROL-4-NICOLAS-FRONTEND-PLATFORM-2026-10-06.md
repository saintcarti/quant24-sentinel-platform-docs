# Rol 4 — Nicolás: Frontend (en `quant24-platform`) (06-oct-2026)

Lee antes: `00-LEEME-PILOTO`, `01-INTEGRACION`, `02-REPO-FORJA-OPEN-CODE`. El detalle
completo está en `docs/base/10-rol4-frontend.md`, **pero la parte del panel propio quedó
obsoleta** — este documento manda.

---

## La enmienda principal: tu código NO vive en este repo

El panel de Sentinel ya no es una app aparte. Tus pantallas son **secciones de
`quant24-platform`** (el repo que sirve `quant24.io`), en `[ruta interna]`:

| Ruta | Qué es | RF |
|---|---|---|
| `/app/riesgo` | Feed con filtros (fuente, categoría, nivel, activo, fechas, búsqueda) | RF-25 |
| `/app/riesgo/[id]` | Detalle: contenido normalizado, score con justificación completa, entidades, enlace a la fuente, historial de recalificaciones | RF-26 |
| `/app/riesgo/[id]` (acción) | Recalificación manual con motivo, auditada, sin sobrescribir | RF-13 (interfaz) |
| `/app/riesgo` (vista entrada) | Dashboard de panorama: distribución por categoría/nivel, tendencia, últimas alertas Alto/Crítico — revisión de 30 segundos | RF-29 |
| `/app/admin/riesgo/fuentes` | Activar/desactivar fuentes, última ejecución, tasa de error, estado del circuit breaker | RF-27 |
| `/app/admin/riesgo/umbrales` | Configuración de umbrales y reglas de alerta, sin despliegue nuevo | RF-28 |
| Fase 5 | Pantalla de investigación con progreso en vivo + compuerta de aprobación humana | RF-49–51 |

## Cómo trabajas

1. **Repo de trabajo:** `quant24-platform` (local de Xavier o tu clon), ramas
   `f8/riesgo-feed` o la fase que abra la plataforma. Sus reglas mandan: `pnpm`, `main`/`master`
   con PR, `[paquete interno]` importando por ruta profunda (`[paquete interno]/components/primitives/button`,
   nunca el barrel), **dark-only** (nada de `dark:`), todo número visible dentro de
   `SourceBadge` con `source` obligatorio, aviso CFTC verbatim donde toque.
2. **Datos:** no tocas D1 de Sentinel ni copias tipos a mano. Consumes la API de Xavier vía
   el service binding `SENTINEL` desde el servidor (Next.js/RSC) y tipas las respuestas con
   **`[paquete interno]`** (publicado en el registry npm de `forja.quant24.io`).
   Mientras la API no exista: fixtures deterministas etiquetados `synthetic` (regla de la
   plataforma).
3. **Identidad:** entras por la sesión de la plataforma (Auth.js). No hay login de Sentinel.
   El RBAC de pantalla (quién ve qué) lo resuelve la plataforma; recalificar exige el rol
   Analista, administrar exige Admin.
4. **Estados en vivo que renderizas literal** (acordados con Xavier): `encolado`,
   `planificando`, `buscando`, `descargando`, `evaluando`, `completado`,
   `completado_parcial`, `fallido`, `cancelado`.

## Qué construye AHORA (semana 9 → MVP 18-oct)

1. **`/app/riesgo` con filtros contra la API real o fixture** — es tu entregable del MVP.
2. **`/app/riesgo/[id]`** con detalle y flujo de recalificación (puede quedar con fixture).
3. **Definir con Xavier el formato exacto** de las respuestas que consumes (PR en
   `dominio` si falta un campo — es decisión de los cuatro, no tuya sola).

## Dependencias

| Con quién | Qué |
|---|---|
| Xavier | Tu proveedor de datos: API + contratos + key de integración server-side |
| Mauricio | El contenido de justificación/entidades que muestras viene de su motor; la compuerta de aprobación dispara su indexación |
| Iván | Atribución de GDELT visible en la ficha |

## Frase para tu agente OpenCode

> "Estoy en el repo quant24-platform. Lee su CLAUDE.md y luego
> docs/01-INTEGRACION-QUANT24-PLATFORM-2026-10-06.md del repo sentinel y
> docs/06-ROL-4-NICOLAS-FRONTEND-PLATFORM-2026-10-06.md. Soy Nicolás, Rol 4. Construye la
> sección /app/riesgo (feed + detalle) en [ruta interna] consumiendo la API de Sentinel por el
> service binding SENTINEL (o fixture synthetic si aún no existe), con [paquete interno], SourceBadge
> y dark-only. No toques packages de Sentinel ni su D1."
