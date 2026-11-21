# Trabajo, commits y evidencia

## Repositorios de producto

- Sentinel: `Quant24inc/quant24-sentinel` en la forja; `develop` para integración/dev y `master` para promoción productiva.
- Platform: `Quant24inc/quant24-platform` en la forja; rama `main` consultada para el despliegue.
- Este repositorio GitHub: explicaciones y trazabilidad documental mediante registros de commits. Su historial publicado contiene únicamente documentación, sin los snapshots de código ni sus commits de importación. GitHub Actions está deshabilitado.

## Qué significan los históricos

Cada tabla de `historial/` proviene de `git log` de la rama remota indicada y enumera todos sus commits alcanzables, incluidos merges. La fecha es la fecha de autor original; no se confunde con hora de deploy ni fecha de un commit documental nuevo.

Los asuntos son metadatos históricos, no pruebas de aceptación. Un commit que diga “verificado” sigue requiriendo resultados de tests, CI o proveedor. Se omiten correos de autores, parches, secretos y contenido de archivos originales.

No se reescribe el historial original ni se importan sus objetos Git: eso podría arrastrar código y credenciales. Los enlaces a la forja requieren acceso autorizado.

## Reglas de verificación

1. Identificar commit exacto y entorno.
2. Separar tests, build, CI, upload, versión activa y comportamiento HTTP.
3. Distinguir un job verde de un deploy real: un filtro de paquete sin coincidencias puede no ejecutar nada.
4. Comprobar permisos, destino y recursos antes de escribir.
5. Registrar versión anterior para rollback; una reversión de Worker no revierte automáticamente cambios de DB.
6. No aprobar corpus ni alterar fixtures ajenas para producir un verde.
7. Guardar y transferir credenciales por canales privados, nunca Markdown o Git.

## Lecciones del trabajo documentado

- Un account ID heredado causó consultas al destino equivocado; la cuenta se confirmó desde el Worker real.
- Bindings creados en dashboard pueden perderse si un deploy no los declara.
- `$in` real en Vectorize exigió partición de máscaras, manteniendo los filtros.
- El panel productivo no es la vista del corpus de pruebas dev.
- El cron puede estar sano y materializar cero ítems; aprobación y corpus se comprueban aparte.
- La documentación histórica queda obsoleta: los estados operativos deben llevar fecha y readback.

## Mantenimiento

Actualizar los MD con fuentes y fecha; revisar que no haya datos privados antes de cambiar la visibilidad del repositorio. No hay automatización que sincronice futuros commits o estados.
