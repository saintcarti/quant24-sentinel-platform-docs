# Evidencia de ensayos

Registros documentales de las corridas reales contra el entorno de desarrollo de Sentinel, ejecutadas por HTTP. Las credenciales nunca se versionan: se usan claves dedicadas por entorno y todo queda trazado en la auditoría (`GET /v1/audit`).

- `evidencia-e2e-f5.md` — ensayo real E2E de la Fase 5 (noche 19→20-nov-2026, UTC), ejecutado por Rol 2: curación RF-51 (30 aprobados + 6 descartados, 36 tareas de aprobación), indexación con embeddings reales bge-m3, recuperación semántica con citas (similitud 0.630), working set RF-47 (30 ítems) y primera corrida dorada RNF-28 (PR #30).
- `evaluacion-rag/consultas.json` — definición versionada del set de evaluación (6 consultas).
- `evaluacion-rag/dorada.json` — corrida dorada generada contra el dev real el 20-nov-2026 01:04 UTC; versionada junto al índice (PR #30, rama `rol2/dorada-rag`).

Los sistemas desplegados están detrás de Cloudflare Access; esta evidencia es el registro verificable de lo ejecutado.
