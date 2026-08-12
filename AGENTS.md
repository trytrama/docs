# Instrucciones para agentes

Documentación pública de Trama, en Mintlify. Repo **público**: nada interno acá.

## Reglas

- **Tres idiomas en paridad.** `en` (raíz), `es/`, `pt-BR/`. Toda página existe
  en los tres con el MISMO path. Si agregás una, agregá las tres y sumá las tres
  entradas a `docs.json`.
- **`docs.json` es la única fuente de la navegación.** Un `.mdx` que no está
  referenciado ahí no aparece en el menú — pero **sí se buildea y responde por su
  URL**. No es un bug: es el mecanismo para documentar lo que todavía no salió
  (ver más abajo).
- **No escribas páginas de API a mano, ni edites `openapi.json`.** El tab de API
  se genera de ese archivo, que lo sincroniza solo el cron
  `.github/workflows/sync-openapi.yml` desde `api.trama.so/v1/openapi.json`. Un
  cambio a mano te lo pisa el próximo tick. Si falta un endpoint, se arregla en
  `apps/api` del monorepo.
- **Colores: no toques `colors` sin mirar el contraste.** El amarillo de marca
  (`#FDC700`) da 1.57:1 sobre blanco — es un color de RELLENO, no de texto. Por
  eso `dark` (el color sobre fondo claro) usa `#733E0A`, que es el token
  `--primary-foreground` de `@trama/ui`.
- **Validá antes de pushear:** `bunx mint broken-links`. Un push a `main` deploya
  directo a producción.

## Un solo entorno, a propósito

Hay **un branch (`main`) y un site de Mintlify**. No existe proyecto de staging y
no conviene crearlo.

Un proyecto de Mintlify es un repo + UNA branch de deploy. Dos proyectos sobre el
mismo branch renderizan exactamente lo mismo (mismo `docs.json`, misma
navegación), así que separarlos obliga a una branch por entorno — y ahí aparece
el costo real: Mintlify no tiene variables de entorno, con lo cual cualquier
diferencia de `docs.json` entre entornos es un conflicto de merge en cada
`develop → main`, y el cron del OpenAPI hay que duplicarlo porque hace checkout
de la default branch y pushea sólo ahí.

El único caso que lo justificaría es documentar la API de **staging** con su
propio `openapi.json`. Si algún día pasa, hay que parametrizar
`sync-openapi.yml` por branch: hoy la validación exige a propósito que
`servers[0].url` sea `https://api.trama.so` y falla con cualquier otra, para que
el playground nunca mande requests al lugar equivocado.

Para revisar cambios antes de que salgan están el preview local (`bunx mint dev`)
y el preview automático que Mintlify genera en cada PR contra `main`.

## Documentar algo que todavía no salió

Dos mecanismos, y **no son intercambiables**. Los dos están verificados contra
`mint dev`:

**1. Fuera de `navigation` — el default.** Escribís el `.mdx` y no lo agregás a
`docs.json`. La página buildea, responde 200 por su URL y no aparece en el
sidebar. La ves en local y, una vez mergeada, le podés pasar el link al equipo.
Publicarla después es agregar la entrada a `docs.json` (las tres, una por
idioma). `bunx mint broken-links` no se queja de páginas huérfanas.

> Ojo: sigue siendo **pública** para quien tenga la URL, y entra en el índice de
> search y del assistant. No la uses para algo que no puede filtrarse.

**2. `drafts/` o `*.draft.mdx` — para lo que no puede existir en el server.**
Están en `.mintignore`, y eso aplica **también al preview local**: un archivo ahí
da 404 en `mint dev`, no sólo en producción. Para verlo renderizado hay que
sacarlo de la carpeta. Es el mecanismo caro; usalo sólo cuando el contenido no
puede estar publicado ni siquiera por URL.

Mientras una página está sin publicar la paridad de idiomas sigue valiendo: creá
las tres versiones. Lo que se difiere es la entrada en `docs.json`, no la
traducción.

## Convenciones de contenido

- Español rioplatense (vos, no tú). El `es/` es el canónico del tono; `en` y
  `pt-BR` son traducciones.
- Callouts: `<Note>` para aclaraciones, `<Info>` para features en beta.
- Los links internos llevan el prefijo del idioma: `/es/...`, `/pt-BR/...`, y
  sin prefijo para inglés.
