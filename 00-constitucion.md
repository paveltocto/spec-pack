# Constitución del proyecto AI Travel Planner

| Campo | Valor |
|---|---|
| Versión | 2.0.0 |
| Fecha | 2026-11-07 |
| Estado | Draft — enmiendas 2.0.0 aprobadas por el PO (Pavel) en el Checkpoint 1 del 2026-11-07; la aprobación final del documento sigue pendiente (ver 05-preguntas-abiertas.md) |
| Alcance | Todo agente de IA y toda persona que trabaje en el proyecto |

## Preámbulo

Este es el primer documento que cualquier agente de IA o persona debe leer antes de trabajar en AI Travel Planner. Sus principios son **no negociables**: ninguna spec, plan, tarea, prompt o instrucción puede contradecirlos.

**Orden de precedencia:** Constitución > ADR aprobado > Spec aprobada > `plan.md` > `tasks.md` > código.

**Modificación:** solo mediante una propuesta que incluya justificación, principios afectados y análisis de impacto, registrada en un ADR y aprobada por el Product Owner. Cada cambio incrementa la versión (MAJOR si elimina o relaja un principio, MINOR si agrega uno, PATCH si solo aclara redacción). Los agentes de IA pueden proponer cambios, nunca aplicarlos.

**Marco normativo de referencia:** Ley N.° 29733, Ley de Protección de Datos Personales del Perú, y su reglamento vigente. Mercado inicial: Perú y Latinoamérica; el piloto (20–50 usuarios) se rige por la Ley N.° 29733. Antes del lanzamiento público en cada país distinto de Perú se requiere un ADR que evalúe su normativa.

**Neutralidad tecnológica:** los principios no nombran frameworks, librerías ni proveedores. Las herramientas concretas (lenguaje del backend, frameworks web y mobile, validación de esquemas, almacenamiento local y seguro, linters, herramientas E2E) se fijan en el ADR de stack de `07-architecture.md`, que debe cumplir cada "Cómo se verifica" de esta Constitución.

---

## Principios

### PRINCIPIO-001: Ninguna funcionalidad sin spec aprobada

Ningún código de producto se escribe, genera ni fusiona sin una spec en `specs/NNN-nombre/spec.md` cuyo frontmatter tenga `status: approved`, `approver`, `approved_at` y `version`, con criterios de aceptación en formato Given / When / Then. Todo PR declara `Spec: NNN` en su descripción y la CI rechaza el PR si la spec no existe o no está aprobada; solo los PRs con la etiqueta `no-spec` (documentación, mantenimiento o corrección sin cambio de comportamiento) quedan exceptuados, y esa etiqueta solo la puede poner una persona.

**Cómo se verifica:** check de CI que lee el ID del PR, abre el `spec.md` correspondiente y valida `status: approved`.

**Ejemplo de cumplimiento:** un agente recibe la tarea "agregar filtro por presupuesto", verifica que `specs/003-travel-budget/spec.md` tiene `status: approved`, implementa y abre un PR con `Spec: 003`.

**Ejemplo de violación:** un agente implementa "exportar itinerario a PDF" porque "parecía útil", sin spec, o con una spec en `status: draft`.

---

### PRINCIPIO-002: La IA no inventa datos factuales

El LLM no puede producir como hecho ningún precio, horario, disponibilidad, restricción de actividad (edad, altura, salud, accesibilidad), requisito migratorio o sanitario, pronóstico del clima ni tiempo de traslado; esos datos solo pueden provenir de una fuente registrada (sitio oficial, organismo público o API contratada) o mostrarse como Estimada o Por confirmar. Solo la capa de verificación del backend puede asignar el estado Verificada, nunca el LLM, y toda consulta recibe respuesta: si un dato no se puede verificar, se dice y se recomienda confirmarlo.

**Cómo se verifica:** el backend valida la respuesta estructurada del LLM contra un esquema en el que cada dato factual exige `reliability_status`; un dato con `verified` sin `source` registrada se rechaza. Suite de evaluación (golden set) que falla si aparece un dato factual sin estado.

**Ejemplo de cumplimiento:** el usuario pregunta el horario del Museo Larco; no hay fuente consultada, y la app responde "! Por confirmar — No pudimos verificar este dato. Confírmalo directamente con el establecimiento antes de tu viaje."

