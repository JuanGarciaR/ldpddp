# Marco de Control — Ley 21.719 (Chile)

Traducción de la **Ley 21.719** (que reforma la Ley 19.628 sobre protección de la vida privada) a
**controles de ingeniería verificables**. Es la fuente de verdad del modo `/ldpddp audit`.

> **Sobre las citas de artículos:** los números provienen de la *Descripción y síntesis de la Ley
> 21.719* de la Biblioteca del Congreso Nacional (BCN). La 21.719 reemplaza casi todos los artículos de
> la 19.628, por lo que la numeración vigente difiere de la ley antigua. Ante cualquier duda puntual,
> confirma en `bcn.cl/leychile` (idNorma=1209272). No es asesoría legal.

**Vigencia plena:** 1 de diciembre de 2026 (artículo primero transitorio).
**Autoridad:** Agencia de Protección de Datos Personales (APDP).
**Principios rectores (art. 3°):** licitud y lealtad, finalidad, proporcionalidad, calidad,
responsabilidad (accountability, art. 3° e), seguridad, transparencia e información, confidencialidad.

---

## Régimen sancionatorio (para mapear severidad)

| Severidad | Multa máxima | Ejemplos (art. 34 y ss.) |
|-----------|--------------|--------------------------|
| **Leve** | 5.000 UTM | Información incompleta, deber de transparencia mal cumplido, registro desactualizado |
| **Grave** | 10.000 UTM | Tratar sin base de licitud, no adoptar medidas de seguridad, no realizar EIPD exigible |
| **Gravísima** | 20.000 UTM | Tratar datos **sensibles** o de **NNA** sin fundamento, a sabiendas (art. 34 ter); obstrucción a la APDP |

**Reincidencia:** hasta triplicar la multa **o** el **4% de los ingresos anuales** por ventas y servicios
en Chile (lo que resulte mayor). **PYME (Ley 20.416):** primera infracción = amonestación escrita.
**Registro Nacional de Sanciones y Cumplimiento:** público; la inscripción por sanción dura 5 años.

**Regla de mapeo de severidad en el audit:**
- Falta un control que involucra **datos sensibles o de NNA** → **Gravísima**.
- Falta una base de licitud, medidas de seguridad, o EIPD exigible → **Grave**.
- Falta transparencia, RAT/evidencia, o hay desactualización documental → **Leve**.

---

## Las 12 áreas de control

Cada área: **obligación legal (artículo) · qué significa · cómo se verifica en el código · control de
ingeniería que la satisface · severidad si falta.**

### C1 — Inventario y RAT (evidencia de tratamiento)
- **Base:** deber de información y transparencia (**art. 14 ter**) + principio de responsabilidad
  (**art. 3° e**). *Nota: la 21.719 no crea un "registro de actividades de tratamiento" con un artículo
  único al estilo GDPR Art. 30; el RAT es el instrumento operativo para demostrar cumplimiento y
  transparencia. La APDP fiscaliza evidencia.*
- **Qué significa:** existe un inventario actualizado de qué datos se tratan, con qué finalidad, bajo qué
  base de licitud, por cuánto tiempo, con qué destinatarios y con qué medidas de seguridad.
- **Verificación en código:** ¿hay un mapa de datos vivo? Normalmente NO existe → se construye desde el
  esquema de BD, modelos y formularios (ver `deteccion-datos.md`).
- **Control:** `RAT.md` mantenido (una fila por actividad de tratamiento) + proceso para actualizarlo.
- **Severidad si falta:** Leve (documental), pero es el cimiento de todo lo demás.

### C2 — Bases de licitud
- **Base:** consentimiento (**art. 12**); otras fuentes de licitud sin consentimiento (**art. 13**):
  obligación legal, interés legítimo del responsable, ejecución de contrato, formulación/ejercicio/defensa
  de derechos, etc. El responsable **debe acreditar** la licitud.
- **Qué significa:** toda actividad de tratamiento tiene una base de licitud documentada. No todo requiere
  consentimiento.
- **Verificación en código:** para cada actividad del RAT, ¿está declarada la base? El código rara vez lo
  dice → marca `[REQUIERE CONFIRMACIÓN HUMANA]` y exige que el humano asigne la base.
- **Control:** columna "base de licitud" en el RAT, justificada por actividad.
- **Severidad si falta:** Grave (tratar sin base de licitud es infracción grave).

