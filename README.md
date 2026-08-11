# Trama — Documentación

La documentación pública de [Trama](https://trama.so), servida en
**[docs.trama.so](https://docs.trama.so)** con [Mintlify](https://mintlify.com).

## Estructura

Tres idiomas, mismo árbol de archivos en los tres. Inglés es el default y vive
en la raíz (es el `DEFAULT_LOCALE` y el `x-default` del sitio de marketing, así
que las docs no lo contradicen):

```
introduction.mdx  quickstart.mdx  agent/  channels/  operations/ ...   → en
es/                                                                    → español
pt-BR/                                                                 → portugués (Brasil)
```

La navegación, los colores y los idiomas salen de `docs.json`. Cada idioma tiene
dos tabs: **Documentación** y **API**.

## La API Reference se genera sola

El tab de API NO se escribe a mano: sale del OpenAPI que publica `apps/api`
(`@hono/zod-openapi`) en <https://api.trama.so/v1/openapi.json>.

> 🔴 Mintlify baja esa spec **en tiempo de build**. Un cambio en la API no
> redeploya estas docs solo: hay que llamar al endpoint *Trigger deployment* de
> Mintlify desde el CI que la publica (`deploy-api.yml` en el monorepo).

## Editar

El contenido son MDX planos. Para previsualizar:

```bash
bunx mint dev            # servidor local
bunx mint broken-links   # validar links antes de pushear
```

Un push a `main` deploya. No hay staging: lo que se mergea, sale.

## Ojo

Este repo es **público**. Nada interno acá — la documentación de arquitectura
vive en `docs/` del monorepo, que es privado.
