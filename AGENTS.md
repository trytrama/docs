# Instrucciones para agentes

Documentación pública de Trama, en Mintlify. Repo **público**: nada interno acá.

## Reglas

- **Tres idiomas en paridad.** `en` (raíz), `es/`, `pt-BR/`. Toda página existe
  en los tres con el MISMO path. Si agregás una, agregá las tres y sumá las tres
  entradas a `docs.json`.
- **`docs.json` es la única fuente de la navegación.** Un `.mdx` que no está
  referenciado ahí no aparece en el sitio.
- **No escribas páginas de API a mano.** El tab de API se genera del OpenAPI de
  `apps/api` (`https://api.trama.so/v1/openapi.json`). Si falta un endpoint, se
  arregla en el monorepo, no acá.
- **Colores: no toques `colors` sin mirar el contraste.** El amarillo de marca
  (`#FDC700`) da 1.57:1 sobre blanco — es un color de RELLENO, no de texto. Por
  eso `dark` (el color sobre fondo claro) usa `#733E0A`, que es el token
  `--primary-foreground` de `@trama/ui`.
- **Validá antes de pushear:** `bunx mint broken-links`. Un push a `main` deploya
  directo a producción.

## Convenciones de contenido

- Español rioplatense (vos, no tú). El `es/` es el canónico del tono; `en` y
  `pt-BR` son traducciones.
- Callouts: `<Note>` para aclaraciones, `<Info>` para features en beta.
- Los links internos llevan el prefijo del idioma: `/es/...`, `/pt-BR/...`, y
  sin prefijo para inglés.
