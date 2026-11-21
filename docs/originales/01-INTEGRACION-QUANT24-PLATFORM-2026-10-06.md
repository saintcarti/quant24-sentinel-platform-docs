# Integración de Sentinel con `quant24-platform` (06-oct-2026)

**Decisión aprobada por Xavier (CTO) el 06-oct-2026.** Este documento sustituye, para todo
efecto práctico, la parte del entregable del 02-sep-2026 que mandaba construir un panel
independiente en Cloudflare Pages con autenticación propia.

---

## 1. La decisión en una frase

El frontend de Sentinel no es una app separada: son secciones dentro de la web app de
`quant24-platform` (`/app/riesgo`), consumiendo la API de Sentinel por la red interna de
Cloudflare — y los humanos entran con la sesión que la plataforma ya tiene.

## 2. Por qué (fundamento)

El entregable original mandaba: panel React+Vite en Pages, usuarios propios con email +
contraseña (RF-32), roles en el panel (RF-31), design system propio (RF-30). Al 06-oct-2026,
`quant24-platform` ya tiene todo eso resuelto y con gates firmados:

| Lo que el panel separado pedía | Lo que `quant24-platform` ya tiene |
|---|---|
| Autenticación de personas (RF-32: email + contraseña) | Auth.js v5 con magic link + Google, sesión JWT que verifica la fila del usuario en cada request |
| Usuarios y roles (RF-31) | Área `/app` con `(app-admin)` y filas de usuario en D1 |
| Design system Quant24 (RF-30) | `[paquete interno]` (dark-only, `SourceBadge`, aviso CFTC como test) |
| Despliegue propio | Un solo Worker (`q24-web`), un solo dominio, CI con deploy a producción |

Construir el panel aparte duplicaría autenticación, gestión de usuarios y design system,
y produciría dos UX distintas bajo la misma marca. La integración elimina las dos copias.

## 3. Arquitectura técnica

```
Warren / agentes externos ── API key ──> https://sentinel.quant24.io   (Worker `sentinel-api`, público)
                                              ▲
quant24.io/app ───── service binding ─────────┘   misma cuenta Cloudflare, tráfico interno
   /app/riesgo            feed + filtros (RF-25) + búsqueda
   /app/riesgo/[id]       detalle (RF-26) + recalificación (RF-13)
   /app/admin/risco/*     fuentes (RF-27), umbrales (RF-28), usuarios/auditoría (RF-31/33)
```

1. **`sentinel-api` sigue siendo Worker y repo independientes.** Ingesta, motor de riesgo,
   RAG y agente on-demand viven en él. La plataforma **nunca** toca el D1 de Sentinel
   directo: solo por la API.
2. **Service binding** `SENTINEL` → `sentinel-api` declarado en `[ruta interna]`
   de la plataforma. Sin CORS, sin llaves en el tráfico interno, sin latencia de red pública.
3. **Contratos compartidos por la forja:** el paquete `[paquete interno]` (generado de
   `[ruta interna]`) se publica en el registry npm de `forja.quant24.io`, y
   `[ruta interna]` de la plataforma lo consume. Fuente única de verdad de tipos y
   esquemas Zod.
4. **RBAC en dos capas (RNF-09 intacto):**
   - La plataforma decide **quién ve qué pantalla** con su sesión y sus roles.
   - Sentinel aplica **scopes por API key y roles en la API y en la base**. La plataforma
     llama con una API key de integración server-side (escritura solo para recalificación y
     administración), nunca con la sesión del usuario como credencial.
5. **Identidad de auditoría:** Sentinel mantiene su tabla `User` (filas espejo de los
   usuarios de la plataforma) porque `AuditLog.actor_id` y `RiskScoreOverride.usuario_id`
   necesitan quién hizo cada acción. La plataforma envía el identificador del usuario en la
   llamada; Sentinel lo registra.

## 4. Qué cambia del entregable de septiembre

| Antes (02-sep) | Ahora (06-oct) |
|---|---|
| Panel React+Vite en Cloudflare Pages (`[ruta interna]`) | No existe. Secciones en `quant24-platform/[ruta interna]` |
| RF-32: email + contraseña propios | **Subsumida** por la sesión de la plataforma. Desvío declarado, no escondido |
| RF-25/26/27/28/29/33 dibujados por el Rol 4 en el repo Sentinel | Dibujados por Nicolás **en el repo de la plataforma**, como nueva fase con su plan y gate |
| Repositorio en GitHub (`Quant24inc/sentinel`) | Repositorio en **`forja.quant24.io/Quant24inc/quant24-sentinel`** |
| Tareas 4.3/4.4/5.4 del Gantt en el proyecto Sentinel | Migran al proyecto `quant24-platform` (post-F8) |

Lo que **no cambia**: todo el trabajo del Rol 1, Rol 2 y el del Rol 3 en el Worker; Warren y
agentes externos consumen igual por API key; el modelo de datos, los RF 01-24 y 34-51
quedan intactos.

## 5. Condiciones y riesgos declarados

- **`quant24-platform` está en F8 (auditoría integral).** Las secciones de riesgo entran
  como nueva fase con plan y gate propios cuando F8 cierre; Nicolás puede empezar el código
  en rama de la plataforma sin esperar.
- **Orden de despliegue:** `sentinel-api` debe existir antes de activar el binding en la
  plataforma (deploy del API primero, binding después).
- **Dominio:** `sentinel.quant24.io` es ruta custom del Worker (la zona de Cloudflare ya
  sirve `quant24.io`). Quien crea la ruta es Xavier.
- **Acoplamiento de contrato:** un campo nuevo en la respuesta de la API es un PR en
  `dominio`, versión nueva del paquete y actualización en la plataforma. Nada se cambia en silencio.
- **Si Sentinel cae, la sección de riesgo de la plataforma degrada** (mensaje de estado, no
  pantalla rota). El resto de la plataforma no depende de Sentinel.
