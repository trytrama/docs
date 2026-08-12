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

El tab de API NO se escribe a mano: sale de `openapi.json`, que es la spec que
`apps/api` genera con `@hono/zod-openapi` y publica en
<https://api.trama.so/v1/openapi.json>.

Ese archivo **está versionado acá a propósito**, no linkeado. Mintlify baja una
spec remota en tiempo de build y sólo buildea cuando hay un push a este repo:
apuntando a la URL, un cambio en la API no producía ningún push y la referencia
pública quedaba congelada sin que fallara nada.

Lo mantiene en hora `.github/workflows/sync-openapi.yml`: cada 6 horas trae la
spec de producción, la valida y la commitea **sólo si cambió**. Ese commit es lo
que dispara el rebuild.

> No edites `openapi.json` a mano: el cron te lo pisa. Si falta un endpoint, se
> arregla en `apps/api` del monorepo. Para no esperar el tick, corré el workflow
> con **Run workflow** (`workflow_dispatch`).

## Editar

El contenido son MDX planos. Para previsualizar:

```bash
bunx mint dev            # servidor local, con hot reload
bunx mint broken-links   # validar links antes de pushear
```

Un push a `main` deploya. **No hay entorno de staging y es a propósito**: un
proyecto de Mintlify es un repo + una sola branch, así que un segundo site
obligaría a una branch por entorno y a mantener dos `docs.json` que conflictúan
en cada merge.

Para documentar algo que todavía no salió, la página se escribe pero **no se
agrega a `navigation`** en `docs.json`: buildea y responde por su URL, fuera del
menú. Publicarla después es agregar la entrada. El porqué y el otro mecanismo
(`drafts/`, que ni siquiera se ve en local) están en
[`AGENTS.md`](./AGENTS.md).

## Ojo

Este repo es **público**. Nada interno acá — la documentación de arquitectura
vive en `docs/` del monorepo, que es privado.
