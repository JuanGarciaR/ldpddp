---
name: ldpddp
description: Marco de trabajo y control para cumplir la Ley 21.719 de Protección de Datos Personales de Chile (en vigor el 1 de diciembre de 2026) dentro de proyectos de software. Escanea el código para detectar datos personales y sensibles, construye el Registro de Actividades de Tratamiento (RAT), audita brechas de cumplimiento contra el articulado, y genera un backlog de remediación con patrones y plantillas. Úsala cuando el usuario mencione protección de datos personales, Ley 21.719, LDPDP, RAT, derechos ARCOP, datos sensibles, consentimiento, evaluación de impacto (EIPD), cumplimiento de privacidad, o cuando escriba /ldpddp.
---

# /ldpddp — Marco de Cumplimiento Ley 21.719 (Chile)

Escanea cualquier proyecto de software, detecta el tratamiento de datos personales y sensibles, lo
contrasta contra las obligaciones concretas de la **Ley 21.719** de Chile, y produce reportes y un
**backlog de remediación** accionable. Construye el cumplimiento *dentro* del proyecto, no lo audita al
final.

> **⚠️ Esto es un marco de ingeniería, NO asesoría legal.** Ayuda a traducir la ley en controles
> técnicos verificables; no reemplaza a un abogado ni a un Delegado de Protección de Datos. Las
> decisiones legales (designar DPO, redactar contratos, calificar bases de licitud) las confirma un
> humano.

## Contexto de la ley (por qué existe esta skill)

La Ley 21.719 (publicada 13-dic-2024) **entra en vigencia plena el 1 de diciembre de 2026**. Reemplaza
casi por completo la Ley 19.628, se alinea con el GDPR europeo, crea la **Agencia de Protección de Datos
Personales (APDP)** y establece un régimen sancionatorio real: multas de hasta **20.000 UTM** (≈ USD
1,4M) o el **4% de los ingresos anuales**. La APDP **fiscaliza evidencia, no declaraciones** — por eso
esta skill produce artefactos verificables (inventario, RAT, hallazgos con `archivo:línea`), no
promesas.

## Uso

```
/ldpddp             # Auditoría completa: scan → RAT → brechas → backlog de remediación
/ldpddp scan        # Solo inventario de datos personales/sensibles + RAT
/ldpddp audit       # Solo análisis de brechas contra la Ley 21.719 (requiere inventario previo)
/ldpddp remediate   # Convierte las brechas en un backlog de ingeniería (+ plantillas)
/ldpddp checklist   # Checklist de control imprimible (las 12 áreas, sin escanear código)
/ldpddp status      # Scorecard de cumplimiento / progreso desde la última corrida
/ldpddp help        # Muestra esta ayuda
```

Si el usuario invoca `/ldpddp --help` o `/ldpddp help`, imprime la sección **Uso** de arriba y detente.

## Paso 0 — Preparación (siempre, antes de cualquier modo)

1. **Extensión local (opcional).** Si existe el archivo `references/local.md` en la carpeta de esta
   skill, léelo y aplica sus indicaciones antes de continuar. Una extensión local puede redefinir la
   carpeta de salida de los reportes, añadir un handoff hacia una etapa de tu flujo de trabajo, o activar
   agentes o gates propios de tu entorno. Si no existe, opera con los valores por defecto de esta guía.
   (Ver `references/local.example.md` para la plantilla.)
2. **Carpeta de salida.** Por defecto, guarda todos los reportes en **`.ldpddp/`** en la raíz del
   proyecto. (Una extensión local puede redefinir esta ruta.)
3. **Protege el workspace.** Si el proyecto es un repositorio git, verifica que la carpeta de salida esté
   en `.gitignore`; si no está, añádela e informa al usuario. Los reportes de esta skill son un **mapa de
   dónde vive el PII y dónde están las brechas de privacidad** — no deben viajar en el repo ni llegar a un
   cliente o repo público.
4. **Anuncia** dónde aparecerán los archivos.

## Procedimiento por modo

Carga los archivos de `references/` **bajo demanda**, solo cuando el modo los necesita, para mantener el
contexto liviano.

### `/ldpddp scan` — Inventario + RAT

1. Carga `references/deteccion-datos.md`. Aplica sus diccionarios y patrones (incluido el **RUT/RUN
   chileno**) sobre: esquemas y migraciones de BD, modelos/entidades ORM, formularios, DTOs y
   validadores de API, variables de entorno y config, logs, seeds/fixtures.
2. Clasifica cada hallazgo como **dato personal**, **dato sensible** (ver categorías en
   `references/ley-21719-marco.md`, C3) o **dato de NNA**, con su ubicación `archivo:línea`.
