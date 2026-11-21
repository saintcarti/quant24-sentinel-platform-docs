# Alcance vigente de Sentinel

Decisión de Xavier, 18-nov-2026. Esta nota prevalece sobre referencias a Warren AI en los documentos base y planes anteriores.

## Producto y consumidores

Warren AI ya no existe como producto y no se conectará a Sentinel. Se cancela la tarea de crear una clave con `integration: "warren"`; no se crea ni se configura esa integración.

Sentinel conserva su API estructurada, motor de riesgo, investigación on-demand y recuperación semántica con citas para el panel y los consumidores autorizados que se definan. No depende de un consumidor concreto. Las integraciones nuevas usarán claves de alcance mínimo, sin privilegios especiales.

El working set es contexto curado de Sentinel, no una función exclusiva de Warren. La ruta `/v1/rag/working-set` se conserva. Los nombres históricos `[identificador interno]` y `WarrenWorkingSet` se mantienen por compatibilidad con datos y contratos existentes; no implican una integración activa. No se renombra una tabla remota solo para corregir narrativa.

## Aprobación

La aprobación humana de RF-51 sigue vigente. La llegada de una fuente programada no sustituye una decisión humana. Los nuevos ítems quedan pendientes hasta aprobar o descartar; el motor puede puntuarlos antes de la aprobación, pero el RAG solo indexa y sirve aprobados.

Los aprobados históricos no se reclasifican automáticamente en una migración: antes de producción hay que identificar cuáles fueron aprobados por política automática y revisar su procedencia. Una suite verde no prueba esa revisión ni habilita la promoción.

## Identidad de auditoría

`X-On-Behalf-Of` conserva el contrato de delegación existente: representa una identidad declarada por el cliente autenticado, no una identidad humana verificada por Sentinel. No se usa para cuotas ni autorización; estas usan la clave autenticada. Antes de conectar un panel con múltiples usuarios, validar la delegación y conservar también la procedencia de la clave. El permiso de aprobar exige `write` y rol analista/admin, aunque el cliente declare otro actor.

## Despliegue y verificación

- `develop` despliega dev mediante CI; `master` activa el workflow productivo.
- Recursos de dev y prod separados. Metadata indexes antes de insertar vectores.
- Cron horario del working set en ambos entornos; backup solo producción.
- Refuerzo apagado en producción. Dev se habilita después de verificar AI y presupuesto.
- Pruebas con dobles, bundle, CI y comprobaciones contra proveedores reales se reportan por separado.
- Sin acceso Cloudflare no se declara infraestructura creada ni funcionamiento punta a punta.