**Ejemplo de violación:** el itinerario dice "Entrada a Machu Picchu: S/ 152, abre a las 6:00" sin fuente, o el LLM etiqueta por sí mismo un dato como "Verificada".

---

### PRINCIPIO-003: Formato obligatorio de los estados de confiabilidad

Todo dato de las categorías del PRINCIPIO-002 muestra uno de estos tres estados, con **ícono + texto + color** (nunca solo color) y con estos textos exactos en es-419, almacenados como claves de i18n:

| Estado | Etiqueta | Texto obligatorio que acompaña al dato |
|---|---|---|
| Verificada | `✓ Verificada` | `Fuente: {fuente} · Consultado el {dd/mm/aaaa} a las {HH:mm}` |
| Estimada | `≈ Estimada` | `Valor aproximado basado en {base_de_cálculo}. Te recomendamos confirmarlo antes de tu viaje.` |
| Por confirmar | `! Por confirmar` | `No pudimos verificar este dato. Confírmalo con {fuente_sugerida} antes de tu viaje.` |

Un dato Verificado pasa automáticamente a Por confirmar al superar su vigencia: precios, horarios y restricciones de actividades, 7 días; requisitos migratorios y sanitarios, 24 horas (no aplica en v1, que no muestra requisitos migratorios: ver PRINCIPIO-008); disponibilidad, clima y tiempos de traslado, la vigencia que declare la fuente (sin fecha declarada, no se consideran vigentes).

**Cómo se verifica:** tests unitarios con 100 % de cobertura sobre la función que asigna estado y vigencia; test de snapshot de los tres componentes de etiqueta en web y mobile; revisión de que las traducciones conservan el sentido de la recomendación.

**Ejemplo de cumplimiento:** "Almuerzo en Barranco: S/ 40–60 por persona · ≈ Estimada — Valor aproximado basado en precios promedio de la zona. Te recomendamos confirmarlo antes de tu viaje."

**Ejemplo de violación:** mostrar "S/ 40–60" con solo un punto amarillo, o mostrar como "✓ Verificada" un requisito de visa consultado hace 3 días.

---

### PRINCIPIO-004: Minimización de datos de menores

De un menor solo se registra la **cantidad** y su **rango de edad**: 0–2, 3–5, 6–11 o 12–17 años. Nunca se solicita, almacena, registra en logs ni envía al LLM su nombre, fecha de nacimiento, fotografía, documento de identidad, diagnóstico médico ni texto libre que lo describa; las necesidades prácticas (cochecito, movilidad reducida, restricciones alimentarias, menor exigencia física) son opcionales, se eligen de un catálogo cerrado y se asocian al **grupo de viaje**, no a un menor concreto.

**Cómo se verifica:** el esquema de validación de entrada del backend no tiene campos para esos datos y rechaza todo campo no declarado; test con 100 % de cobertura sobre el filtro que construye el contexto del LLM; test que inspecciona el payload enviado al proveedor y falla si contiene campos fuera de la lista permitida.

**Ejemplo de cumplimiento:** el formulario pide "Niños: 2 — Niño 1: 3–5 años, Niño 2: 6–11 años" y "Necesidades del grupo: ☑ cochecito".

**Ejemplo de violación:** un campo "Nombre del niño (opcional)", un selector de fecha de nacimiento, o un campo libre "Cuéntanos sobre tu hijo" cuyo texto se envía al LLM.

---

### PRINCIPIO-005: Privacidad de invitados y usuarios registrados

El backend no persiste ningún dato de viaje de un usuario invitado (lo procesa en memoria y lo descarta al responder); sus itinerarios viven solo en el almacenamiento local del dispositivo (del navegador en web, de la app en mobile). Los logs de todos los usuarios nunca contienen el payload del viaje, datos personales ni datos de menores; los datos de usuarios nunca se usan para entrenar ni ajustar modelos; la migración de viajes locales a una cuenta requiere consentimiento explícito por viaje; y una solicitud de eliminación de cuenta se ejecuta, incluidos los respaldos, en un máximo de 30 días, salvo obligación legal de conservación documentada.

**Cómo se verifica:** test de integración que genera un itinerario como invitado y comprueba que no se creó ninguna fila en la base de datos; test que analiza los logs emitidos y falla si encuentra campos del payload; revisión del contrato con el proveedor de LLM (opción de no retención y no entrenamiento) en el ADR de selección; job de borrado con test que verifica el plazo.