3. Agrupa los hallazgos en **actividades de tratamiento** (p. ej. "registro de usuarios", "procesamiento
   de pagos", "soporte"). Para cada una infiere: finalidad, categorías de datos, categorías de titulares,
   posibles destinatarios y si hay transferencia internacional.
4. Genera el **inventario** y el **RAT** usando las plantillas de `references/reportes.md`. Donde falte
   información que el código no revela (base de licitud, plazo de conservación, encargados), déjalo
   marcado como **`[REQUIERE CONFIRMACIÓN HUMANA]`** — nunca lo inventes.
5. Guarda `DATA_INVENTORY.md` y `RAT.md` en la carpeta de salida. Informa un resumen y sugiere `/ldpddp audit`.

### `/ldpddp audit` — Análisis de brechas

1. Si no existe un inventario previo, ejecuta primero el flujo de `scan`.
2. Carga `references/ley-21719-marco.md`. Recorre las **12 áreas de control (C1–C12)**. Para cada una
   evalúa el estado real del proyecto: **Cumple / Parcial / No cumple / Requiere confirmación humana**,
   con hallazgos concretos (`archivo:línea`) y la cita del artículo correspondiente.
3. Asigna **severidad** a cada brecha mapeándola al régimen sancionatorio (Leve / Grave / Gravísima —
   ver la tabla en el reference). Tratar datos sensibles o de NNA sin base es **Gravísima**.
4. Genera `COMPLIANCE_REPORT.md` con la plantilla de `references/reportes.md`: resumen ejecutivo honesto,
   estado por control, y las brechas priorizadas. Informa las más críticas y sugiere `/ldpddp remediate`.

### `/ldpddp remediate` — Backlog de ingeniería

1. Requiere un `COMPLIANCE_REPORT.md` previo. Carga `references/controles-tecnicos.md`.
2. Convierte cada brecha en una **tarea de backlog** con: control asociado (Cx), patrón técnico de
   remediación, plantilla aplicable (`assets/plantillas/…`), esfuerzo estimado y criterio de "hecho".
3. Ordena por severidad (Gravísima → Leve). Genera `REMEDIATION_BACKLOG.md`.
4. **No escribes código de producción.** Produces el plan. Entrega el `REMEDIATION_BACKLOG.md`; si tu
   flujo de trabajo tiene una etapa de planificación o aprobación de cambios, pásalo a esa etapa para su
   construcción. (Una extensión local puede definir ese handoff de forma explícita.)
5. Copia a la carpeta de salida las plantillas de `assets/plantillas/` que el proyecto deba mantener
   (RAT en blanco, EIPD, notificación de brecha, registro de consentimiento, bitácora ARCOP).

### `/ldpddp checklist` — Checklist imprimible

Carga `references/ley-21719-marco.md` y emite el checklist de las 12 áreas (sin escanear código), como
una lista de verificación marcable. Útil para revisiones rápidas o para arrancar un proyecto nuevo con
privacidad desde el diseño.

### `/ldpddp status` — Scorecard

Lee los reportes previos en la carpeta de salida y emite un **scorecard** compacto (áreas Cumple/Parcial/
No cumple, número de brechas críticas abiertas, progreso desde la última corrida). Si no hay reportes
previos, sugiere ejecutar `/ldpddp` primero.

### `/ldpddp` (sin argumento) — Auditoría completa

Ejecuta en secuencia `scan` → `audit` → `remediate`, sin esperar confirmación entre etapas, y presenta
al final un resumen con el scorecard y las 3 brechas más críticas.

## Reglas

- **No es asesoría legal.** Declara el disclaimer en cada reporte generado.
- **No escribes código de producción.** Esta skill genera reportes, backlog y plantillas de
  documentación. La construcción de código pasa por tu flujo de desarrollo, con un plan aprobado antes de
  tocar código.
- **Evidencia > declaraciones.** Cada hallazgo lleva `archivo:línea`. La APDP fiscaliza evidencia.
- **Severidad real.** Mapea al régimen sancionatorio; no exageres ni minimices.
- **Honestidad ante lo no verificable.** Lo que el código no puede probar (si hay un DPO designado, si
  existe un contrato de encargo, la base de licitud real de una actividad) se marca
  `[REQUIERE CONFIRMACIÓN HUMANA]`. **Nunca inventes cumplimiento.**
- **Cita el articulado con cuidado.** Los números de artículo del reference están tomados de la síntesis
  oficial de la BCN; si tienes dudas puntuales, cítalo como "según texto refundido" y remite al usuario a
  `bcn.cl/leychile` (idNorma=1209272).
- **Protege el workspace.** La carpeta de salida siempre gitignored.

## Personalización (extensión local)

Esta skill es genérica y autónoma. Puedes adaptarla a tu entorno o pipeline **sin modificar sus
archivos**: crea `references/local.md` en la carpeta de la skill con tus reglas propias — dónde guardar
los reportes, cómo hacer el handoff del backlog a tu flujo, o qué agentes/gates activar. La skill detecta
ese archivo en el Paso 0 y lo aplica. Como `references/local.md` no forma parte del repositorio (está en
`.gitignore`), tus adaptaciones sobreviven a las actualizaciones (`git pull`). Ver
`references/local.example.md` para la plantilla.
