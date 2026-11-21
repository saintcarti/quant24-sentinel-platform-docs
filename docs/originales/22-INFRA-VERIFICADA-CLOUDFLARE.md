# Infraestructura verificada en Quant24

Cuenta confirmada por whoami: x.liberona@..., 6fd14e....

- Creados [recurso interno] y [recurso interno]: 1024 dimensiones, cosine; ambos leídos de vuelta.
- Los seis metadata indexes fueron creados antes de vectores y luego confirmados por API en cada índice: [slot]:string, [slot]:number, [slot]:number, [slot]:string, [slot]:string, [slot]:number.
- Los índices ajenos de otros productos no se modificaron. Que Warren producto esté retirado no autoriza borrar un recurso ajeno.
- Prueba real en [recurso interno], con tres vectores sintéticos y visibilidad confirmada: $in100 devuelve solo el vector incluido; $in128/200/255 son rechazados con40029: longitudmáxima100. El índice de ensayo fue eliminado y ausencia confirmada por listado.
- Solución de código: particionar máscaras en consultas filtradas <=100, fusionar el [función interna] por similitud. Conservar seis filtros; no ocho booleanas ni post-filtro de muestra limitada.
- Workers AI bge-m3 ejecutado contra APIreal: vector1024 confirmado.
- Migraciones0003–0006 aplicadas solo [recurso interno]; readback “No migrations to apply”. No [archivo interno] remoto; datos previos dev eran seis pending.
- BindingsprodAI/[binding] [función interna]/[binding] declarados después del readback. REFUERZO_ACTIVO1 solo dev; prodapagado.
- Namespace[tipo interno] configurado solo dev para prueba failclosed; providersreal siguenpendientes.

## Rollback previo

Dev versión f5fff06f-3a2c-4561-8a45-7043ad77667a. Producción versión469aa8ce-e56b-4bb1-81af-91dd7ac3abe5. Lecturas de deployment antes de cambiar Worker. No afirmar deploynuevo hasta versionreadback+health+pruebas funcionales.

## Gates abiertos

CI del commit nuevo, despliegue y E2Erealdev pendientes. Producción Worker/base sin modificar. Token delegadoMauricio90días requiere creación issuer; no compartir OAuth local.
