# 13 · Capa 2 del motor — refuerzo con LLM (RF-11)

Autor: Mauricio Gatica (motor de riesgo + RAG) · Fecha: 2026-11-17
Estado al enviar este PR: **implementada con dobles, apagada por defecto** (espera el
binding `AI` de Rol 3 y la var `REFUERZO_ACTIVO="1"` para operar).

## 1. Qué resuelve

RF-11 (motor de 2 capas): la capa 1 (reglas + tono GDELT) puntúa el 100% de los ítems a
costo cero; esta capa **refuerza con un LLM solo lo que quedó Medio+**, acotando el gasto
(RNF-21). El resultado entra como `[identificador interno]` con `layer='llm'` y queda como score
visible por ser el más reciente, con justificación textual y versión (RF-12, RF-17). El
score de la capa 1 nunca se borra (auditoría). Se engancha al pipeline en `[archivo interno]`
después de `[función interna]()` (extensión de la convergencia RF-40).

## 2. Decisiones

| # | Tema | Decisión |
|---|---|---|
| 1 | Modelo | `@cf/meta/llama-3.3-70b-instruct-fp8-fast` (catálogo de Workers AI; el crédito NO cubre AI Gateway, que no se usa). `REFUERZO_MODELO` en `motor/[archivo interno]` |
| 2 | Versión del prompt | `refuerzo-v1`; la versión completa persiste en `[identificador interno].version` como `modelo@prompt` (`VERSION_REFUERZO`) |
| 3 | Rango de salida (RNF-26) | ACOTADO a `medium..critical` (`NIVELES_REFUERZO` en los contratos): el refuerzo no puede silenciar una noticia (bajarla a informative/low) ni superar critical. Un intento de inyección que pida silenciar termina rechazado y logueado |
| 4 | Interruptor (RNF-21) | Var `REFUERZO_ACTIVO` ("1" enciende; vacío = solo capa 1) → `config.[función interna]`. Sin binding `AI`, no-op aunque esté encendido |
| 5 | Límite por ronda | 25 por defecto, 100 máximo (parámetro); cuerpo recortado a 4.000 chars por prompt; salida validada con esquema estricto (`[función interna]` en los contratos) |
| 6 | Fallback | Salida inválida (nivel fuera de rango, categorías desconocidas, texto sin JSON) o fallo del modelo ⇒ NO inserta: queda el score de capa 1 y el motivo en el log; la ronda continúa |
| 7 | Recalificación manual | Un ítem con score `manual` nunca se re-refuerza (la decisión del analista se respeta) |
| 8 | Alertas | Si el refuerzo cambia el nivel, se re-evalúan las alertas por el mismo camino RF-21 |

## 3. Archivos

- `[ruta interna]` (nuevo): contrato de salida del refuerzo
  (`[función interna]`, `RefuerzoSalida`, `NIVELES_REFUERZO`,
  `LIMITE_JUSTIFICACION_REFUERZO`), re-exportado desde el índice del paquete.
- `[ruta interna]` (nuevo): `EntradaRefuerzo`, interfaz inyectable
  `Refuerzo`, `[función interna]()` (defensa anti-inyección en el prompt),
  `[función interna]()` (validación + acotado + normalización de categorías al orden
  canónico), adapter `[función interna]()` para el binding real, versiones.
- `[ruta interna]`: `[función interna]()` — selección idempotente
  (último score `rules` medio+, sin `llm` ni `manual`, oldest-first), inserción
  `layer='llm'`, re-evaluación de alertas, logs por ítem y resumen por ronda.
- `[ruta interna]`: enganche tras `[función interna]()` con try/catch.
- `[ruta interna]`: `config.[función interna]` (false por defecto) y
  `Entorno.ia: Refuerzo | null` (binding inyectable).
- `[ruta interna]`: binding `AI` estructural + var `REFUERZO_ACTIVO` →
  adapter real; `null` cuando no hay binding.
- `[ruta interna]`: var `REFUERZO_ACTIVO` (vacía) en prod y dev — Rol 3
  la enciende cuando exista el binding `AI`.
- `[ruta interna]`: 16 tests (12 del servicio + 4 de defensas).

## 4. Defensa anti-inyección (RNF-26), en dos planos

1. **Prompt** (`[función interna]`): el texto del ítem se declara CONTENIDO analizado,
   no instrucciones; se exige JSON único y el rango permitido.
2. **Validación** (`[función interna]` + ronda): esquema estricto de los contratos,
   nivel acotado a medium..critical, categorías válidas normalizadas al orden canónico,
   verborrea del modelo tolerada (extracción del bloque `{...}`), y **rechazo sin
   inserción** con motivo en el log. La inyección puede, a lo sumo, pedir un nivel
   válido — el error ruidoso queda del lado conservador (fail-loud, no fail-silent).

## 5. Cómo se enciende (cuando Rol 3 tenga el binding)

1. Rol 3 agrega el binding `AI` a `[archivo interno]` (prod y dev).
2. Se pone `REFUERZO_ACTIVO: "1"` en el entorno.
3. Verificación de costo en la primera corrida (usage del binding en el panel) contra
   el presupuesto RNF-21; el límite por ronda y el recorte de cuerpo ya acotan el gasto.

## 6. Verificación

- Typecheck 2/2 (contracts + api).
- Suite completa: **219 tests en verde** (203 base + 16 nuevos). Los dobles de
  `Refuerzo` son deterministas; nada llama a la red.
- CI: typecheck + suite por PR; deploy a dev en cada push a develop.
