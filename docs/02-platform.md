# Qué es Quant24 Platform

Quant24 Platform reúne el sitio público de Quant24 y su aplicación web autenticada. El sitio vive en `quant24.io`; la aplicación, bajo `/app` en el mismo dominio.

## Responsabilidades

- Presentar contenido, formularios y páginas públicas.
- Autenticar al usuario y controlar acceso a secciones protegidas.
- Presentar sesiones, estrategias, validaciones, paquetes y estados del producto dentro del alcance de desarrollo/sandbox.
- Mostrar datos sintéticos etiquetados y distinguirlos de noticias externas reales.
- Ofrecer el panel de riesgo conectado a Sentinel.

## Arquitectura

Next.js App Router y React sobre Cloudflare Workers mediante OpenNext. El monorepo separa la web, el sistema de diseño, contratos, contenido, base de datos, correo y motor cuantitativo. Usa D1 para datos, R2 para archivos/caché, KV para banderas/límites, y Queues, Workflows y Durable Objects para trabajos con estado.

Autenticación con Auth.js; integraciones de correo y pagos tienen configuración y límites propios. El despliegue de infraestructura no demuestra que todas las integraciones comerciales estén habilitadas.

## Qué no implica que el sitio esté disponible

No implica conexión a brokers con capital real, asesoría personalizada, rentabilidad garantizada ni aprobación de todos los gates de calidad y legales. La comprobación de salud tampoco prueba cada flujo autenticado.

Fuente: manifiestos, configuración y documentación de operación de Platform; el prompt de construcción expresa objetivos, no evidencia de que todos se hayan cumplido. [Repositorio original](https://forja.quant24.io/Quant24inc/quant24-platform).

> *Quant24 no es un asesor de inversiones registrado. El producto está actualmente en desarrollo. Ningún trade real está siendo ejecutado. Todas las visualizaciones y demos son simuladas. El rendimiento pasado referenciado es ilustrativo y no garantiza resultados futuros. Nada en esta comunicación constituye asesoría de inversión o una solicitud de valores.*
