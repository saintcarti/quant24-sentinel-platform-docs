# Rol 1 — Iván: Ingesta y datos (06-oct-2026)

Lee antes: `00-LEEME-PILOTO`, `01-INTEGRACION`, `02-REPO-FORJA-OPEN-CODE`. El detalle
completo de tus RF/RNF está en `docs/base/07-rol1-ingesta.md` y las trampas técnicas en
`docs/base/05-arranque-tecnico.md`. Este documento manda sobre lo que diga el base.

---

## Tu misión en una frase

Traer el mundo exterior a Sentinel: conectores de fuente, normalización a `NewsItem`,
deduplicación, entidades, filtro de relevancia — y la política que gobierna cualquier
descarga, programada u on-demand.

## Dueño de

- Interfaz `[adaptador]` (RF-05) y sus conectores: **SEC EDGAR 8-K, RSS oficiales (bancos
  centrales/reguladores), GDELT, Google News**.
- Normalización a `NewsItem` canónico + extracción de entidades (RF-07, RF-09).
- Deduplicación por hash (RF-04) y filtro de relevancia financiera (RF-06) — la palanca
  principal de costo del sistema.
- Esquema de D1 (con Xavier, que lo integra al paquete `dominio`).
- **Para la fase 5:** `[adaptador]` y `[adaptador]`... no: `[adaptador]`
  (RF-37), política de scraping y `DomainPolicy.check(url)` (RF-38). El `[adaptador]`
  lo implementa Xavier sobre tu misma interfaz.
- Licencias por clase de fuente y atribución (RNF-15, RF-16), cortesía de red (RNF-25),
  techo de D1 (RNF-27), salud de conectores — tú produces los datos (RNF-14).

## Qué construye AHORA (semana 9 → MVP 18-oct)

1. **Interfaz `[adaptador]`** en `[ruta interna]` — es un contrato compartido; proponerla
   es tuyo, aprobarla es de los cuatro.
2. **Conector SEC EDGAR 8-K primero** (sin API key, formato estable, volumen bajo): cabecera
   User-Agent identificable o responde 403. Luego RSS oficiales, luego GDELT (el que aprieta
   en volumen y obliga a resolver filtro y lotes de D1), Google News al final.
3. **Normalización + dedup + entidades** sobre lo que entra.
4. **Banco de ensayo por fuente**: `[archivo interno]`, `[archivo interno]`, `[archivo interno]`,
   `[archivo interno]` — golpean la fuente real, guardan fixtures con `--grabar`. Son
   tu entrega más valiosa.
5. **Filtro de relevancia (RF-06)** tan pronto haya volumen real: es lo que evita pagar
   inferencia por basura.

## Dependencias

| Con quién | Qué |
|---|---|
| Xavier | Él orquesta el cron (RF-03) y llama tus conectores. **Acordar la firma de `[adaptador]` y `DomainPolicy.check(url)` esta semana**, aunque se usen en fase 5 |
| Mauricio | Tu `NewsItem` es su materia prima. No cambias el esquema sin PR en `dominio` |
| Nicolás | Consumirá tus fuentes por la API; para él, atribución visible (GDELT) |

## Enmiendas al documento base

- El repo es la forja, no GitHub (`02-REPO-FORJA`).
- No hay panel propio que consuma tus tablas: la plataforma las lee por la API de Xavier.
- Tu cron NO lo programas tú: Xavier expone el disparo (Cron Triggers) y tu código provee el
  trabajo de cada fuente.

## Frase para tu agente OpenCode

> "Lee docs/00-LEEME-PILOTO-2026-10-06.md, docs/01-INTEGRACION..., docs/02-... y
> docs/03-ROL-1-IVAN-INGESTA-2026-10-06.md. Soy Iván, Rol 1 — Ingesta y datos. Trabaja solo
> en [ruta interna] y en el módulo de ingesta de [ruta interna] Empieza por la interfaz
> [adaptador] y el conector SEC EDGAR con User-Agent configurable y tests contra fixture.
> No toques el motor de riesgo ni la API."
