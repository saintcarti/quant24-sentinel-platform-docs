# Quant24 Platform + Sentinel

Documentación explicativa de dos proyectos independientes que se conectan: **Platform es la interfaz de usuario; Sentinel aporta noticias clasificadas y recuperación de contexto con citas**.

La versión actual de este repositorio público contiene únicamente Markdown: explicaciones y registros de commits originales, sin código de producto. Es una fotografía documental, no una sincronización automática.

**Sin CI/CD:** GitHub Actions está deshabilitado.

El historial publicado fue reemplazado por un único commit de documentación. No contiene carpetas de código ni commits de importación de los proyectos. Los registros originales se conservan como referencias documentales en `historial/` y los documentos de avance en `docs/originales/`.

## El equipo

Repositorio de evidencia del equipo del Capstone APT (Duoc UC, noviembre de 2026):

- **Xavier Liberona** — Rol 3: API, plataforma e infraestructura; administrador del repositorio.
- **Nicolás Vásquez** — Rol 4: panel y frontend de la plataforma.
- **Mauricio Gatica** — Rol 2: motor de riesgo y capa RAG.
- **Iván Rocco** — Rol 1: ingesta y datos.

El trabajo individual, con pull requests y referencias verificables por integrante, está en [Aporte por integrante](docs/06-aporte-por-integrante.md).

## Recorrido

1. [Qué es Sentinel](docs/01-sentinel.md).
2. [Qué es Quant24 Platform](docs/02-platform.md).
3. [Cómo se unen](docs/03-integracion.md).
4. [Estado verificado y límites](docs/04-estado-verificado.md).
5. [Cómo se trabaja y se verifica](docs/05-trabajo-y-evidencia.md).
6. [Aporte por integrante](docs/06-aporte-por-integrante.md).
7. [Historial original de Sentinel](historial/sentinel.md).
8. [Historial original de Platform](historial/platform.md).
9. [Pull requests de la forja](historial/pull-requests.md).
10. [Documentos originales del proyecto](docs/originales/).
11. [Evidencia de ensayos](docs/evidencia/).

## Distinción importante

Un sitio desplegado no equivale a operar inversiones con dinero real. La plataforma mantiene su alcance de desarrollo/sandbox. Sentinel puede contener noticias reales de fuentes externas; eso no convierte las demos de trading en operaciones reales.

## Sobre los commits

Los archivos de `historial/` registran hashes, fechas y asuntos reales de los commits alcanzables desde las ramas consultadas. Los commits de **este repositorio** son nuevas entregas documentales con su fecha real: la importación posterior del código es un snapshot nuevo, sin inventar una cronología de contribuciones.

Fuentes: repositorios privados de la forja Quant24, configuración de despliegue y lecturas de Cloudflare/D1. Los enlaces internos pueden requerir autorización. Corte: 20-nov-2026, alrededor de 03:15 UTC.

> *Quant24 no es un asesor de inversiones registrado. El producto está actualmente en desarrollo. Ningún trade real está siendo ejecutado. Todas las visualizaciones y demos son simuladas. El rendimiento pasado referenciado es ilustrativo y no garantiza resultados futuros. Nada en esta comunicación constituye asesoría de inversión o una solicitud de valores.*
