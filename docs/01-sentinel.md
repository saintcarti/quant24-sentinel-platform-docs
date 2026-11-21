# Qué es Sentinel

Sentinel es un servicio de información de riesgo: adquiere noticias, las normaliza, identifica señales de riesgo y ofrece una API estructurada y búsqueda semántica con citas.

## Flujo de una noticia

1. Un cron programa una fuente y una cola distribuye el trabajo.
2. Un conector adquiere el contenido y el pipeline normaliza y deduplica.
3. La capa de reglas asigna nivel y categorías con justificación. Un refuerzo LLM opcional puede agregar otra evaluación; su activación depende del entorno y presupuesto.
4. La noticia queda pendiente de aprobación humana. Puntuar no es aprobar.
5. Solo los aprobados pueden indexarse y recuperarse en el RAG.
6. La indexación trocea, genera embeddings y guarda vectores. La búsqueda hidrata resultados desde D1 para volver a comprobar aprobación y estado vigente.
7. Descartar exige exclusión de recuperación y limpieza reconciliable del índice.

## RAG y working set

El RAG devuelve fragmentos con título, fuente, fecha y URL. Usa bge-m3 de 1024 dimensiones y Vectorize con similitud cosine. Los filtros incluyen símbolo, nivel, fecha, fuente, origen y categorías. La implementación particiona máscaras de `$in` a un máximo de 100 valores por consulta y fusiona resultados; no elimina filtros para sortear límites.

El working set es una selección horaria acotada de noticias aprobadas, ordenadas por riesgo y recencia. Guarda criterios, entradas y salidas en D1. Un cron ejecutado puede producir un set vacío si no hay aprobados.

## Capacidades y límites

- API autenticada con claves, roles, scopes, límites y auditoría.
- Fuentes con circuit breaker y observabilidad de corridas.
- Aprobaciones, recalificación e indexación con reconciliación.
- Investigación on-demand con núcleo persistente y cierre seguro cuando faltan proveedores. No se afirma investigación externa completa.
- Feedback y MCP están integrados en el árbol consultado; su promoción y pruebas remotas deben verificarse por entorno.

Sentinel no recomienda inversiones ni ejecuta trades. Warren AI fue retirado como producto consumidor: los nombres históricos de tablas no significan una integración activa.

Fuente: docs 18–22, docs RAG 14–17, servicios y contratos del repo Sentinel. [Repositorio original](https://forja.quant24.io/Quant24inc/quant24-sentinel).
