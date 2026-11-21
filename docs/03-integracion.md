# Cómo se unen

## Mapa de responsabilidades

```text
Fuentes externas
       |
       v
Sentinel: ingesta -> reglas/refuerzo -> aprobación humana
       |                                  |
       |                                  v
       |                            RAG + working set
       v                                  |
API autenticada de Sentinel <--------------+
       ^
       | Service binding SENTINEL + credencial del servidor
       |
Platform: rutas protegidas /app/api/riesgo/*
       ^
       | sesión del usuario
       |
Navegador: /app/riesgo
```

Platform no copia el motor RAG. Su servidor consulta a Sentinel y presenta el resultado. Los repositorios, bases y recursos siguen separados.

## Dos autenticaciones distintas

1. El usuario inicia sesión en Platform.
2. El servidor de Platform se identifica ante Sentinel con una API key y usa el Service binding `SENTINEL` para seleccionar el Worker destino.

La clave upstream no se entrega al navegador ni aparece en este repositorio. Un binding no concede autorización por sí solo. La allowlist administrativa de Platform tampoco concede automáticamente permisos de aprobación en Sentinel.

## Separación por entorno

El binding verificado en el Worker productivo de Platform apunta a **`sentinel-api`**, producción. El corpus de aceptación de Sentinel está en **dev**, en `sentinel-api-dev`. Por eso el panel productivo no constituye una vista del corpus dev ni evidencia del E2E de dev.

Un panel preview/dev solo debe afirmarse conectado tras leer su configuración real. No se presupone ese enlace aquí.

## Fallos y diagnóstico

- 401/403 JSON: la ruta existe, pero falta autorización o permiso.
- 404 HTML: puede indicar código anterior o ruta ausente; no se arregla cambiando una key a ciegas.
- 503 upstream: puede indicar bindings, contratos o proveedor no disponibles.
- Índice vacío y working set vacío pueden ser estados válidos, no éxito de recuperación.

Al desplegar Platform hay que conservar los bindings autorizados creados en el dashboard. En el despliegue documentado se preservó `SENTINEL` mediante una modificación de configuración local, sin alterar el código de `main` ni reemplazar secretos.
