# Adaptadores por harness

El **núcleo** de `ldpddp` (`../references/` + `../assets/`) es markdown agnóstico: sirve a cualquier
agente que lea archivos, busque en el código y escriba markdown. Lo único específico de cada harness es
**cómo se activa** el contenido. Esta carpeta contiene adaptadores delgados; todos apuntan al mismo
núcleo, no lo duplican en lógica.

| Adaptador | Harness(es) | Cómo se activa |
|-----------|-------------|----------------|
| `../SKILL.md` (raíz del repo) | Claude Code (Agent Skills) | Comando `/ldpddp` + auto-discovery |
| `agents-md/` | AGENTS.md — Codex, Cursor, Aider, Gemini CLI, Zed, VS Code, Claude Code y 20+ | Contexto permanente por proyecto |

¿Falta el tuyo (Cursor `.mdc`, Copilot prompt files, Windsurf, o un modo portátil de un solo archivo)?
Sigue el mismo patrón: un archivo delgado que active el núcleo en el formato del harness, sin copiar la
lógica. Los PR son bienvenidos.
