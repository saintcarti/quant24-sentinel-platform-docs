# Rol 2 — Mauricio: Motor de riesgo y RAG (06-oct-2026)

Lee antes: `00-LEEME-PILOTO`, `01-INTEGRACION`, `02-REPO-FORJA-OPEN-CODE`. El detalle
completo de tus RF/RNF está en `docs/base/08-rol2-motor-rag.md`. Este documento manda.

---

## Tu misión en una frase

Convertir cada `NewsItem` en una calificación de riesgo con justificación auditable, y
después (fase 5) volver todo el corpus recuperable en lenguaje natural para Warren.

## Dueño de

- Capa 1 del motor: reglas por palabras clave + tono nativo de GDELT (RF-11) — costo cero
  de inferencia.
- Capa 2 del motor: LLM de refuerzo solo para lo que la capa 1 marca Medio+ (RF-11).
- Taxonomía de niveles/categorías aplicada, explicabilidad obligatoria y versionado de
  reglas/modelo (RF-10, RF-12, RF-17).
- Suite de regresión por categoría con casos fijos (RNF-10) — se escribe con datos reales de
  la primera semana de flujo.
- Recalificación manual como dato, no como sobrescritura: `RiskScoreOverride` (RF-13) — la
  pantalla es de Nicolás, el motor de datos es tuyo.
- **Fase 5:** troceado (~800 tokens con solape), embeddings e índice Vectorize (RF-44/45),
  API de recuperación semántica con citas obligatorias y herramienta MCP para Warren
  (RF-46), Working Set top-100 (RF-47), convergencia del pipeline canónico (RF-40).

## Qué construye AHORA (semana 9 → MVP 18-oct)

1. **Taxonomía y esquemas** de `RiskScore` en `[ruta interna]` (con todos).
2. **Capa 1 de reglas** sobre fixtures mientras Iván no traiga flujo real; luego sobre los
   primeros datos reales.
3. **Explicabilidad y versionado desde el primer score** — un score sin justificación y
   versión es un bug, no una mejora pendiente.
4. **Primeros casos fijos de regresión** con lo que entre (RNF-10 exige cerrarlos en fase 2;
   aquí van atrasados y se recuperan).
5. **Capa 2 (LLM)** se activa cuando haya volumen que la justifique — y es el paso más caro:
   presupuéstalo (RNF-21 es tuyo).

## Dependencias

| Con quién | Qué |
|---|---|
| Iván | Su `NewsItem` es tu entrada. Tú lo enriqueces con `RiskScore` **sin modificarlo** |
| Xavier | Reutilizas su middleware de auth (RF-22) para tu API de recuperación (RF-46) — no escribes el tuyo. Convergencia del pipeline (RF-40) con él |
| Nicolás | La compuerta de aprobación (RF-51): nadie se trocea ni indexa sin su cambio de estado — ese candado lo implementas tú al leer el estado |

## Enmiendas al documento base

- **El modelo de refuerzo NO pasa por AI Gateway.** El entregable decía AI Gateway como
  proxy; está excluido del crédito de Cloudflare (se paga en efectivo). Preferencia de la
  casa: modelos del catálogo de **Workers AI** (dentro del crédito). Solo si ningún modelo
  del catálogo sirve se discute un externo — decisión con Xavier, no tuya sola.
- Repo en la forja; sin panel propio; la recalificación llega por la API con el middleware
  de Xavier.

## Frase para tu agente OpenCode

> "Lee docs/00-LEEME-PILOTO-2026-10-06.md, docs/01-INTEGRACION..., docs/02-... y
> docs/04-ROL-2-MAURICIO-MOTOR-RAG-2026-10-06.md. Soy Mauricio, Rol 2 — Motor de riesgo y
> RAG. Trabaja solo en [ruta interna] y en el módulo del motor dentro de [ruta interna]
> Empieza por la capa 1 de reglas sobre fixtures, con justificación y versión en cada score
> y su suite de regresión. No toques conectores, API pública ni auth."