### C3 — Datos sensibles
- **Base:** norma general (**art. 16**); salud y perfil biológico (**art. 16 bis**); datos biométricos
  (**art. 16 ter**). Regla general: **consentimiento explícito**, con excepciones tasadas.
- **Categorías de datos sensibles en Chile** (nótese lo distintivo frente al GDPR):
  origen étnico o racial · afiliación política, sindical o gremial · **situación socioeconómica** ·
  convicciones ideológicas o filosóficas · creencias religiosas · datos de salud · perfil biológico ·
  vida sexual · orientación sexual · datos biométricos · datos genéticos.
- **Verificación en código:** campos/columnas que revelen cualquiera de las categorías anteriores (ver
  diccionario en `deteccion-datos.md`).
- **Control:** consentimiento explícito registrado + cifrado + acceso restringido + minimización. Evitar
  recolectarlos si no son imprescindibles.
- **Severidad si falta:** **Gravísima**.

### C4 — Derechos del titular (ARCOP + bloqueo)
- **Base:** acceso, rectificación, supresión (derecho al olvido) y oposición (arts. 5 a 8, según texto
  refundido); **bloqueo** temporal del tratamiento (**art. 8 ter**); **portabilidad** (**art. 9**);
  procedimiento y plazo de respuesta / reclamación ante la APDP (**art. 11**, y tutela **art. 41**).
- **Qué significa:** el titular puede ejercer estos derechos y el responsable debe responder por medios
  electrónicos dentro de plazo.
- **Verificación en código:** ¿existen mecanismos para exportar (acceso/portabilidad), corregir
  (rectificación), eliminar (supresión), suspender (bloqueo) y registrar oposición? Usualmente faltan.
- **Control:** endpoints/flujos ARCOP + una bitácora de solicitudes (`solicitud-arcop.md`).
- **Severidad si falta:** Grave.

### C5 — Consentimiento
- **Base:** **art. 12** — libre, informado, específico, mediante acto afirmativo; **esencialmente
  revocable**; los medios para otorgar/revocar deben ser "expeditos, fidedignos, gratuitos y
  permanentemente disponibles" (art. 12, inc. 5°). Se **presume no libre** si se recaba en un contrato
  donde esa recolección no es necesaria (inc. 6°).
- **Verificación en código:** ¿se captura consentimiento con acto afirmativo (no casillas premarcadas)?
  ¿se guarda **datado y versionado**? ¿se puede **revocar tan fácil como se otorga**?
- **Control:** registro de consentimiento datado/versionado (`registro-consentimiento.md`) + flujo de
  revocación.
- **Severidad si falta:** Grave (si es la base declarada) o Gravísima (si aplica a datos sensibles).

### C6 — Seguridad
- **Base:** deber de adoptar medidas de seguridad (**art. 14 quinquies**) + deber de secreto/
  confidencialidad (**art. 14 bis**) + principio de seguridad (art. 3°).
- **Verificación en código:** cifrado en tránsito (TLS) y en reposo para datos sensibles; hashing de
  contraseñas (bcrypt/argon2, no md5/sha1); control de acceso por rol y mínimo privilegio; seudonimización
  donde sea posible; registro de acceso (audit log) a datos personales; secrets fuera del repo.
- **Control:** ver `controles-tecnicos.md` (cifrado, RBAC, audit log, seudonimización).
- **Severidad si falta:** Grave (Gravísima si expone datos sensibles).

### C7 — Conservación y minimización
- **Base:** principios de finalidad y proporcionalidad (art. 3°). Los datos no se conservan más allá de
  la finalidad.
- **Verificación en código:** ¿hay plazos de retención definidos? ¿existe eliminación/anonimización
  automatizada? ¿se recolectan campos que no se usan (sobre-recolección)?
- **Control:** política de retención + jobs de eliminación/anonimización programados.
- **Severidad si falta:** Leve/Grave según el dato (Gravísima si retiene sensibles sin base).

### C8 — Notificación de brechas (vulneraciones)
- **Base:** deber de reportar vulneraciones de las medidas de seguridad ante la APDP (**art. 14 sexies**).
  El reporte debe hacerse **por los medios más expeditos posibles y sin dilaciones indebidas**.
  *Nota importante:* el plazo de **72 horas** pertenece al GDPR europeo y a la Ley 21.663 de
  ciberseguridad — **no** a la 21.719. No cites "72 horas" como plazo de la ley de datos.
- **Verificación en código:** ¿hay detección/logging/alerta de accesos indebidos o fugas? ¿existe un
  procedimiento y plantillas de notificación?