**Ejemplo de cumplimiento:** un invitado genera un viaje a Cusco; el backend registra solo `request_id`, duración y código de estado.

**Ejemplo de violación:** guardar los itinerarios de invitados "para analítica", registrar en logs `{"destination": "Cusco", "children": [...]}`, o elegir un proveedor de LLM que usa los prompts para entrenar.

---

### PRINCIPIO-006: Cifrado y manejo de credenciales

Toda comunicación usa TLS 1.2 como mínimo (TLS 1.3 preferido) y se rechaza HTTP plano; la base de datos y sus respaldos se cifran en reposo con AES-256 (o el cifrado equivalente administrado por el proveedor); las contraseñas se almacenan solo con Argon2id (mínimo: 19 MiB de memoria, 2 iteraciones, paralelismo 1); el login social usa OAuth 2.0 / OpenID Connect. Los tokens se guardan en el almacén seguro del sistema operativo en mobile y en cookies `HttpOnly`, `Secure` y `SameSite` en web; nunca se guardan tokens, contraseñas, credenciales ni datos identificativos (nombre, correo, teléfono, documento) en el almacenamiento local no cifrado del navegador o de la app, y ningún secreto se versiona en el repositorio. El itinerario del invitado, incluida la cantidad y el rango de edad de los menores sin ningún identificador (PRINCIPIO-004), sí puede guardarse en el almacenamiento local del dispositivo del propio usuario (PRINCIPIO-005); esta precisión requiere validación legal antes del lanzamiento público.

**Cómo se verifica:** escaneo de configuración TLS en cada entorno; test unitario que verifica los parámetros de Argon2id; regla de lint que prohíbe escribir claves de sesión o credenciales en el almacenamiento local; escáner de secretos en CI (por ejemplo gitleaks) que bloquea el merge.

**Ejemplo de cumplimiento:** el token de sesión mobile se guarda en el almacén seguro del sistema operativo, la contraseña se verifica contra un hash `$argon2id$...` y el itinerario del invitado con "Niño 1: 3–5 años" se guarda en el almacenamiento local.

**Ejemplo de violación:** guardar el JWT o el correo del usuario en el almacenamiento local del navegador, usar bcrypt o SHA-256 para contraseñas, o subir un `.env` con la API key del LLM.

---

### PRINCIPIO-007: Lo que NO puede hacer un agente de IA de desarrollo

Un agente de IA de desarrollo puede crear ramas, implementar tareas de specs aprobadas, ejecutar pruebas y abrir PRs; **no puede**: hacer merge a `main`, desplegar a ningún entorno compartido, modificar esta Constitución, specs o ADRs aprobados, borrar, saltar (`skip`, `xfail`) o debilitar tests existentes para que pasen, agregar o actualizar dependencias sin aprobación humana, ni acceder a datos de producción, secretos, variables de entorno protegidas o configuración de infraestructura. Cuando una spec es ambigua o contradice esta Constitución, el agente se detiene y lo reporta en lugar de decidir.

**Cómo se verifica:** protección de rama en `main` (merge solo con aprobación humana); CODEOWNERS humano sobre `00-constitucion.md`, `specs/**/spec.md`, `docs/adr/` y lockfiles; check de CI que marca para revisión obligatoria todo PR que elimine tests o agregue marcadores `skip`; credenciales de agentes sin permisos sobre producción.

**Ejemplo de cumplimiento:** un test falla tras un cambio; el agente corrige el código y, si cree que el test está mal, lo reporta en el PR sin tocarlo.

**Ejemplo de violación:** el agente marca como `skip` un test que falla, cambia `status: draft` a `approved` en una spec, o instala una librería nueva por su cuenta.

---

### PRINCIPIO-008: Límites del agente de IA del producto

El asistente que genera itinerarios no puede reservar, comprar, cobrar ni redirigir a pagos, ni incluir enlaces de afiliados (MVP); no da asesoramiento médico ni legal (ofrece información general y remite a la fuente oficial), no redacta requisitos migratorios (en v1 solo muestra, en todo itinerario, el aviso migratorio con enlaces a fuentes oficiales), no garantiza que una persona cumple requisitos de ingreso a un país, no describe un destino como "seguro" sin fuente oficial que lo respalde, y nunca da instrucciones para evadir controles migratorios, aduaneros o legales. Ante una solicitud fuera de alcance, responde explicando el límite y ofreciendo una alternativa legal, sin dejar la consulta sin respuesta.

