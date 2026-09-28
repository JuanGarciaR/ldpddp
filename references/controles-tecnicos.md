# Controles Técnicos de Remediación

Patrones para el modo `/ldpddp remediate`. Cada patrón: **intención → sketch mínimo → control que
satisface**. Son *guías de implementación* para el backlog, no código a insertar sin plan aprobado.
Stack-agnóstico, con notas para **Node/React** y **PHP/jQuery**.

---

## §Consentimiento — captura y registro (C5, art. 12)

**Intención:** capturar consentimiento libre, informado, específico, con acto afirmativo; guardarlo
**datado y versionado**; permitir revocación tan fácil como el otorgamiento.

- **Modelo mínimo:** tabla `consent_records(id, titular_id, finalidad, version_texto, canal, otorgado_en,
  revocado_en, evidencia)`. Una fila por finalidad y por versión del texto informado.
- **Regla:** nada de casillas premarcadas (`checked` por defecto viola "acto afirmativo"). El texto que el
  usuario aceptó se versiona: si cambia la finalidad, se re-solicita.
- **Revocación:** endpoint/acción que setea `revocado_en` y detiene el tratamiento basado en esa base.
- **Node:** middleware que verifica consentimiento vigente antes de tratar por esa finalidad.
- **PHP:** guardar en la misma transacción del alta; no confiar solo en un flag booleano en `usuarios`.

**Satisface:** C5 (consentimiento). Refuerza C2 (base de licitud) cuando la base es el consentimiento.

---

## §ARCOP — derechos del titular (C4, arts. 8 ter, 9, 11)

**Intención:** que el titular ejerza acceso, rectificación, supresión, oposición, **bloqueo** y
**portabilidad**, con respuesta registrada dentro de plazo.

| Derecho | Mecanismo mínimo |
|---------|------------------|
| Acceso | Endpoint/vista que devuelve todos los datos del titular |
| Portabilidad | Export en formato estructurado y reutilizable (JSON/CSV) |
| Rectificación | Flujo de edición con trazabilidad del cambio |
| Supresión | Borrado o anonimización + propagación a copias/backups/terceros |
| Oposición | Marca que excluye al titular de un tratamiento específico |
| Bloqueo | Suspensión temporal del tratamiento mientras se resuelve una solicitud |

- **Bitácora:** registrar cada solicitud (`solicitud-arcop.md`): fecha, tipo, estado, resolución, plazo.
- **Node:** rutas `/me/export`, `/me/delete`, `/me/rectify`, etc., autenticadas; borrado en cascada real.
- **PHP:** una pantalla de "mis datos" + procesos server-side; cuidado con borrados que rompen integridad
  referencial (preferir anonimización si hay dependencias contables/legales que obligan a conservar).

**Satisface:** C4.

---

## §RBAC y mínimo privilegio (C6)

**Intención:** que solo quien deba acceda a datos personales, y menos aún a los sensibles.

- Roles y permisos explícitos; nada de "todos los usuarios internos ven todo".
- Datos sensibles (C3) detrás de permiso específico, no del rol genérico de staff.
- **Node:** guardas por rol/scope en cada endpoint sensible. **PHP:** verificación central de permisos,
  no `if ($_SESSION['admin'])` disperso.

**Satisface:** C6 (seguridad), refuerza C3.

---

## §Cifrado en reposo y en tránsito (C6, C3)

**Intención:** proteger datos sensibles aunque se filtre la base.

- **Tránsito:** TLS en todo; sin endpoints http de datos personales.
- **Reposo:** cifrado a nivel de columna para datos sensibles (salud, biométricos, socioeconómicos).
  Claves fuera del repo (KMS/secret manager/env). Considera cifrado transparente de la BD como base, y
  cifrado a nivel de aplicación para las columnas más críticas.
- **Contraseñas:** `argon2` o `bcrypt`. **Nunca** md5/sha1/texto plano (hallazgo Grave inmediato).
- **PHP heredado:** reemplazar `md5()` por `password_hash()`/`password_verify()` en la migración.

**Satisface:** C6; imprescindible para C3.

---

## §Audit log de acceso a datos personales (C6, C8)

**Intención:** saber quién accedió a qué dato personal y cuándo (evidencia para la APDP y para detectar
brechas).

- Log append-only: `actor, accion, recurso, titular_id, timestamp`. **No** registrar el dato sensible en
  el log (eso sería otra fuga) — registrar referencias, no valores.
- Alertas ante patrones anómalos (descargas masivas, accesos fuera de horario).

**Satisface:** C6; habilita C8 (detección de vulneraciones).

---

## §Retención y minimización (C7)

**Intención:** no conservar datos más allá de su finalidad; no recolectar de más.

- Plazos de retención por actividad (en el RAT). Job programado que elimina o anonimiza al vencer.
- Revisar formularios/DTOs: quitar campos que no se usan (sobre-recolección).
- **Node:** cron/worker de retención. **PHP:** tarea programada (cron del servidor) documentada.

**Satisface:** C7.

---

## §Seudonimización / anonimización (C6, C7)

**Intención:** reducir el riesgo separando identidad de datos, o eliminando la reidentificación.

- **Seudonimización:** reemplazar identificadores directos (RUT, email) por tokens; tabla de mapeo
  aparte y protegida. Útil para analítica y ambientes de prueba.
- **Anonimización:** irreversible; datos que ya no permiten reidentificar salen del alcance de la ley.
- **Seeds/fixtures:** nunca datos reales de producción; generar RUT/emails ficticios.

**Satisface:** C6, C7; reduce alcance regulatorio.

---

## §Detección y respuesta a brechas (C8, art. 14 sexies)

**Intención:** detectar vulneraciones y reportarlas "por los medios más expeditos, sin dilaciones
indebidas" (recuerda: **72h es GDPR/Ley 21.663, no la 21.719**).

- Detección: alertas del audit log + monitoreo de accesos indebidos/fugas.
- Procedimiento documentado: quién evalúa, cómo se contiene, cuándo se notifica a la APDP y a los
  titulares afectados. Plantillas en `brecha-notificacion.md`.

**Satisface:** C8.

---

## §Transferencias internacionales (C10, arts. 27-29)

**Intención:** que los datos que salen de Chile tengan país adecuado o garantías.

- Inventariar destinos (cloud, APIs, analítica, CDNs) y su ubicación de almacenamiento.
- Dejar constancia en el RAT del mecanismo por destinatario (país adecuado / cláusulas tipo /
  certificación). Sin garantía → infracción Grave.

**Satisface:** C10.

---

## §Privacidad por defecto (C11)

**Intención:** que la opción más protectora sea la predeterminada.

- Opt-in (no opt-out) para tratamientos no esenciales; visibilidad mínima por defecto; recolectar lo
  mínimo. Aplícalo al crear features nuevas.

**Satisface:** C11.
