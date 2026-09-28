# Extensión local (plantilla)

**Esto es una plantilla de ejemplo. Es OPCIONAL.** Para usarla, cópiala como `references/local.md` en la
carpeta de la skill y edítala con las reglas de tu entorno. `/ldpddp` carga `references/local.md` en el
Paso 0 y aplica lo que definas aquí.

`references/local.md` está en `.gitignore`: tus adaptaciones **no se publican** y **sobreviven a los
`git pull`**. Este `local.example.md` sí se versiona, solo como referencia.

Define solo lo que necesites. Ejemplos:

## Carpeta de salida
> Si tu proyecto usa una carpeta de workspace para reportes de proceso, indícala aquí. Por ejemplo:
> "Si existe `.miworkspace/` en la raíz del proyecto, guarda los reportes en `.miworkspace/compliance/`
> en vez de `.ldpddp/`."

## Handoff del backlog
> Al terminar `/ldpddp remediate`, entrega el `REMEDIATION_BACKLOG.md` a tu etapa de planificación o
> aprobación de cambios. Por ejemplo: "Ofrece llevar el backlog a `<tu comando/etapa de planificación>`."

## Agentes o gates de tu entorno
> Antes de desplegar a producción, verifica que no queden brechas Gravísimas o Graves abiertas en el
> `COMPLIANCE_REPORT.md`. Si tu entorno define un agente auditor de datos, actívalo en las revisiones.

## Ajustes de detección o severidad (opcional)
> Añade términos propios al diccionario de detección, o ajusta cómo se comunican los hallazgos según las
> convenciones de tu equipo.