**Cómo se verifica:** casos del golden set que cubren reservas, consultas médicas, visas y evasión de controles, con aserciones sobre la respuesta; revisión de que la respuesta estructurada no admite campos de pago o URLs de afiliados.

**Ejemplo de cumplimiento:** "¿Necesito visa para ir a EE.UU.?" → la app no redacta requisitos y muestra el aviso migratorio: "Los requisitos de ingreso pueden cambiar. Consúltalos en la fuente oficial antes de tu viaje", con el enlace oficial de la lista curada.

**Ejemplo de violación:** "No te preocupes, con tu pasaporte peruano entras sin problema", o explicar cómo cruzar una frontera sin pasar por el control migratorio.

---

### PRINCIPIO-009: El backend es la única fuente de verdad

Toda regla de negocio y toda validación se ejecutan en el backend; web y mobile validan solo para mejorar la experiencia y nunca confían en un dato porque el cliente lo validó. Web y mobile comparten un único paquete `core` en el monorepo con tipos, contratos de API, clientes HTTP, constantes y validaciones de UX, sin reglas de negocio duplicadas; el acceso al LLM pasa siempre por una capa de abstracción propia, y cada generación registra proveedor y versión del modelo.

**Cómo se verifica:** los tipos de `core` se generan del contrato OpenAPI del backend y la CI falla si hay diferencias; regla de lint de imports que prohíbe importar el SDK de un proveedor de LLM fuera del módulo adaptador; tests de API que envían datos inválidos saltándose el cliente.

**Ejemplo de cumplimiento:** el límite de viajeros por itinerario se valida en el backend; web y mobile importan el mismo tipo `TripRequest` desde `core`.

**Ejemplo de violación:** calcular el presupuesto con fórmulas distintas en web y en mobile, o llamar al SDK del proveedor de LLM directamente desde un endpoint.

---

### PRINCIPIO-010: Accesibilidad WCAG 2.2 AA en web y mobile

Web cumple WCAG 2.2 nivel AA y mobile sus criterios equivalentes: contraste mínimo 4.5:1 para texto normal y 3:1 para texto grande, objetivos táctiles de al menos 44 × 44 pt, etiqueta accesible en todo elemento interactivo, compatibilidad con VoiceOver y TalkBack, texto ampliable hasta 200 % sin pérdida de contenido y navegación completa por teclado en web. Ninguna información (incluidos los estados de confiabilidad) se comunica solo con color.

**Cómo se verifica:** axe-core en los E2E de web con cero violaciones de severidad *serious* o *critical*; lint de accesibilidad en mobile; checklist manual con VoiceOver y TalkBack de los flujos principales antes de cada release.

**Ejemplo de cumplimiento:** el botón "Generar itinerario" tiene etiqueta accesible, mide 48 × 48 pt y su texto tiene contraste 7:1.

**Ejemplo de violación:** un ícono de basurero sin etiqueta para lectores de pantalla, o texto gris claro sobre blanco con contraste 2.8:1.

---

### PRINCIPIO-011: Idiomas e internacionalización

Código, endpoints, base de datos, commits, ramas y PRs se escriben en inglés; specs, ADRs y esta Constitución, en español. La v1 se lanza solo en español latinoamericano (es-419), sin selector de idioma; un segundo idioma (pt-BR, en evaluación para después de v1) requiere un ADR. Aun así, ningún texto visible al usuario se escribe directamente en el código (todo pasa por archivos de traducción), fechas, números y monedas se formatean según la configuración regional, y cuando exista un segundo idioma el idioma del itinerario será elegible independientemente del idioma de la interfaz.

**Cómo se verifica:** regla de lint que prohíbe literales de texto en componentes de UI; test que verifica que toda clave visible existe en el archivo es-419; revisión de PR sobre nombres en inglés. El test que cambia el idioma del itinerario sin cambiar el de la interfaz se agrega cuando exista un segundo idioma.

**Ejemplo de cumplimiento:** `{t('trip.generate')}` en lugar del literal y `Intl.NumberFormat('es-PE', { style: 'currency', currency: 'PEN' })`.

