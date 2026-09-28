# Detección de Datos Personales y Sensibles

Heurísticas para el modo `/ldpddp scan`. El objetivo es encontrar **dónde** el proyecto trata datos
personales y sensibles, con ubicación `archivo:línea`, para alimentar el inventario y el RAT.

> La detección por nombre de campo tiene falsos positivos y negativos. Es un punto de partida, no un
> veredicto. Todo hallazgo dudoso se marca para revisión humana. Prioriza **recall** (mejor sobre-marcar
> y descartar) sobre precisión.

## Dónde buscar (en orden)

1. **Esquema de base de datos:** `CREATE TABLE`, migraciones (`*.sql`, `migrations/`, `prisma/schema.prisma`,
   `*_migration.php`, Sequelize/TypeORM/Eloquent models), diccionarios de datos.
2. **Modelos / entidades ORM:** clases con propiedades mapeadas a columnas.
3. **Formularios:** `<input name=...>`, `<label>`, componentes de formulario (React/Vue), validadores.
4. **DTOs / contratos de API:** payloads de request/response, schemas (OpenAPI, Zod, Joi, class-validator).
5. **Config y entorno:** `.env`, `config/*`, claves de terceros que impliquen envío de datos.
6. **Logs:** ¿se registran datos personales en texto plano? (fuga frecuente).
7. **Seeds / fixtures / dumps:** datos reales de prueba (riesgo alto si son de producción).

## Diccionario — datos personales (identificadores y de contacto)

Nombres de campo/columna a marcar (ES/EN, con variantes y camel/snake case):

- **Identidad:** `nombre`, `apellido`, `first_name`, `last_name`, `full_name`, `razon_social`.
- **Identificadores nacionales (Chile):** `rut`, `run`, `dni`, `cedula`, `pasaporte`, `passport`,
  `tax_id`, `numero_documento`.
- **Contacto:** `email`, `correo`, `telefono`, `celular`, `phone`, `mobile`, `direccion`, `domicilio`,
  `address`, `comuna`, `ciudad`, `codigo_postal`, `zip`.
- **Fechas/atributos:** `fecha_nacimiento`, `birthdate`, `dob`, `edad`, `age`, `genero`, `sexo`, `gender`,
  `nacionalidad`, `estado_civil`.
- **Digitales/rastreo:** `ip`, `ip_address`, `user_agent`, `device_id`, `cookie`, `session`, `lat`,
  `lng`, `latitude`, `longitude`, `geolocation`, `location`.
- **Cuentas:** `username`, `usuario`, `password` (→ verificar hashing, C6), `token`, `foto`, `avatar`,
  `photo` (imagen de rostro puede ser biométrico → C3).

## Diccionario — datos sensibles (art. 16 y ss.) — prioridad ALTA

Marcar como **sensible** (severidad Gravísima si se tratan sin base):

- **Salud / perfil biológico (art. 16 bis):** `salud`, `health`, `diagnostico`, `enfermedad`,
  `discapacidad`, `medicamento`, `alergia`, `tipo_sangre`, `historia_clinica`, `licencia_medica`,
  `embarazo`, `prevision` (Isapre/Fonasa).
- **Biométricos (art. 16 ter):** `huella`, `fingerprint`, `biometric`, `facial`, `rostro`, `iris`, `retina`,
  `voz`, `firma_biometrica`, y campos de reconocimiento facial.
- **Genéticos:** `genetic`, `adn`, `dna`, `genoma`.
- **Ideología/creencias:** `religion`, `credo`, `political`, `partido`, `sindicato`, `afiliacion`,
  `conviccion`, `ideologia`.
- **Vida/orientación sexual:** `orientacion_sexual`, `sexual`, `genero_identidad` (con cuidado).
- **Situación socioeconómica** (distintivo de Chile — SÍ es sensible): `ingresos`, `salario`, `sueldo`,
  `renta`, `nivel_socioeconomico`, `gse`, `deuda`, `dicom`, `score_crediticio`, `beneficio_social`,
  `subsidio`, `puntaje_rsh` (Registro Social de Hogares).
- **Origen étnico/racial:** `etnia`, `raza`, `pueblo_originario`, `nacionalidad` (con contexto).

## Diccionario — datos de NNA (art. 16 quáter) — protección reforzada

`menor`, `nino`, `niña`, `adolescente`, `minor`, `child`, `tutor`, `apoderado`, `curso`, `colegio`,
`establecimiento`, o cualquier tabla cuya población sean menores de 18. Si el titular es NNA, el
tratamiento requiere fundamento reforzado → severidad **Gravísima** si falta.

## Identificador maestro chileno: RUT / RUN

El **RUT** (Rol Único Tributario) / **RUN** es el identificador universal de personas en Chile — máxima
prioridad. Detectarlo por nombre de campo (`rut`, `run`, `rut_cliente`, `dni`) **y** por patrón de valor
en seeds/fixtures/logs/validadores:

```
# Formato con o sin puntos y con dígito verificador (0-9 o K):
\b\d{1,2}\.?\d{3}\.?\d{3}-[\dkK]\b

# Ejemplos válidos: 12.345.678-5   |   9876543-K   |   5.123.456-7
```

El dígito verificador se calcula con módulo 11; si el proyecto valida RUT, ubica esa función (buena señal
de que trata identidad chilena). El RUT es dato personal (no sensible por sí solo), pero es la llave que
vincula todo el resto — trátalo como pivote del inventario.

## Salida: tabla de inventario

Cada hallazgo alimenta esta tabla (que luego se agrupa en actividades de tratamiento para el RAT):

| Campo/columna | Ubicación (`archivo:línea`) | Clasificación | Categoría | Notas |
|---------------|------------------------------|---------------|-----------|-------|
| `rut` | `db/schema.sql:14` | Personal | Identificador (Chile) | Pivote |
| `diagnostico` | `models/Paciente.php:22` | **Sensible** | Salud (art. 16 bis) | Requiere consentimiento explícito |
| `ingresos` | `forms/registro.jsx:88` | **Sensible** | Situación socioeconómica | Distintivo Chile |

**Clasificación:** Personal / Sensible / NNA.
Marca `[REVISAR]` los hallazgos ambiguos por nombre de campo y confírmalos leyendo el contexto.
