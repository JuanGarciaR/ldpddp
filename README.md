# ldpddp — Marco de Cumplimiento Ley 21.719 (Chile) para proyectos de software

Skill de [Claude Code](https://claude.com/claude-code) que ayuda a **construir el cumplimiento de la Ley
21.719** de Protección de Datos Personales de Chile *dentro* de tus proyectos: escanea el código, detecta
datos personales y sensibles, construye el **RAT** (Registro de Actividades de Tratamiento), audita
brechas contra el articulado y genera un **backlog de remediación** accionable.

> **⚠️ Marco de ingeniería, no asesoría legal.** Traduce la ley a controles técnicos verificables. No
> reemplaza a un abogado ni a un Delegado de Protección de Datos. Las decisiones legales las confirma un
> humano.

**Por qué ahora:** la Ley 21.719 entra en **vigencia plena el 1 de diciembre de 2026**, crea la Agencia
de Protección de Datos Personales (APDP) y trae multas de hasta **20.000 UTM** o el **4% de los ingresos
anuales**. La APDP fiscaliza **evidencia**, no declaraciones.

## Instalación

Clona (o descarga) el repositorio dentro de la carpeta de skills de Claude Code, con el nombre `ldpddp`:

```bash
# macOS / Linux
git clone https://github.com/<tu-usuario>/ldpddp.git ~/.claude/skills/ldpddp
```

```powershell
# Windows / PowerShell
git clone https://github.com/<tu-usuario>/ldpddp.git "$HOME\.claude\skills\ldpddp"
```

O descarga el ZIP del repo y copia su contenido a `~/.claude/skills/ldpddp/` (Windows:
`%USERPROFILE%\.claude\skills\ldpddp\`). Luego, en cualquier proyecto, escribe `/ldpddp help`. Claude Code
auto-descubre las skills en `~/.claude/skills/*/SKILL.md`.

## Uso

```
/ldpddp             # Auditoría completa: scan → RAT → brechas → backlog
/ldpddp scan        # Inventario de datos personales/sensibles + RAT
/ldpddp audit       # Análisis de brechas contra la Ley 21.719
/ldpddp remediate   # Brechas → backlog de ingeniería (+ plantillas)
/ldpddp checklist   # Checklist de control imprimible (12 áreas)
/ldpddp status      # Scorecard de cumplimiento
/ldpddp help        # Ayuda
```

Los reportes se guardan en `.ldpddp/` dentro del proyecto, y la carpeta se añade a `.gitignore`
automáticamente — son un mapa sensible de dónde vive el PII y dónde están las brechas.

## Estructura

```
ldpddp/
├── SKILL.md                    # adaptador Claude Code + orquestador (modos, procedimiento, reglas)
├── references/                 # ← núcleo agnóstico (sirve a cualquier agente)
│   ├── ley-21719-marco.md      # ★ las 12 áreas de control + articulado + severidad
│   ├── deteccion-datos.md      # heurísticas PII/sensibles (diccionarios ES/EN, RUT/RUN)
│   ├── controles-tecnicos.md   # patrones de remediación
│   ├── reportes.md             # plantillas de salida (RAT, COMPLIANCE_REPORT, BACKLOG, scorecard)
│   └── local.example.md        # plantilla para adaptar la skill a tu entorno (opcional)
├── assets/plantillas/          # documentos fill-in (RAT, EIPD, brecha, consentimiento, ARCOP)
├── adapters/                   # ← activación en otros harnesses (mismo núcleo)
│   └── agents-md/              # AGENTS.md: Codex, Cursor, Aider, Gemini CLI, Zed, VS Code…
├── LICENSE
└── README.md
```

## Personalización (sin tocar los archivos de la skill)

¿Necesitas que la skill se comporte distinto en tu equipo — otra carpeta de salida, un handoff hacia tu
pipeline, o activar agentes/gates propios? Crea `references/local.md` con tus reglas: la skill lo detecta
en el Paso 0 y lo aplica. Ese archivo está en `.gitignore`, así que tus adaptaciones **no se publican** y
**sobreviven a los `git pull`**. Copia `references/local.example.md` como punto de partida.

## Uso en otros agentes (más allá de Claude Code)

El núcleo (`references/` + `assets/`) es markdown agnóstico; solo cambia la capa de activación. En
[`adapters/`](adapters/) hay adaptadores delgados que apuntan al mismo núcleo:

- **AGENTS.md** ([`adapters/agents-md/`](adapters/agents-md/)) — el estándar universal de contexto para
  agentes, que leen Codex, Cursor, Aider, Gemini CLI, Zed, VS Code, Claude Code y 20+ herramientas. Pega
  la sección en el `AGENTS.md` de tu proyecto y (opcional) copia el núcleo a `docs/ldpddp/` para la
  auditoría completa.

¿Quieres otro (Cursor, Copilot, Windsurf, modo portátil)? Sigue el patrón de `adapters/` — PRs bienvenidos.

## Alcance y límites

- **Cubre:** inventario de datos, RAT, análisis de brechas contra las 12 áreas de control de la ley,
  backlog de remediación con patrones y plantillas.
- **No cubre / requiere humano:** asesoría legal, calificación definitiva de bases de licitud, designación
  de DPO, redacción de contratos de encargo, y la decisión final de cumplimiento. Esos ítems se marcan
  `[REQUIERE CONFIRMACIÓN HUMANA]`.
- **No escribe código de producción:** produce el plan; la construcción pasa por tu flujo de desarrollo.
- **Citas legales:** tomadas de la síntesis oficial de la BCN; ante dudas puntuales, confirma en
  `bcn.cl/leychile` (idNorma=1209272).

## Contribuir

Las mejoras son bienvenidas: diccionarios de detección, patrones de remediación, plantillas, y
correcciones al articulado (con referencia a la fuente). Abre un issue o un pull request.

## Licencia

[MIT](LICENSE). Aviso: el contenido legal es orientativo y no constituye asesoría jurídica.

---
*Construido para pensar antes de hacer. Ley 21.719 · vigencia 1-dic-2026.*