- **Control:** detección + procedimiento de respuesta + plantilla `brecha-notificacion.md` (a la APDP y a
  los titulares afectados).
- **Severidad si falta:** Grave.

### C9 — Evaluación de Impacto (EIPD)
- **Base:** **art. 15 ter** — evaluación previa cuando el tratamiento sea probable que produzca un
  **alto riesgo** para los derechos de los titulares.
- **Criterios de alto riesgo:** perfilamiento/decisiones automatizadas sistemáticas; tratamiento masivo;
  vigilancia de zonas de acceso público; tratamiento de datos sensibles o de NNA a gran escala.
- **Verificación en código:** ¿el proyecto cae en algún criterio? Si sí y no hay EIPD documentada → brecha.
- **Control:** EIPD documentada antes de iniciar (`EIPD.md`).
- **Severidad si falta:** Grave.

### C10 — Transferencias internacionales
- **Base:** licitud y garantías de la transferencia (**art. 27**, con excepciones tasadas); países con
  nivel adecuado de protección (**art. 28**); fiscalización por la APDP (**art. 29**).
- **Verificación en código:** ¿los datos salen de Chile? Detecta proveedores cloud, APIs de terceros,
  analítica, y CDNs con almacenamiento fuera del país (env/config, SDKs, endpoints).
- **Control:** transferir solo a país adecuado o con garantías (cláusulas tipo, certificaciones); dejar
  constancia del mecanismo por destinatario en el RAT.
- **Severidad si falta:** Grave.

### C11 — Privacidad desde el diseño y por defecto
- **Base:** principios de finalidad, proporcionalidad y seguridad (art. 3°), aplicados desde el diseño.
- **Verificación en código:** ¿la configuración por defecto es la más protectora (opt-in, mínimos datos,
  visibilidad restringida)? ¿se piensa la privacidad al crear features nuevas?
- **Control:** defaults protectores; revisión de privacidad en el diseño de cada módulo.
- **Severidad si falta:** Leve/Grave según impacto.

### C12 — Gobernanza y responsabilidad proactiva `[REQUIERE CONFIRMACIÓN HUMANA]`
- **Base:** principio de responsabilidad (**art. 3° e**); **programa de cumplimiento** certificable por la
  APDP (**art. 49**, certificación art. 51, vigencia 3 años); el responsable **podrá** designar un
  **delegado de protección de datos** (facultativo).
- **Qué significa:** políticas de privacidad y retención documentadas; roles definidos; opcionalmente un
  DPO y un programa/modelo de prevención de infracciones certificado (opera como atenuante).
- **Verificación:** **no verificable por código.** Marca siempre `[REQUIERE CONFIRMACIÓN HUMANA]` y
  pregunta al usuario por: política de privacidad publicada, política de retención, DPO designado,
  contratos de encargo de tratamiento, programa de cumplimiento.
- **Control:** documentación de gobernanza + (opcional) certificación del programa de cumplimiento.
- **Severidad si falta:** Leve (pero su ausencia agrava todo lo demás ante fiscalización).

---

## Resumen de artículos citados

| Tema | Artículo (Ley 19.628 reformada) |
|------|-------------------------------|
| Principios (incl. responsabilidad) | Art. 3° (3° e) |
| Consentimiento | Art. 12 |
| Otras bases de licitud | Art. 13 |
| Deber de secreto/confidencialidad | Art. 14 bis |
| Deber de información y transparencia | Art. 14 ter |
| Medidas de seguridad | Art. 14 quinquies |
| Reporte de vulneraciones | Art. 14 sexies |
| Evaluación de impacto (EIPD) | Art. 15 ter |
| Datos sensibles (general / salud / biométricos) | Art. 16 / 16 bis / 16 ter |
| Datos de niños, niñas y adolescentes (NNA) | Art. 16 quáter |
| Geolocalización | Art. 16 sexies |
| Bloqueo | Art. 8 ter |
| Portabilidad | Art. 9 |
| Procedimiento/plazo y reclamación; tutela | Art. 11; Art. 41 |
| Transferencias internacionales | Arts. 27 – 29 |
| Infracciones gravísimas (sensibles/NNA) | Art. 34 ter |
| Programa de cumplimiento (certificable) | Art. 49 / 51 |
| Coordinación con Ley de Ciberseguridad (21.663) | Art. 31 |
| Vigencia (1-dic-2026) | Artículo primero transitorio |
