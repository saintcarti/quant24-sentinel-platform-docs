# El repo en la forja y OpenCode (06-oct-2026)

Cómo se entra al código, qué le carga cada programador a su agente y cómo se trabaja en el
día a día. Este documento es el que cada persona abre antes de encender OpenCode.

---

## 1. Dónde vive el código

| Qué | Dónde |
|---|---|
| Repositorio remoto | **`https://forja.quant24.io/Quant24inc/quant24-sentinel`** (Gitea, privado, en la organización) |
| Copia local en la máquina de Xavier | `C:\Users\Xavier\Desktop\Q24\sentinel\` |
| Org en la forja | `Quant24inc` (ya existe; allí vive `[repo interno]`) |

> El repo vivió brevemente en `qfd-admin/quant24-sentinel` y se movió a la organización el
> 06-oct. Si tu clon apunta a la ruta vieja: `git remote set-url origin https://forja.quant24.io/Quant24inc/quant24-sentinel.git`

**La forja está detrás de Cloudflare Access.** Para entrar con el navegador: abrir
`forja.quant24.io`, iniciar sesión con "Cloudflare" (cuenta activa de Cloudflare es
requisito), verificar el correo `@quant24.io` con el PIN. La sesión dura 24 horas.

****Para git (clone/push)**: autenticación por token propio.
Este repo **ya tiene su acceso git configurado** (rutas nueva y vieja). Nueva regla de la casa: **cada repo nuevo de la forja necesita su propio acceso git** — la crea Xavier; sin
ella, `git clone` da la página de Access y no los refs.

```bash
git clone https://forja.quant24.io/Quant24inc/quant24-sentinel.git
cd sentinel
git config user.name  "Quant24"
git config user.email "githubquant@quant24.io"   # sin esto el despliegue queda bloqueado
```

## 2. Qué le carga cada programador a OpenCode

OpenCode trabaja bien cuando le das el contexto mínimo correcto, no el repo entero. El
paquete de arranque de cada rol es **siempre** estos archivos más su documento de rol:

1. `docs/00-LEEME-PILOTO-2026-10-06.md` — el mapa completo
2. `docs/07-ESTADO-CODIGO-2026-10-06.md` — **qué ya existe y qué no** (obligatorio: evita que el agente reconstruya lo que ya está hecho)
3. `docs/01-INTEGRACION-QUANT24-PLATFORM-2026-10-06.md` — cómo encaja con la plataforma
4. Este archivo (`docs/02-REPO-FORJA-OPEN-CODE-2026-10-06.md`)
5. Tu documento de rol (ver tabla)

| Rol | Su documento | Doc base con el detalle completo (en `docs/base/`) |
|---|---|---|
| Iván — Ingesta | `docs/03-ROL-1-IVAN-INGESTA-2026-10-06.md` | `07-rol1-ingesta.md` + `05-arranque-tecnico.md` |
| Mauricio — Motor/RAG | `docs/04-ROL-2-MAURICIO-MOTOR-RAG-2026-10-06.md` | `08-rol2-motor-rag.md` |
| Xavier — API/Plataforma | `docs/05-ROL-3-XAVIER-API-PLATAFORMA-2026-10-06.md` | `09-rol3-api-plataforma.md` |
| Nicolás — Frontend | `docs/06-ROL-4-NICOLAS-FRONTEND-PLATFORM-2026-10-06.md` | `10-rol4-frontend.md` (parcial: el panel ya no está aquí) |

Frase modelo para arrancar la sesión con tu agente:

> "Lee `docs/00-LEEME-PILOTO-2026-10-06.md`, `docs/01-INTEGRACION-...`, `docs/02-...` y
> `docs/03-ROL-1-...`. Soy Iván, Rol 1. Empieza por el conector SEC EDGAR siguiendo
> `docs/base/05-arranque-tecnico.md`. No toques nada fuera de `[ruta interna]` y tu módulo."

## 3. Cómo se trabaja en el día a día

- **Ramas por tarea:** `rol1/conector-edgar`, `rol2/capa1-reglas`, `rol3/auth-middleware`...
  Nunca push directo a `master` — la forja tiene el cerrojo de merge (un push directo a
  master se ignora; solo un merge de PR despliega/dispara CI).
- **PR en la forja**, con descripción de qué toca y qué prueba. Revisión por otro rol cuando
  cruzan contratos (ver sección 5 de `01-INTEGRACION`).
- **Mensajes de commit en español.** Código y sitio en inglés (convención de la casa).
- **Comandos base:**
  ```bash
  pnpm install                    # nunca npm install (hay hook que lo bloquea)
  pnpm test                       # vitest en todo el monorepo
  pnpm typecheck                  # tsc estricto en todo el monorepo
  pnpm dev                        # wrangler dev del API en :8850 (puerto alto a propósito)
  ```
- **`.dev.vars`** (ignorado por git) guarda credenciales locales de desarrollo. Nunca un
  secreto en el código, en un PR ni en una pantalla compartida (`wrangler tail` enseña
  headers de Authorization).
- **D1 local:** `pnpm wrangler d1 migrations apply [recurso interno] --local` después de cada pull
  que traiga migraciones nuevas. En Windows, migraciones con `--file`, nunca `--command`
  multilínea.

## 4. Estructura del monorepo

```
sentinel/
├── packages/
│   ├── api/          Worker con Hono: API, cron de ingesta, motor de riesgo, agente
│   └── dominio/      Tipos TS + esquemas Zod + migraciones D1 (contrato de los cuatro)
├── docs/             Documentación viva (este conjunto)
├── docs/base/        Entregable 02-sep-2026 (referencia, con enmiendas declaradas)
├── pnpm-workspace.yaml
└── README.md
```

**No hay `[ruta interna]`**: el frontend vive en `quant24-platform` (ver `01-INTEGRACION`).
Si tu agente propone crearlo, es una señal de que no leyó los documentos.

## 5. Quién despliega y dónde

- **API:** Worker `sentinel-api`, ruta custom `sentinel.quant24.io`. Despliega Xavier
  (o CI al mergear a `master`, cuando el pipeline esté armado).
- **Panel:** se despliega con `quant24-platform` (su propio pipeline, OpenNext).
- **Recursos Cloudflare:** los crea Xavier en Fase 0 con los nombres `sentinel-*`; nadie más
  crea recursos (los que usan, usan).
