# CLAUDE.md

Las instrucciones de este repo son las de `AGENTS.md` — mismas reglas para todos
los agentes, un solo archivo para que no diverjan. Se importan acá:

@AGENTS.md

Abajo, sólo lo que es específico de Claude Code.

## Herramientas

- **Runtime: `bun`.** Nunca `npm` / `pnpm` / `yarn`.
- **Preview local:** `bunx mint dev` (hot reload; toma archivos nuevos sin
  reiniciar). Si el puerto está ocupado, `--port 3111`.
- **Único gate antes de pushear:** `bunx mint broken-links`. Acá no hay typecheck
  ni lint — es lo único que corre, y un push a `main` sale a producción sin
  revisión de nadie.

## Antes de commitear

- **Confirmá branch y worktree** (`git rev-parse --abbrev-ref HEAD`,
  `git worktree list`). Este repo se trabaja desde varios worktrees en paralelo;
  no commitees en uno inesperado.
- **Stageá sólo lo del cambio en curso.** Nada de `git add -A`.
- **`openapi.json` es generado.** No lo edites ni lo incluyas en un commit a
  mano: lo pisa `sync-openapi.yml` en el próximo tick.

## Recordá que el repo es público

Nada de nombres de clientes, credenciales, endpoints internos ni detalles de
arquitectura. Eso vive en `docs/` del monorepo, que es privado.
