# Plantillas de Salida

Plantillas de los reportes que genera `/ldpddp`. Se guardan en la carpeta de salida definida en el
Paso 0 (por defecto `.ldpddp/`). Todo reporte abre con el disclaimer y la fecha.

Encabezado común de cada reporte:

```
> ⚠️ Marco de ingeniería, no asesoría legal. Generado por /ldpddp — Ley 21.719 (Chile).
**Proyecto:** [nombre]   **Fecha:** [YYYY-MM-DD]
```

---

## 1. `DATA_INVENTORY.md` — Inventario de datos

```markdown
# Inventario de Datos Personales — [proyecto]
[encabezado común]

## Resumen
- Campos personales detectados: [N]
- Campos sensibles detectados: [N]  ⚠️
- Datos de NNA: [sí/no]
- Transferencia internacional detectada: [sí/no/indeterminado]

## Hallazgos
| Campo/columna | Ubicación | Clasificación | Categoría | Notas |
|---------------|-----------|---------------|-----------|-------|
| ... | archivo:línea | Personal/Sensible/NNA | ... | ... |

## Actividades de tratamiento inferidas
[Agrupación de campos en actividades: p. ej. "Registro de usuarios", "Pagos", "Soporte"]
```

---

## 2. `RAT.md` — Registro de Actividades de Tratamiento

Una fila por actividad. Lo que el código no revela → `[REQUIERE CONFIRMACIÓN HUMANA]`.

```markdown
# Registro de Actividades de Tratamiento (RAT) — [proyecto]
[encabezado común]
> Instrumento de evidencia (art. 14 ter + art. 3° e). La APDP fiscaliza evidencia, no declaraciones.

| # | Actividad | Finalidad | Categorías de datos | ¿Sensibles? | Titulares | Base de licitud (art. 12/13) | Conservación | Destinatarios | Transf. internacional | Medidas de seguridad |
|---|-----------|-----------|---------------------|-------------|-----------|------------------------------|--------------|---------------|-----------------------|----------------------|
| 1 | Registro de usuarios | Crear cuenta | nombre, rut, email | No | Clientes | [REQUIERE CONFIRMACIÓN] | [REQUIERE CONFIRMACIÓN] | — | No | Hashing pass, TLS |

## Pendientes de confirmación humana
- [ ] Base de licitud de cada actividad
- [ ] Plazos de conservación
- [ ] Encargados de tratamiento (terceros) y contratos
```

---

## 3. `COMPLIANCE_REPORT.md` — Análisis de brechas

Estilo: resumen ejecutivo honesto + estado por control + brechas priorizadas.

```markdown
# Reporte de Cumplimiento — Ley 21.719 — [proyecto]
[encabezado común]

## Resumen ejecutivo
[Párrafo honesto del estado de cumplimiento. Sin suavizar. El usuario necesita saber su exposición real
a ~5 meses de la vigencia.]

**Nivel de cumplimiento general:** [CRÍTICO / BAJO / MEDIO / ALTO]
**Brechas Gravísimas:** [N]   **Graves:** [N]   **Leves:** [N]

## Estado por área de control
| # | Área | Estado | Severidad si falta | Hallazgos (archivo:línea) | Artículo |
|---|------|--------|--------------------|---------------------------|----------|
| C1 | Inventario y RAT | Parcial | Leve | ... | Art. 14 ter / 3° e |
| C2 | Bases de licitud | No cumple | Grave | ... | Art. 12/13 |
| C3 | Datos sensibles | No cumple | **Gravísima** | tabla_pacientes.salud (schema.sql:22) | Art. 16 bis |
| ... | ... | ... | ... | ... | ... |

Estados: **Cumple / Parcial / No cumple / [REQUIERE CONFIRMACIÓN HUMANA]**.

## Brechas priorizadas
### Gravísimas
[Detalle: qué falta, dónde, por qué es gravísima, qué control C la cubre]
### Graves
[...]
### Leves
[...]

## Lo que ya está bien
[Sección honesta: controles que el proyecto ya cumple.]

---
*Siguiente paso: `/ldpddp remediate`*
```

---

## 4. `REMEDIATION_BACKLOG.md` — Backlog de ingeniería

Formateado para alimentar tu etapa de planificación o aprobación de cambios, o ejecutarse de forma autónoma.

```markdown
# Backlog de Remediación — Ley 21.719 — [proyecto]
[encabezado común]

Ordenado por severidad. Cada ítem: control, patrón, plantilla, esfuerzo, criterio de "hecho".

## [GRAVÍSIMA] R-01 — Cifrar y restringir datos de salud
- **Control:** C3 (datos sensibles, art. 16 bis) · C6 (seguridad)
- **Brecha:** `pacientes.diagnostico` en claro, sin control de acceso (models/Paciente.php:22)
- **Patrón:** cifrado en reposo a nivel de columna + RBAC (ver controles-tecnicos.md §Cifrado, §RBAC)
- **Plantilla:** —
- **Esfuerzo:** M
- **Hecho cuando:** la columna está cifrada, el acceso queda logueado y restringido por rol.

## [GRAVE] R-02 — Implementar derechos ARCOP
- **Control:** C4 (art. 8 ter, 9, 11)
- **Patrón:** endpoints export/delete/rectify/block (ver controles-tecnicos.md §ARCOP)
- **Plantilla:** `solicitud-arcop.md`
- **Esfuerzo:** L
- **Hecho cuando:** el titular puede ejercer los 6 derechos y queda registrado.

[... resto de ítems ...]

## Handoff
- Lleva este backlog a tu etapa de planificación o aprobación de cambios para su construcción.
- No tocar código sin un plan aprobado.
```

---

## 5. Scorecard (`/ldpddp status`)

Salida compacta en chat (no necesariamente archivo), o `SCORECARD.md`:

```markdown
# Scorecard de Cumplimiento — [proyecto] — [fecha]
Cumplimiento general: MEDIO  ·  Áreas: 4 Cumple / 5 Parcial / 3 No cumple
Brechas abiertas: 2 Gravísimas · 3 Graves · 4 Leves
Δ desde última corrida ([fecha]): +2 áreas resueltas, -1 brecha gravísima

Top 3 pendientes:
1. [GRAVÍSIMA] Datos de salud sin cifrar (C3/C6)
2. [GRAVE] Sin mecanismos ARCOP (C4)
3. [GRAVE] Sin base de licitud documentada en 4 actividades (C2)
```
