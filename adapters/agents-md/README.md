# Adaptador AGENTS.md

[`AGENTS.md`](https://agents.md/) es el archivo de contexto estándar para agentes de coding: Markdown
plano en la raíz del repo, sin campos obligatorios, bajo la Agentic AI Foundation (Linux Foundation). Lo
leen Codex, Cursor, Aider, Gemini CLI, Zed, VS Code, Claude Code y otros — más de 20 herramientas.

A diferencia de un comando `/ldpddp`, `AGENTS.md` es **contexto permanente por proyecto**: el agente lo
lee siempre. Por eso el adaptador tiene dos niveles.

## Nivel 1 — Reglas permanentes (mínimo)

Pega el contenido de [`AGENTS.section.md`](AGENTS.section.md) en el `AGENTS.md` de tu proyecto (créalo en
la raíz si no existe). Con esto, cualquier agente compatible sabe que el proyecto trata datos personales
bajo la Ley 21.719 y aplica **privacidad desde el diseño** en todo lo que hace.

## Nivel 2 — Auditoría completa (recomendado)

Además, copia el núcleo de referencia al proyecto para que el agente pueda hacer el inventario, el RAT,
el análisis de brechas y el backlog:

```bash
# desde una copia de este repo, en la raíz de tu proyecto objetivo
mkdir -p docs/ldpddp
cp -R /ruta/al/repo/ldpddp/references/* /ruta/al/repo/ldpddp/assets/plantillas docs/ldpddp/
```

```powershell
# Windows / PowerShell
New-Item -ItemType Directory -Force docs\ldpddp | Out-Null
Copy-Item -Recurse "C:\ruta\al\repo\ldpddp\references\*","C:\ruta\al\repo\ldpddp\assets\plantillas" docs\ldpddp\
```

El snippet ya referencia `docs/ldpddp/`; ajusta la ruta si la cambias. Los reportes generados van a
`.ldpddp/` — añádela a tu `.gitignore` (son un mapa sensible de dónde vive el PII).

## Verlo integrado

[`AGENTS.example.md`](AGENTS.example.md) muestra cómo queda un `AGENTS.md` real con la sección
incorporada.

## Límite honesto

`AGENTS.md` no invoca comandos: el flujo se dispara pidiéndolo en lenguaje natural ("audita el
cumplimiento de la Ley 21.719") o cuando el agente detecta que toca datos personales. La sustancia —el
marco, la detección, los controles— es idéntica a la skill de Claude Code.