**Ejemplo de violación:** el literal "Generar itinerario" escrito en un componente, una función `calcularPresupuesto()`, o mostrar "S/.152.00" construido concatenando cadenas.

---

### PRINCIPIO-012: Puertas de calidad y consistencia entre plataformas

Ningún PR se fusiona si no pasa: cobertura ≥ 80 % en backend y `core`; 100 % en la lógica de estados de confiabilidad (PRINCIPIO-003) y en el filtrado de datos de menores (PRINCIPIO-004); verificación de tipos en modo estricto y linter sin errores en backend, `core`, web y mobile (herramientas fijadas en el ADR de stack); y, si el PR toca prompts, el modelo o la capa de verificación, el 100 % del golden set de no invención. Para un mismo input, web y mobile producen el mismo resultado funcional (mismas reglas, contratos, estados de confiabilidad y textos), aunque la interfaz se adapte a cada plataforma.

**Cómo se verifica:** la CI publica cobertura y resultados del golden set y bloquea el merge si no se alcanzan los umbrales; cada flujo crítico tiene el mismo escenario E2E en web y en mobile (herramientas fijadas en el ADR de stack) con las mismas aserciones funcionales.

**Ejemplo de cumplimiento:** un PR que ajusta el prompt de generación pasa los 100 % de casos del golden set y la cobertura de `reliability/` se mantiene en 100 %.

**Ejemplo de violación:** fusionar un cambio de prompt sin ejecutar el golden set, o que la app mobile muestre "≈ Estimada" donde la web muestra "! Por confirmar" para el mismo dato.

---

## Decisiones pendientes que bloquean un lanzamiento público

Estas decisiones no se resuelven en la Constitución, pero ninguna versión pública o comercial se lanza sin cerrarlas en un ADR:

1. **Responsable del tratamiento de datos** e inscripción del banco de datos ante la Autoridad Nacional de Protección de Datos Personales.
2. **Retención de cuentas inactivas** (plazo y procedimiento).
3. **Selección del proveedor de LLM**, incluyendo retención, no entrenamiento y país de procesamiento.
4. **Transferencias internacionales de datos** (nube y LLM) y sus garantías.
5. **Analítica**: herramienta, eventos permitidos y mecanismo de consentimiento.

---

## Historial de enmiendas

| Versión | Fecha | Tipo | Cambio | Principios | Aprobó |
|---|---|---|---|---|---|
| 1.0.0 | 2026-11-06 | — | Versión inicial | Todos | Pendiente |
| 2.0.0 | 2026-11-07 | MAJOR | AL-26: v1 solo es-419, sin selector de idioma; la elección del idioma del itinerario se condiciona a un segundo idioma (relaja PRINCIPIO-011). | PRINCIPIO-011 | Pavel (PO), Checkpoint 1 |
| 2.0.0 | 2026-11-07 | PATCH | AL-22: neutralidad tecnológica; se quitan nombres de frameworks y librerías (FastAPI, Pydantic, React + Vite, React Native + Expo, SecureStore, pytest, mypy, Ruff, Playwright, Maestro). Ningún principio se relaja. | 004, 005, 006, 007, 009, 010, 012 | Pavel (PO), Checkpoint 1 |
| 2.0.0 | 2026-11-07 | PATCH | AL-23: mercado inicial Perú y Latinoamérica; ADR por país antes del lanzamiento público. | Preámbulo | Pavel (PO), Checkpoint 1 |
| 2.0.0 | 2026-11-07 | PATCH | AL-01 / AL-02: la IA no redacta requisitos migratorios; aviso con enlaces oficiales en todo itinerario (AL-24). | PRINCIPIO-003, PRINCIPIO-008 | Pavel (PO), Checkpoint 1 |
| 2.0.0 | 2026-11-07 | PATCH | AL-29: PRINCIPIO-006 prohíbe en el almacenamiento local credenciales, tokens y datos identificativos; el itinerario del invitado con rangos de edad sin identificadores sí se permite. Validación legal recomendada antes del lanzamiento público. | PRINCIPIO-006 | Pavel (PO), Checkpoint 1 |

El ADR que registra esta enmienda es ADR-001 en `07-architecture.md`. Detalle de los hallazgos en la auditoría de alineación registrada en las especificaciones.
