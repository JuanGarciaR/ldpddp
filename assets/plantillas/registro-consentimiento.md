# Registro de Consentimiento — Esquema

> Plantilla fill-in / esquema de datos. Consentimiento libre, informado, específico, con acto afirmativo
> y **esencialmente revocable** (Ley 21.719, art. 12). Los medios para otorgar y revocar deben ser
> "expeditos, fidedignos, gratuitos y permanentemente disponibles".

## Reglas
- **Sin casillas premarcadas** (viola el "acto afirmativo").
- Una constancia **por finalidad** y **por versión** del texto informado.
- Revocar debe ser tan fácil como otorgar.
- Se presume **no libre** el consentimiento recabado dentro de un contrato donde esa recolección no es
  necesaria (art. 12, inc. 6°).

## Esquema del registro (tabla `consent_records`)
| Campo | Descripción |
|-------|-------------|
| `id` | Identificador de la constancia |
| `titular_id` | Referencia al titular |
| `finalidad` | Finalidad específica consentida |
| `version_texto` | Versión del texto informado aceptado |
| `canal` | Web / app / presencial / … |
| `otorgado_en` | Timestamp del acto afirmativo |
| `revocado_en` | Timestamp de revocación (nulo si vigente) |
| `evidencia` | Referencia a la evidencia (hash del texto, IP, etc. — sin exceso de datos) |

## Bitácora (si no se guarda en BD)
| Titular | Finalidad | Versión | Canal | Otorgado | Revocado |
|---------|-----------|---------|-------|----------|----------|
| | | | | | |
