# AGENTS.md — ejemplo integrado

Así queda un `AGENTS.md` de proyecto con la sección de `ldpddp` incorporada. Las secciones de arriba son
ilustrativas (ajústalas a tu proyecto); lo aportado por este adaptador es la sección **"Protección de
datos personales"**.

---

# AGENTS.md

## Proyecto
App de fichas de pacientes. Node.js + React + PostgreSQL.

## Comandos
- Instalar dependencias: `npm install`
- Servidor de desarrollo: `npm run dev`
- Tests: `npm test`
- Lint: `npm run lint`

## Estilo de código
- TypeScript estricto. ESLint + Prettier. Commits en español.

## Protección de datos personales — Ley 21.719 (Chile)

Este proyecto trata datos personales y se rige por la **Ley 21.719** de Protección de Datos Personales de
Chile (vigencia plena **1 de diciembre de 2026**). Marco de ingeniería, **no** asesoría legal; las
decisiones legales las confirma un humano.

**Reglas permanentes (privacidad desde el diseño):**
- Minimiza: no recolectes datos personales que no necesites; por defecto opt-in, no opt-out.
- **Datos sensibles** (salud, biométricos, **situación socioeconómica**, ideología, religión, vida u
  orientación sexual, origen étnico) y **datos de NNA**: trato reforzado — cifrado, acceso restringido y
  consentimiento explícito.
- Nunca registres PII en logs. Contraseñas con `argon2`/`bcrypt`, nunca md5/sha1. Secrets fuera del repo.
- Consentimiento: acto afirmativo (sin casillas premarcadas), datado, versionado y revocable.
- Datos que salen de Chile (nubes, APIs, analítica): solo a país con nivel adecuado o con garantías.

**Auditoría de cumplimiento** (cuando se solicite, o al tocar código que trata datos personales) — si el
marco de referencia está en `docs/ldpddp/`, síguelo:
1. **Inventario + RAT:** detecta PII/sensibles con `docs/ldpddp/deteccion-datos.md`; arma el RAT con las
   plantillas de `docs/ldpddp/reportes.md`.
2. **Brechas:** evalúa las 12 áreas de control de `docs/ldpddp/ley-21719-marco.md`, con severidad
   (Leve/Grave/Gravísima) y cita de artículo. Datos sensibles o de NNA sin base → **Gravísima**.
3. **Backlog:** convierte cada brecha en una tarea con `docs/ldpddp/controles-tecnicos.md`.

Guarda los reportes en `.ldpddp/` (gitignored). Cada hallazgo con `archivo:línea`. Lo que el código no
prueba (base de licitud, DPO designado, contratos de encargo) se marca `[REQUIERE CONFIRMACIÓN HUMANA]`;
no inventes cumplimiento. No escribas código de producción sin un plan aprobado.
