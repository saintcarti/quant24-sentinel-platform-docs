# Documentos originales del proyecto

Copias de los documentos de avance del Capstone (`00` a `23`) de la carpeta `docs/` del repositorio privado `Quant24inc/quant24-sentinel` (rama `develop`, head `31e045d1f87ef7032f200c8f916a8cf40979245c`), exportados con `git archive`. Cada nombre conserva la fecha y el rol del integrante: son la evidencia de avance con fecha que cita el informe final del Capstone (anexo 8.3).

Se excluyen los documentos de especificación del producto (`docs/base/`): son documentación interna de la compañía, no evidencia del Capstone.

## Documentos no publicados

De los 24 documentos, dos se mantienen solo en la forja privada porque su nivel de detalle interno (volcado de esquema de la base de datos y auditoría archivo por archivo) no admite redacción sin destruir su valor:

- `07-ESTADO-CODIGO-2026-10-06.md` — auditoría del estado del código.
- `09-F1-BUILD-STATUS-IVAN-2026-10-09.md` — estado de build de la Fase 1.

## Redacciones aplicadas

Para publicar los 22 documentos restantes se aplicaron redacciones uniformes, sin borrar archivos ni alterar la estructura ni la cronología:

- Correos personales de los integrantes: truncados a `nombre@...`.
- Identificadores hexadecimales de infraestructura: truncados a sus primeros dígitos.
- Rutas de archivos y carpetas del repositorio de producto: `[ruta interna]`.
- Nombres de archivos de código, migraciones y scripts: `[archivo interno]`, `[migración interna]`, `[script de evaluación]`.
- Valores de claves y tokens (todos ejemplos de entornos locales): `sk_[redactada]`.
- URLs de workers y entornos desplegados: `[worker redactado]`.
- Recursos internos de Cloudflare (bases, índices, buckets, colas): `[recurso interno]`, `[binding]`.
- Nombres de tablas, columnas y esquema de la base de datos: `[identificador interno]`, `[campo interno]`.
- Nombres de funciones, clases, interfaces, constantes y códigos internos del código: `[función interna]`, `[interfaz interna]`, `[constante interna]`, `[código interno]`.
- Detalles del mecanismo de acceso a la forja y a la infraestructura: sustituidos por «acceso git» o «acceso interno».

Ningún valor de credencial aparece en estos documentos: solo nombres de secretos y ejemplos redactados de entornos locales, nunca valores de producción.

## Índice rápido

| Documentos | Autor | Tema |
|---|---|---|
| 00, 01, 02 | Equipo | Piloto: arranque, integración con Platform, forja (el 07 queda en la forja privada) |
| 03–06 | Iván / Mauricio / Xavier / Nicolás | Asignación de roles (06-oct-2026) |
| 08–11 | Iván (Rol 1) | Portada de la Fase 1, prueba de 72 horas, propuesta de DLQ (el 09 queda en la forja privada) |
| 12–17 | Mauricio (Rol 2) | Plan RAG P0–P5, capa 2 LLM, indexación, API de recuperación, working set, evaluación |
| 18–22 | Xavier (Rol 3) | Alcance vigente, investigación on-demand, desbloqueo de infra F5, handoff F5, infra verificada |
| 23 | Mauricio (Rol 2) | Feedback RAG + herramienta MCP |
