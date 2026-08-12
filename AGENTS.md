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

## Glosario canónico

**La app es la fuente de verdad.** Si la doc y la interfaz no coinciden, la que
está mal es la doc. Esto no es pedantería: la documentación acumuló nombres de
una versión anterior del producto y hoy manda al lector a buscar secciones que
no existen con ese nombre.

### Secciones (los nombres de la barra de navegación)

| Canónico (es) | en | pt-BR | Qué es |
|---|---|---|---|
| **Mission Control** | Mission Control | Mission Control | Vista diaria. Pestañas: **Mi día**, **Resumen**, **Métricas**. **No** es el tablero |
| **Oportunidades** | Opportunities | Oportunidades | El tablero Kanban. Pestañas **Pipeline** y **Fuera del funnel** |
| **Inbox** | Inbox | Inbox | Donde llegan y se leen las consultas. Es el nombre en la interfaz: no lo llames "Conversaciones" ni "Consultas" |
| **Clientes** | Customers | Clientes | |
| **Herramientas** | Tools | Ferramentas | Menú: Catálogo, Cotizaciones, Link de ofertas, Anuncios |
| **Agentes** | Agents | Agentes | Los agentes de la organización |
| **Automatizaciones** | Automations | Automações | Pestañas: Reparto, Recordatorios, Ciclo de vida, Respuestas |
| **Equipo** | Team | Equipe | |
| **Canales** | Channels | Canais | Chat web, WhatsApp, Instagram, Messenger, Gmail |
| **Configuración** | Settings | Configurações | |

**Mi día es una pestaña de Mission Control, no una sección.** Y las columnas del
tablero son **Por atender · En contacto · Cotizado · Ganado · Perdido**, escritas
así — ni en mayúsculas ni traducidas literalmente ("TO ATTEND" es calco: en
inglés va **To contact**).

### Los dos agentes — la confusión más cara del sitio

Son dos productos distintos y hoy comparten cuatro nombres entre sí:

- **Agente de Consultas** (*Inquiry Agent*): el que atiende a quien escribe. Vive
  en **Agentes**, tiene nombre propio (en la demo, Tramy) y se configura en cinco
  pestañas: Identidad, Tu agencia, Reglas, Turnos, Cierre.
- **Trama Agent**: el copiloto del equipo, en el número central de Trama. Es a
  quien el vendedor le pregunta "¿qué tengo pendiente hoy?".

Nunca escribas "Trama agent" en minúscula para referirte al primero, ni
"Operations Agent" / "Agente Operativo" para el segundo. Un solo nombre cada uno,
en los tres idiomas.

### Las personas

| Rol | Canónico (es) | en | Nunca |
|---|---|---|---|
| Quien escribe | **cliente** | customer | traveler, passenger, visitor, lead, pasajero, viajero |
| Quien vende | **vendedor** | sales rep | seller, advisor, asesor |
| Roles del producto | **Dueño / Administrador / Vendedor** | Owner / Admin / Member | Traducir a medias ("solo Owner o Admin" en una página en español) |

Excepción: **lead** sigue siendo válido cuando se habla de anuncios y medición
(la pantalla de Anuncios cuenta "leads"), no como sinónimo de cliente.

### Score y temperatura

Una sola cosa con dos representaciones: un **score** numérico sobre 100 y una
**temperatura** derivada (**Caliente / Tibio / Frío**, más **Sin score** cuando
todavía no se calculó). No inventes un cuarto valor: "descartada" es un estado
del Inbox, no una temperatura.

### Posicionamiento

Trama es **un CRM que se llena solo**. Esa es la fórmula, y va igual en las tres
versiones. No escribas "no es un CRM" en una página y "el CRM está incluido" en
otra, que es lo que pasaba.

## Capturas de pantalla

Las capturas salen de la organización demo **Trama Tourism**, nunca de una cuenta
real. Antes de commitear una imagen, mirala: el repo es público y una vez que la
imagen entra, queda en el historial de git aunque después la borres. Revisá que
no aparezcan nombres de clientes reales, emails, teléfonos, IDs de anuncios de
Meta, ni la pantalla de **Facturación** (queda fuera de la doc por decisión de
producto).

- Van en `images/<seccion>/<pagina>-<n>.png` y se referencian con **path
  absoluto desde la raíz del repo** (`/images/...`). Los paths relativos no
  funcionan en Mintlify.
- **Par claro/oscuro.** La app tiene switch de tema, así que se capturan las dos
  y se sirven con las clases de Tailwind:

  ```mdx
  <img className="block dark:hidden" src="/images/agente/identidad-light.png" alt="..." />
  <img className="hidden dark:block" src="/images/agente/identidad-dark.png" alt="..." />
  ```

- Viewport fijo **1440×900** y zoom 100%, para que todas las imágenes del sitio
  tengan la misma escala tipográfica.
- Para movimiento, **`.webm`**, no GIF: pesa entre 10 y 50 veces menos, y el GIF
  además no admite par claro/oscuro. Ya hay un precedente en
  `channels/whatsapp-setup.mdx`.
- La interfaz de la plataforma **sólo existe en español**. Las capturas van en
  español también en `en/` y `pt-BR/`; es una decisión consciente, no un olvido.

## Antes de dar por buena una página

Abrí la pantalla que documenta y comparala. Las páginas de este repo
describieron durante meses una organización de secciones que el producto ya no
tiene, y nada falló: no hay test que agarre eso.
