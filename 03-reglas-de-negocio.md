# 03 — Reglas de negocio · AI Travel Planner

> **Fase BMAD:** Planificación · **Agente:** Business Analyst
> **Insumos:** 00-constitucion.md (v2.0.0), 01-project-brief.md (v1.2), 02-alcance.md
> **Fecha:** 2026-11-07 · **Revisión:** 2026-11-07, correcciones de la auditoría QA (ver Anexo D)
> **Estado:** Borrador para revisión del Product Owner (aprobación pendiente en PREG-54; PAR y reglas "BA (propuesta)" en PREG-55) · Preguntas abiertas en 05-preguntas-abiertas.md
> **Revisión 2026-11-07 (Checkpoint 1):** referencias al Alcance R-NN/D-NN → RES-NN/DEP-NN (AL-27); RN-024 reescrita: aviso migratorio genérico en todo itinerario, incluido Perú, sin pedir residencia ni nacionalidad (AL-24).
> **Revisión 2026-11-07: hallazgos auditoría Clase 2 vuelta 1:** QB-03 (semántica única de rechazo y degradación de un `verified` en RN-064 y RN-106) y QB-06 (RN-069 pasa a WON'T (v1)). Detalle en el Anexo D.

---

## 0. Convenciones

### 0.1 Palabras normativas

| Palabra | Significado |
|---|---|
| **DEBE** | Obligatorio. Su incumplimiento es un defecto. |
| **NO DEBE** | Prohibido. Su aparición es un defecto. |
| **PUEDE** | Permitido u opcional. Su ausencia no es un defecto. |

### 0.2 Patrones EARS usados

| Patrón | Plantilla en español |
|---|---|
| Ubiquitous | El sistema DEBE / NO DEBE … |
| Event-driven | CUANDO \<evento\>, el sistema DEBE … |
| State-driven | MIENTRAS \<estado\>, el sistema DEBE … |
| Unwanted | SI \<condición no deseada\>, ENTONCES el sistema DEBE … |
| Optional | DONDE \<funcionalidad incluida\>, el sistema DEBE / PUEDE … |

"El sistema" designa al producto completo (backend + web + mobile). Cuando la regla aplica solo al backend se escribe "el backend", porque según el PRINCIPIO-009 el backend es la única fuente de verdad: los clientes validan solo para mejorar la experiencia.

**Atomicidad:** una regla puede nombrar varias acciones cuando juntas forman una sola respuesta del sistema (por ejemplo, rechazar la solicitud e indicar el motivo). Nunca une dos comportamientos independientes.

### 0.3 Valores de la columna "Fuente"

| Valor | Significado |
|---|---|
| Constitución PRINCIPIO-NNN | Deriva de un principio de 00-constitucion.md. No se puede relajar sin un ADR. |
| Alcance F-NN / RES-NN / DEP-NN | Deriva de 02-alcance.md (RES = restricción, DEP = dependencia). Los IDs D-NN sin "Alcance" designan decisiones vigentes del PO (registradas en ADRs de 07-architecture.md). |
| Brief §N | Deriva de 01-project-brief.md. |
| Ley 29733 | Deriva de la Ley de Protección de Datos Personales del Perú. |
| BA (propuesta, a confirmar por PO) | Valor o regla propuesta por el BA para cubrir un vacío. Requiere aprobación del Product Owner. Los parámetros están en el Anexo A. |

### 0.4 Otras convenciones

- **Fechas:** en datos y reglas se usa AAAA-MM-DD. En pantalla, los estados de confiabilidad usan dd/mm/aaaa y HH:mm porque así lo fija el PRINCIPIO-003.
- **Fecha actual:** se toma en la zona horaria America/Lima (mercado inicial: Perú y Latinoamérica, D-01; la zona horaria de referencia sigue abierta en PREG-23 y PREG-32).
- **Montos:** siempre con moneda (código ISO 4217 en datos; símbolo regional en pantalla).
- **Valores de los ejemplos:** son ilustrativos. Ningún precio, horario ni restricción de un ejemplo es un dato verificado.
- **Textos de estado en los ejemplos:** todo ejemplo que muestra un dato factual incluye el texto obligatorio completo del PRINCIPIO-003. "…" solo abrevia la base de cálculo.
- **Ritmos oficiales:** Relajado, Equilibrado y Activo (decisión del 2026-11-07, aplicada en 02-alcance F-02).
- **Rangos de edad de menores:** 0–2, 3–5, 6–11 y 12–17 años (PRINCIPIO-004). Coinciden con el brief §2.4 (v1.1) y con 02-alcance F-02.
- **Precedencia:** si una regla de este documento contradice la Constitución, prevalece la Constitución y la regla se considera defectuosa.

---

## 1. Índice

| Categoría | Reglas | Funcionalidades |
|---|---|---|
| Solicitud de viaje | RN-001 a RN-007 | F-02 |
| Perfil de viajeros | RN-008 a RN-016 | F-02 |
| Generación de itinerario | RN-017 a RN-029, RN-105, RN-111 | F-03, F-04 |
| Adaptación familiar | RN-030 a RN-037 | F-03 |
| Personalización | RN-038 a RN-045 | F-05, F-09 |
| Presupuesto | RN-046 a RN-054 | F-07 |
| Confiabilidad de datos | RN-055 a RN-071, RN-102 a RN-104, RN-106, RN-107 | F-03, F-06 |
| Acceso e invitados | RN-072 a RN-082 | F-01, F-10, F-11 |
| Fallas del servicio de IA | RN-083 a RN-089, RN-108 a RN-110 | F-03, F-05 |
| Pérdida de conexión | RN-090 a RN-094 | F-01, F-03, F-05, F-14 |
| Datos y privacidad | RN-095 a RN-099 | F-01, F-02, F-03 |
| UX | RN-100 a RN-101 | F-03, F-05, F-06 |

---

## 2. Catálogo de reglas

### 2.1 Solicitud de viaje

---

**RN-001** | Categoría: Solicitud de viaje | Prioridad: MUST | Fuente: Alcance F-02 | Funcionalidad: F-02

Patrón EARS: Unwanted

SI la solicitud de viaje no incluye un destino, ENTONCES el sistema DEBE rechazar la solicitud indicando que el campo "Destino" es obligatorio.

✅ **Ejemplo:** Destino vacío, fechas 2026-12-18 a 2026-12-21, tipo Pareja → la app no genera el itinerario y muestra "Indica un destino para continuar" junto al campo Destino.

❌ **Contraejemplo:** Destino vacío → la IA genera "4 días en Lima" como destino por defecto.

---

**RN-002** | Categoría: Solicitud de viaje | Prioridad: MUST | Fuente: BA (propuesta, a confirmar por PO) | Funcionalidad: F-02

Patrón EARS: Unwanted

SI la fecha de inicio o la fecha de fin no corresponde a una fecha existente del calendario gregoriano, ENTONCES el sistema DEBE rechazar la solicitud.

✅ **Ejemplo:** Fecha de fin 2027-02-30 → la app rechaza la solicitud con el mensaje "La fecha 2027-02-30 no existe".

❌ **Contraejemplo:** Fecha de fin 2027-02-30 → el sistema la interpreta como 2027-03-02 y genera 3 días adicionales.

---

**RN-003** | Categoría: Solicitud de viaje | Prioridad: MUST | Fuente: BA (propuesta, a confirmar por PO) | Funcionalidad: F-02

Patrón EARS: Unwanted

SI la fecha de fin es anterior a la fecha de inicio, ENTONCES el sistema DEBE rechazar la solicitud.

✅ **Ejemplo:** Inicio 2026-12-21, fin 2026-12-18 → rechazo con "La fecha de fin debe ser igual o posterior a la fecha de inicio".

❌ **Contraejemplo:** Inicio 2026-12-21, fin 2026-12-18 → el sistema invierte las fechas sin avisar y genera del 2026-12-18 al 2026-12-21.

---

**RN-004** | Categoría: Solicitud de viaje | Prioridad: MUST | Fuente: BA (propuesta, a confirmar por PO) | Funcionalidad: F-02

Patrón EARS: Unwanted

SI la fecha de inicio es anterior a la fecha actual en America/Lima, ENTONCES el sistema DEBE rechazar la solicitud.

✅ **Ejemplo:** Fecha actual 2026-11-07; inicio 2026-11-05 → rechazo. Inicio 2026-11-07 → aceptada.

❌ **Contraejemplo:** Fecha actual 2026-11-07; inicio 2026-11-05 → se genera un itinerario con dos días ya transcurridos.

---

**RN-005** | Categoría: Solicitud de viaje | Prioridad: MUST | Fuente: BA (propuesta, a confirmar por PO) — PAR-01 | Funcionalidad: F-02

Patrón EARS: Unwanted

SI la duración del viaje (fecha de fin − fecha de inicio + 1) supera 14 días, ENTONCES el sistema DEBE rechazar la solicitud indicando el máximo de 14 días.

✅ **Ejemplo:** 2027-01-10 a 2027-01-25 (16 días) → rechazo con "El máximo es 14 días por itinerario".

❌ **Contraejemplo:** 2027-01-10 a 2027-01-25 → el sistema genera 16 días y consume el doble de tokens del proveedor de IA.

---

**RN-006** | Categoría: Solicitud de viaje | Prioridad: MUST | Fuente: BA (propuesta, a confirmar por PO) — PAR-02 | Funcionalidad: F-02

Patrón EARS: Unwanted

SI la fecha de inicio es posterior a la fecha actual más 365 días, ENTONCES el sistema DEBE rechazar la solicitud.

✅ **Ejemplo:** Fecha actual 2026-11-07; inicio 2027-11-10 (368 días después) → rechazo con "Puedes planificar viajes que empiecen hasta el 2027-11-07".

❌ **Contraejemplo:** Fecha actual 2026-11-07; inicio 2029-07-28 → el sistema genera el itinerario.

---

**RN-007** | Categoría: Solicitud de viaje | Prioridad: MUST | Fuente: Alcance DEP-02 | Funcionalidad: F-02

Patrón EARS: Unwanted

SI el destino no se resuelve a un único lugar en la fuente de lugares, ENTONCES el sistema DEBE pedir al viajero que elija o precise el destino antes de generar.

✅ **Ejemplo:** Destino "San Juan" → la app muestra opciones como "San Juan de Lurigancho (Lima, Perú)" y "San Juan (Puerto Rico)" y espera la elección. Destino "Qwzxy" sin ningún resultado → "No encontramos ese destino. Revisa cómo está escrito." y no genera.

❌ **Contraejemplo:** Destino "San Juan" → la IA asume Puerto Rico y genera el itinerario sin preguntar.

---

### 2.2 Perfil de viajeros

---

**RN-008** | Categoría: Perfil de viajeros | Prioridad: MUST | Fuente: Alcance F-02 | Funcionalidad: F-02

Patrón EARS: Unwanted

SI la solicitud de viaje no tiene exactamente un tipo de grupo del catálogo (Solo, Pareja, Amigos o Familia), ENTONCES el sistema DEBE rechazar la solicitud.

✅ **Ejemplo:** Tipo de grupo "Pareja".

❌ **Contraejemplo:** Solicitud sin tipo de grupo, o con "Pareja" y "Familia" seleccionados a la vez.

---

**RN-009** | Categoría: Perfil de viajeros | Prioridad: MUST | Fuente: BA (propuesta, a confirmar por PO) | Funcionalidad: F-02

Patrón EARS: Unwanted

SI la solicitud no incluye al menos 1 adulto (18 años o más), ENTONCES el sistema DEBE rechazar la solicitud.

✅ **Ejemplo:** 0 adultos y 2 menores de 12–17 años → rechazo con "El grupo debe incluir al menos un adulto".

❌ **Contraejemplo:** 0 adultos y 2 menores de 12–17 años → el sistema genera el itinerario.

---

**RN-010** | Categoría: Perfil de viajeros | Prioridad: MUST | Fuente: BA (propuesta, a confirmar por PO) — PAR-03 | Funcionalidad: F-02

Patrón EARS: Unwanted

SI el total de viajeros (adultos + menores) supera 10, ENTONCES el sistema DEBE rechazar la solicitud indicando el máximo de 10 viajeros.

✅ **Ejemplo:** 4 adultos + 7 menores = 11 viajeros → rechazo.

❌ **Contraejemplo:** 11 viajeros → el sistema genera el itinerario.

---

**RN-011** | Categoría: Perfil de viajeros | Prioridad: MUST | Fuente: BA (propuesta, a confirmar por PO) | Funcionalidad: F-02

Patrón EARS: Unwanted

SI la composición del grupo no coincide con la definición de su tipo de grupo (ver 04-glosario: Solo, Pareja, Amigos, Familia), ENTONCES el sistema DEBE rechazar la solicitud indicando qué tipo de grupo corresponde.

✅ **Ejemplo:** Tipo "Familia" con 2 adultos y 0 menores → rechazo con "Agrega al menos un menor o cambia el tipo de grupo a Pareja o Amigos". Tipo "Amigos" con 2 adultos y 0 menores → aceptado.

❌ **Contraejemplo:** Tipo "Pareja" con 3 adultos → el sistema genera el itinerario como pareja.

---

**RN-012** | Categoría: Perfil de viajeros | Prioridad: MUST | Fuente: Constitución PRINCIPIO-004 | Funcionalidad: F-02

Patrón EARS: Unwanted

SI un menor del grupo no tiene exactamente un rango de edad del catálogo (0–2, 3–5, 6–11 o 12–17 años), ENTONCES el sistema DEBE rechazar la solicitud.

✅ **Ejemplo:** "Niños: 2 — Niño 1: 3–5 años, Niño 2: 6–11 años".

❌ **Contraejemplo:** Un campo numérico libre donde se escribe "4 años y 7 meses", o un rango "3–6" que no está en el catálogo.

---

**RN-013** | Categoría: Perfil de viajeros | Prioridad: MUST | Fuente: Constitución PRINCIPIO-004; Ley 29733 | Funcionalidad: F-02

Patrón EARS: Ubiquitous

El sistema NO DEBE solicitar datos identificativos de un menor (nombre, fecha de nacimiento, fotografía, documento de identidad o diagnóstico médico).

✅ **Ejemplo:** El formulario de menores solo muestra el selector de rango de edad.

❌ **Contraejemplo:** Un campo "Nombre del niño (opcional)" o un selector de fecha de nacimiento.

---

**RN-014** | Categoría: Perfil de viajeros | Prioridad: MUST | Fuente: Constitución PRINCIPIO-004 | Funcionalidad: F-02

Patrón EARS: Ubiquitous

Las necesidades del grupo DEBEN elegirse de un catálogo cerrado.

✅ **Ejemplo:** "Necesidades del grupo: ☑ Cochecito ☑ Menor exigencia física".

❌ **Contraejemplo:** Un campo de texto libre "Cuéntanos sobre tu hijo" cuyo contenido se envía al LLM.

---

**RN-015** | Categoría: Perfil de viajeros | Prioridad: MUST | Fuente: Constitución PRINCIPIO-004 | Funcionalidad: F-02

Patrón EARS: Ubiquitous

Cada necesidad DEBE asociarse al grupo de viaje, no a un menor concreto.

✅ **Ejemplo:** "Necesidades del grupo: ☑ Cochecito".

❌ **Contraejemplo:** "Niño 2 (3–5 años): cochecito, restricción alimentaria".

---

**RN-016** | Categoría: Perfil de viajeros | Prioridad: MUST | Fuente: Alcance F-02 (nombres actualizados el 2026-11-07) | Funcionalidad: F-02

Patrón EARS: Unwanted

SI la solicitud de viaje no tiene exactamente un ritmo del catálogo (Relajado, Equilibrado o Activo), ENTONCES el sistema DEBE rechazar la solicitud.

✅ **Ejemplo:** Ritmo "Equilibrado".

❌ **Contraejemplo:** Ritmo "Moderado" (valor fuera del catálogo) o solicitud sin ritmo.

---

### 2.3 Generación de itinerario

---

**RN-017** | Categoría: Generación de itinerario | Prioridad: MUST | Fuente: Alcance F-03, F-04 | Funcionalidad: F-03

Patrón EARS: Ubiquitous

El itinerario generado DEBE contener exactamente un día por cada fecha entre la fecha de inicio y la fecha de fin, ambas incluidas.

✅ **Ejemplo:** 2026-12-18 a 2026-12-21 → 4 días: Día 1 (2026-12-18), Día 2 (2026-12-19), Día 3 (2026-12-20), Día 4 (2026-12-21).

❌ **Contraejemplo:** 2026-12-18 a 2026-12-21 → el itinerario trae 3 días porque la IA consideró el último día "de regreso".

---

**RN-018** | Categoría: Generación de itinerario | Prioridad: MUST | Fuente: Alcance F-04; BA (propuesta, a confirmar por PO) — PAR-04 | Funcionalidad: F-04

Patrón EARS: Ubiquitous

Cada actividad DEBE asignarse a exactamente una franja horaria: Mañana (06:00–12:00), Tarde (12:00–18:00) o Noche (18:00–23:00).

✅ **Ejemplo:** "Mañana — Recorrido por el Centro Histórico".

❌ **Contraejemplo:** Una actividad listada al final del día sin franja horaria.

---

**RN-019** | Categoría: Generación de itinerario | Prioridad: MUST | Fuente: Alcance F-04 | Funcionalidad: F-04

Patrón EARS: Ubiquitous

Dentro de un día, las actividades DEBEN mostrarse en el orden Mañana → Tarde → Noche.

✅ **Ejemplo:** Día 2: Mañana "Mercado de San Pedro", Tarde "Qorikancha", Noche "Cena en San Blas".

❌ **Contraejemplo:** Día 2 muestra primero la cena de la Noche y después la visita de la Mañana.

---

**RN-020** | Categoría: Generación de itinerario | Prioridad: MUST | Fuente: Alcance F-04, DEP-05; Constitución PRINCIPIO-002 | Funcionalidad: F-04

Patrón EARS: Ubiquitous

Cada actividad DEBE mostrar su duración aproximada en minutos acompañada de su estado de confiabilidad.

✅ **Ejemplo:** "Museo Larco · 120 min · ≈ Estimada — Valor aproximado basado en la duración habitual de visitas a museos de la zona. Te recomendamos confirmarlo antes de tu viaje."

❌ **Contraejemplo:** "Museo Larco" sin duración, o "Museo Larco · 2 h" sin estado.

---

**RN-021** | Categoría: Generación de itinerario | Prioridad: MUST | Fuente: Brief §3.3, §5.3 | Funcionalidad: F-03

Patrón EARS: Unwanted

SI una fuente registrada indica que un lugar está cerrado permanentemente, ENTONCES el sistema NO DEBE incluir ese lugar en el itinerario.

✅ **Ejemplo:** La fuente de lugares marca el restaurante "X" como cerrado permanentemente → la generación lo descarta y propone otra actividad para esa franja.

❌ **Contraejemplo:** El restaurante "X", marcado como cerrado permanentemente, aparece como cena del Día 1.

---

**RN-022** | Categoría: Generación de itinerario | Prioridad: MUST | Fuente: Constitución PRINCIPIO-002 | Funcionalidad: F-03

Patrón EARS: Unwanted

SI la respuesta del LLM no cumple el esquema estructurado del itinerario, ENTONCES el backend NO DEBE entregarla al cliente.

✅ **Ejemplo:** El LLM devuelve una actividad sin el campo `reliability_status` → el backend descarta la respuesta y aplica RN-085.

❌ **Contraejemplo:** El backend reenvía al cliente el texto libre del LLM porque "se ve bien".

> El reintento ante una respuesta inválida está en RN-085; las respuestas válidas pero incompletas, en RN-109 y RN-110.

---

**RN-023** | Categoría: Generación de itinerario | Prioridad: MUST | Fuente: Constitución PRINCIPIO-008 | Funcionalidad: F-03

Patrón EARS: Ubiquitous

El itinerario NO DEBE incluir enlaces de reserva, de pago ni de afiliados.

✅ **Ejemplo:** "Tarde — Tour al Valle Sagrado · Disponibilidad: ! Por confirmar — No pudimos verificar este dato. Confírmalo con el operador del tour antes de tu viaje.", sin enlace de compra.

❌ **Contraejemplo:** "Reserva aquí: https://ejemplo-tours.com/valle?aff=123".

---

**RN-024** | Categoría: Generación de itinerario | Prioridad: MUST | Fuente: Alcance RES-10, DEP-04; Constitución PRINCIPIO-008 | Funcionalidad: F-03

Patrón EARS: Ubiquitous

La vista de todo itinerario, cualquiera sea su destino (incluido Perú), DEBE mostrar el aviso migratorio con texto genérico y enlaces a fuentes oficiales de la lista curada.

✅ **Ejemplo:** Destino Bogotá → aviso "Los requisitos de ingreso pueden cambiar. Consúltalos en la fuente oficial antes de tu viaje" con enlaces de la lista curada (p. ej., Migración Colombia). Destino Cusco → el mismo aviso con enlaces de la lista curada (p. ej., Migraciones Perú).

❌ **Contraejemplo:** Destino Cusco → el itinerario no muestra el aviso migratorio porque se asume que el viajero reside en Perú. / La app pide el país de residencia o la nacionalidad para decidir si muestra el aviso.

> El aviso no depende del origen, la residencia ni la nacionalidad del viajero, que no se piden (decisión del Checkpoint 1, AL-24; minimización de datos, PRINCIPIO-004 y RN-096). La lista curada se revisa cada 30 días (Alcance DEP-04).
> Pendiente: qué enlaces muestra el aviso si el país de destino no tiene entrada en la lista curada o su enlace está roto, aviso para menores y aviso sanitario (05-preguntas-abiertas PREG-04).

---

**RN-025** | Categoría: Generación de itinerario | Prioridad: MUST | Fuente: Alcance OUT-OF-SCOPE #4 | Funcionalidad: F-03

Patrón EARS: Ubiquitous

El itinerario NO DEBE contener requisitos migratorios redactados por la IA.

✅ **Ejemplo:** Viaje a Santiago de Chile → solo aparece el aviso de RN-024 con su enlace.

❌ **Contraejemplo:** "Para entrar a Chile solo necesitas tu DNI vigente."

---

**RN-026** | Categoría: Generación de itinerario | Prioridad: MUST | Fuente: Alcance RES-10 | Funcionalidad: F-04

Patrón EARS: Ubiquitous

La vista del itinerario DEBE mostrar un aviso que indique que el itinerario es una sugerencia.

✅ **Ejemplo:** Encabezado del itinerario: "Este itinerario es una sugerencia. Revisa los datos marcados como ≈ Estimada o ! Por confirmar antes de tu viaje."

❌ **Contraejemplo:** El itinerario se muestra sin aviso, con el título "Tu plan definitivo".

---

**RN-027** | Categoría: Generación de itinerario | Prioridad: MUST | Fuente: Alcance F-03; BA (propuesta, a confirmar por PO) — PAR-05 | Funcionalidad: F-03

Patrón EARS: State-driven

MIENTRAS el ritmo del viaje sea Relajado, la carga diaria de cada día NO DEBE superar 360 minutos.

✅ **Ejemplo:** Día 1: actividades de 90 + 120 + 60 min y traslados de 60 min = 330 min.

❌ **Contraejemplo:** Día 1 en ritmo Relajado con 5 actividades y una carga diaria de 420 min.

---

**RN-028** | Categoría: Generación de itinerario | Prioridad: MUST | Fuente: Alcance F-03; BA (propuesta, a confirmar por PO) — PAR-05 | Funcionalidad: F-03

Patrón EARS: State-driven

MIENTRAS el ritmo del viaje sea Equilibrado, la carga diaria de cada día NO DEBE superar 480 minutos.

✅ **Ejemplo:** Día 2: actividades de 120 + 90 + 120 + 60 min y traslados de 75 min = 465 min.

❌ **Contraejemplo:** Día 2 en ritmo Equilibrado con una carga diaria de 540 min.

---

**RN-029** | Categoría: Generación de itinerario | Prioridad: MUST | Fuente: Alcance F-03; BA (propuesta, a confirmar por PO) — PAR-05 | Funcionalidad: F-03

Patrón EARS: State-driven

MIENTRAS el ritmo del viaje sea Activo, la carga diaria de cada día NO DEBE superar 600 minutos.

✅ **Ejemplo:** Día 3: actividades por 480 min y traslados por 100 min = 580 min.

❌ **Contraejemplo:** Día 3 en ritmo Activo con una excursión de 540 min y traslados de 180 min = 720 min.

---

**RN-105** | Categoría: Generación de itinerario | Prioridad: MUST | Fuente: Alcance DEP-02 (plan B); auditoría QA C-03 | Funcionalidad: F-03, F-06

Patrón EARS: Unwanted

SI un lugar del itinerario no se puede contrastar con la fuente de lugares, ENTONCES el sistema DEBE mostrar la existencia de ese lugar con el estado ! Por confirmar.

✅ **Ejemplo:** La fuente de lugares no encuentra "Mirador X" → la actividad muestra "Lugar: ! Por confirmar — No pudimos verificar este dato. Confírmalo con la oficina de turismo local antes de tu viaje."

❌ **Contraejemplo:** "Mirador X", que la fuente de lugares no encuentra, aparece como actividad sin ningún estado.

> Si la existencia del lugar entra en la lista canónica de datos factuales y con qué vigencia: 05-preguntas-abiertas PREG-01.

---

**RN-111** | Categoría: Generación de itinerario | Prioridad: MUST | Fuente: Brief §1.4, §3.3; auditoría QA A-12 | Funcionalidad: F-03

Patrón EARS: Unwanted

SI el horario ✓ Verificada de un lugar indica que está cerrado en la fecha programada, ENTONCES el sistema NO DEBE programar ese lugar en esa fecha.

✅ **Ejemplo:** El horario verificado del museo indica cierre los lunes y el Día 3 es lunes → la generación lo programa otro día o propone otra actividad para esa franja.

❌ **Contraejemplo:** El museo aparece el Día 3 (lunes) aunque su horario verificado indica que cierra ese día.

> A qué fecha se refiere una verificación (fecha de consulta frente a fecha del viaje): PREG-24.

---

### 2.4 Adaptación familiar

> Las reglas de esta sección son **orientativas y ajustables**. Un rango de edad no describe las capacidades de un menor concreto: el sistema solo **excluye** una actividad cuando una fuente registrada la declara incompatible con **todo** el rango; en cualquier otro caso informa y deja la decisión al viajero.

---

**RN-030** | Categoría: Adaptación familiar | Prioridad: MUST | Fuente: Alcance F-03; Constitución PRINCIPIO-002 | Funcionalidad: F-03

Patrón EARS: Unwanted

SI una fuente registrada declara para una actividad una edad mínima superior al límite superior del rango de edad de algún menor del grupo, ENTONCES el sistema NO DEBE incluir esa actividad en el itinerario.

✅ **Ejemplo:** Canopy con edad mínima 14 años (✓ Verificada) y un menor de 6–11 años → la actividad se excluye.

❌ **Contraejemplo:** Canopy con edad mínima 14 años (✓ Verificada) aparece en el Día 2 de un grupo con un menor de 6–11 años.

---

**RN-031** | Categoría: Adaptación familiar | Prioridad: MUST | Fuente: Alcance F-03; restricción "no asumir capacidades por rango" | Funcionalidad: F-03

Patrón EARS: Unwanted

SI la edad mínima declarada por una fuente registrada es mayor que el límite inferior y menor o igual que el límite superior del rango de un menor del grupo, ENTONCES el sistema DEBE mostrar junto a la actividad la edad mínima con su estado de confiabilidad.

✅ **Ejemplo:** Taller con edad mínima 8 años (✓ Verificada) y un menor de 6–11 años → la actividad se incluye con "Edad mínima: 8 años · ✓ Verificada — Fuente: sitio oficial del operador · Consultado el 01/12/2026 a las 10:15. Comprueba que cada menor la cumple."

❌ **Contraejemplo:** El mismo taller se incluye sin mostrar la edad mínima, o se excluye suponiendo que el menor tiene 6 años.

> Si la edad mínima es menor o igual que el límite inferior del rango, la actividad se incluye y la edad mínima se muestra igualmente con su estado, por RN-061. Restricciones vencidas y otras restricciones (altura, edad máxima, salud): PREG-28.

---

**RN-032** | Categoría: Adaptación familiar | Prioridad: MUST | Fuente: Constitución PRINCIPIO-002, PRINCIPIO-003 | Funcionalidad: F-03, F-06

Patrón EARS: Unwanted

SI la restricción de edad de una actividad no tiene fuente registrada, ENTONCES el sistema DEBE mostrar esa restricción con el estado ! Por confirmar.

✅ **Ejemplo:** "Paseo en cuatrimoto — Edad mínima: ! Por confirmar — No pudimos verificar este dato. Confírmalo con el operador del tour antes de tu viaje."

❌ **Contraejemplo:** "Paseo en cuatrimoto — Apto desde 5 años", sin fuente.

---

**RN-033** | Categoría: Adaptación familiar | Prioridad: MUST | Fuente: Brief §2.4; BA (propuesta, a confirmar por PO) — PAR-06 | Funcionalidad: F-03

Patrón EARS: State-driven

MIENTRAS el grupo incluya al menos un menor de 0–2 o de 3–5 años, el itinerario generado DEBE incluir en cada día al menos un bloque de descanso de 60 minutos o más.

✅ **Ejemplo:** Grupo con un menor de 3–5 años → Día 1 incluye "Tarde — Descanso en el alojamiento · 90 min".

❌ **Contraejemplo:** Grupo con un menor de 0–2 años → el Día 1 encadena 4 actividades sin ningún bloque de descanso.

---

**RN-034** | Categoría: Adaptación familiar | Prioridad: MUST | Fuente: Constitución PRINCIPIO-002; restricción "no asumir capacidades por rango" | Funcionalidad: F-03

Patrón EARS: Ubiquitous

Una orientación por rango de edad generada por la IA NO DEBE presentarse como restricción de la actividad.

✅ **Ejemplo:** "Sugerencia: esta caminata de 180 min puede resultar exigente para niños de 3–5 años. Tú decides si la mantienes."

❌ **Contraejemplo:** "Prohibido para menores de 6 años", generado por la IA sin fuente registrada.

---

**RN-035** | Categoría: Adaptación familiar | Prioridad: MUST | Fuente: Alcance F-05; restricción "reglas ajustables" | Funcionalidad: F-03, F-05

Patrón EARS: Ubiquitous

El sistema DEBE permitir al viajero conservar una actividad que tenga una orientación por rango de edad.

✅ **Ejemplo:** El viajero mantiene la caminata de 180 min con un menor de 3–5 años → la actividad sigue en el Día 2 tras recargar la app.

❌ **Contraejemplo:** La app elimina la caminata al recargar o bloquea el botón "Mantener".

---

**RN-036** | Categoría: Adaptación familiar | Prioridad: MUST | Fuente: Brief §2.4; BA (propuesta, a confirmar por PO) | Funcionalidad: F-02

Patrón EARS: State-driven

MIENTRAS la solicitud tenga ritmo Activo y el grupo incluya al menos un menor de 0–2 o de 3–5 años, el sistema DEBE mostrar un aviso orientativo sobre la carga diaria de hasta 600 minutos.

✅ **Ejemplo:** "Con ritmo Activo cada día puede sumar hasta 600 min de actividades y traslados. Con niños de 3–5 años quizá prefieras Equilibrado (480 min)."

❌ **Contraejemplo:** El viajero elige Activo con un menor de 0–2 años y no ve ningún aviso, o elige primero Activo, agrega después un menor de 0–2 años y el aviso no aparece.

---

**RN-037** | Categoría: Adaptación familiar | Prioridad: MUST | Fuente: Restricción "reglas ajustables" | Funcionalidad: F-02, F-03

Patrón EARS: Ubiquitous

El sistema NO DEBE cambiar automáticamente el ritmo elegido por el viajero.

✅ **Ejemplo:** Grupo con un menor de 0–2 años y ritmo Activo → tras el aviso de RN-036, el itinerario se genera con ritmo Activo.

❌ **Contraejemplo:** El sistema genera con ritmo Relajado porque detectó un menor de 0–2 años.

---

### 2.5 Personalización

---

**RN-038** | Categoría: Personalización | Prioridad: MUST | Fuente: Alcance F-05 | Funcionalidad: F-05

Patrón EARS: Ubiquitous

El sistema DEBE permitir al viajero eliminar cualquier actividad del itinerario.

✅ **Ejemplo:** El viajero elimina "Tarde — Museo Inka" del Día 2 → el Día 2 queda con 2 actividades.

❌ **Contraejemplo:** Las actividades generadas por la IA no tienen opción de eliminar, o eliminar exige crear una cuenta.

---

**RN-039** | Categoría: Personalización | Prioridad: MUST | Fuente: Alcance F-05 | Funcionalidad: F-05

Patrón EARS: Event-driven

CUANDO el viajero pida una alternativa para una actividad, el sistema DEBE ubicar la alternativa en el mismo día y en la misma franja horaria.

✅ **Ejemplo:** Alternativa para "Día 3 · Tarde — Sacsayhuamán" → "Día 3 · Tarde — Museo de Arte Precolombino".

❌ **Contraejemplo:** La alternativa aparece en "Día 4 · Mañana".

---

**RN-040** | Categoría: Personalización | Prioridad: MUST | Fuente: Alcance F-05; BA (propuesta, a confirmar por PO) | Funcionalidad: F-05

Patrón EARS: Unwanted

SI la alternativa propuesta coincide con una actividad ya presente en el itinerario, ENTONCES el sistema NO DEBE mostrarla como alternativa.

✅ **Ejemplo:** "Mercado de San Pedro" ya está en el Día 1 → la alternativa para el Día 3 es "Barrio de San Blas".

❌ **Contraejemplo:** La alternativa para el Día 3 es "Mercado de San Pedro", que ya figura en el Día 1.

---

**RN-041** | Categoría: Personalización | Prioridad: MUST | Fuente: Alcance F-05 | Funcionalidad: F-05

Patrón EARS: Event-driven

CUANDO el viajero regenere un día, el sistema NO DEBE modificar las actividades de los demás días.

✅ **Ejemplo:** Regenerar el Día 2 de 4 → los Días 1, 3 y 4 quedan idénticos.

❌ **Contraejemplo:** Regenerar el Día 2 reemplaza también la cena del Día 3.

---

**RN-042** | Categoría: Personalización | Prioridad: MUST | Fuente: Alcance F-03, F-05 | Funcionalidad: F-05

Patrón EARS: Event-driven

CUANDO el sistema genere una alternativa o regenere un día, el resultado DEBE cumplir para la solicitud de viaje las reglas RN-021, RN-027 a RN-034 (incluido el bloque de descanso de RN-033) y RN-111.

✅ **Ejemplo:** Ritmo Relajado → el Día 2 regenerado suma 345 min de carga diaria.

❌ **Contraejemplo:** Ritmo Relajado → el Día 2 regenerado suma 540 min.

---

**RN-043** | Categoría: Personalización | Prioridad: MUST | Fuente: Alcance RES-05, SUP-03; Brief R4; BA (propuesta, a confirmar por PO) — PAR-07 | Funcionalidad: F-03, F-05

Patrón EARS: Unwanted

SI un dispositivo ya realizó 15 operaciones de IA exitosas en las últimas 24 horas, ENTONCES el sistema DEBE rechazar la siguiente operación indicando la fecha y hora desde la que podrá intentarla.

✅ **Ejemplo:** Primera operación el 2026-11-30 a las 20:05; la operación 16 llega el 2026-12-01 a las 18:40 → "Alcanzaste el límite de 15 operaciones. Podrás volver a intentarlo desde el 01/12/2026 a las 20:05."

❌ **Contraejemplo:** La operación 16 falla con "Error inesperado", sin indicar el límite ni la hora.

---

**RN-044** | Categoría: Personalización | Prioridad: SHOULD | Fuente: Alcance F-09; BA (propuesta, a confirmar por PO) | Funcionalidad: F-09

Patrón EARS: Event-driven

CUANDO el viajero agregue una actividad propia y la carga diaria resultante supere el límite de su ritmo, el sistema DEBE mostrar un aviso con los minutos excedidos.

✅ **Ejemplo:** Ritmo Relajado, Día 1 con 330 min; el viajero agrega una actividad de 90 min → "Este día suma 420 min y supera en 60 min el ritmo Relajado (360 min)."

❌ **Contraejemplo:** La carga sube a 420 min sin ningún aviso.

---

**RN-045** | Categoría: Personalización | Prioridad: SHOULD | Fuente: Alcance F-09 | Funcionalidad: F-09

Patrón EARS: Ubiquitous

El sistema DEBE permitir al viajero conservar una actividad propia aunque la carga diaria supere el límite de su ritmo.

✅ **Ejemplo:** Tras el aviso de RN-044, el viajero confirma y el Día 1 queda con 420 min.

❌ **Contraejemplo:** La app impide guardar la actividad propia porque supera los 360 min.

---

### 2.6 Presupuesto

> Todas las reglas de esta sección son **SHOULD** porque dependen de F-07, que es post-MVP. El presupuesto no se pide en el formulario del MVP.

---

**RN-046** | Categoría: Presupuesto | Prioridad: SHOULD | Fuente: Alcance F-07; BA (propuesta, a confirmar por PO) | Funcionalidad: F-07

Patrón EARS: Unwanted

SI el viajero indica un presupuesto menor o igual que 0, ENTONCES el sistema DEBE rechazar el presupuesto.

✅ **Ejemplo:** Presupuesto 1500.00 PEN → aceptado.

❌ **Contraejemplo:** Presupuesto 0.00 PEN o −200.00 PEN → aceptado.

---

**RN-047** | Categoría: Presupuesto | Prioridad: SHOULD | Fuente: Alcance F-07; Constitución PRINCIPIO-011 | Funcionalidad: F-07

Patrón EARS: Event-driven

CUANDO el viajero indique un presupuesto, el sistema DEBE registrarlo con un código de moneda ISO 4217.

✅ **Ejemplo:** `{ "amount": 1500.00, "currency": "PEN" }`.

❌ **Contraejemplo:** Presupuesto "1500" sin moneda, que el sistema interpreta como USD.

---

**RN-048** | Categoría: Presupuesto | Prioridad: SHOULD | Fuente: Alcance F-07; Constitución PRINCIPIO-003 | Funcionalidad: F-07

Patrón EARS: Ubiquitous

El costo estimado total del viaje DEBE mostrarse con el estado ≈ Estimada.

✅ **Ejemplo:** "Total estimado: S/ 1,320.00 · ≈ Estimada — Valor aproximado basado en precios promedio de entradas, comidas y transporte local de la zona. Te recomendamos confirmarlo antes de tu viaje."

❌ **Contraejemplo:** "Total: S/ 1,320.00" sin estado, o con "✓ Verificada".

---

**RN-049** | Categoría: Presupuesto | Prioridad: SHOULD | Fuente: Alcance F-07; Brief §3.3 | Funcionalidad: F-07

Patrón EARS: Event-driven

CUANDO el costo estimado total supere el presupuesto indicado, el sistema DEBE mostrar un aviso de presupuesto insuficiente con la diferencia en la moneda del presupuesto.

✅ **Ejemplo:** Presupuesto S/ 1,200.00, costo estimado S/ 1,450.00 → "Tu presupuesto podría quedarse corto en S/ 250.00 · ≈ Estimada — Valor aproximado basado en el costo estimado del itinerario. Te recomendamos confirmarlo antes de tu viaje."

❌ **Contraejemplo:** Costo estimado S/ 1,450.00 frente a un presupuesto de S/ 1,200.00 sin ningún aviso.

---

**RN-050** | Categoría: Presupuesto | Prioridad: SHOULD | Fuente: Constitución PRINCIPIO-002 ("toda consulta recibe respuesta") | Funcionalidad: F-07

Patrón EARS: Unwanted

SI el presupuesto es insuficiente, ENTONCES el sistema NO DEBE ocultar el itinerario generado.

✅ **Ejemplo:** Presupuesto S/ 1,200.00 insuficiente → el itinerario se muestra completo con el aviso de RN-049.

❌ **Contraejemplo:** "No podemos generar un itinerario con S/ 1,200.00", sin mostrar nada más.

---

**RN-051** | Categoría: Presupuesto | Prioridad: SHOULD | Fuente: Constitución PRINCIPIO-002; requisito del PO | Funcionalidad: F-07

Patrón EARS: Ubiquitous

El sistema NO DEBE aplicar una tarifa diferenciada de niño cuyo dato no esté en estado ✓ Verificada.

✅ **Ejemplo:** Tarifa de niño S/ 15.00 · ✓ Verificada (Fuente: sitio oficial del museo · Consultado el 01/12/2026 a las 10:15) → el cálculo usa S/ 15.00 para el menor.

❌ **Contraejemplo:** La IA aplica "50 % de descuento para niños" sin fuente.

---

**RN-052** | Categoría: Presupuesto | Prioridad: SHOULD | Fuente: BA (propuesta, a confirmar por PO) | Funcionalidad: F-07

Patrón EARS: Unwanted

SI la tarifa de niño de una actividad no es verificable, ENTONCES el cálculo del costo estimado DEBE usar la tarifa general para ese menor.

✅ **Ejemplo:** Tarifa general S/ 30.00 (≈ Estimada), tarifa de niño sin fuente → el menor se calcula con S/ 30.00 y se muestra "Tarifa de niño: ! Por confirmar — No pudimos verificar este dato. Confírmalo con el establecimiento antes de tu viaje."

❌ **Contraejemplo:** El menor se calcula con S/ 0.00 porque "los niños suelen entrar gratis".

---

**RN-053** | Categoría: Presupuesto | Prioridad: SHOULD | Fuente: Constitución PRINCIPIO-011 | Funcionalidad: F-07

Patrón EARS: Ubiquitous

Todo monto visible DEBE formatearse con la configuración regional y la moneda del dato.

✅ **Ejemplo:** `Intl.NumberFormat('es-PE', { style: 'currency', currency: 'PEN' }).format(152)` → "S/ 152.00".

❌ **Contraejemplo:** "S/.152.00", construido concatenando cadenas.

---

**RN-054** | Categoría: Presupuesto | Prioridad: SHOULD | Fuente: Brief §2.5; Constitución PRINCIPIO-003 | Funcionalidad: F-07

Patrón EARS: Event-driven

CUANDO el sistema convierta un monto entre monedas, el monto convertido DEBE mostrarse con estado ≈ Estimada y con la fecha del tipo de cambio en su base de cálculo.

✅ **Ejemplo:** "COP 200,000 ≈ S/ 180.00 · ≈ Estimada — Valor aproximado basado en el tipo de cambio del 01/12/2026. Te recomendamos confirmarlo antes de tu viaje."

❌ **Contraejemplo:** "COP 200,000 = S/ 180.00" sin estado ni fecha del tipo de cambio.

---

### 2.7 Confiabilidad de datos

---

**RN-055** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-002 | Funcionalidad: F-03, F-06

Patrón EARS: Unwanted

SI un precio no proviene de una fuente registrada, ENTONCES el backend DEBE entregarlo con el estado ≈ Estimada o ! Por confirmar.

✅ **Ejemplo:** "Entrada: S/ 30.00 · ≈ Estimada — Valor aproximado basado en precios promedio de museos de la zona…".

❌ **Contraejemplo:** "Entrada a Machu Picchu: S/ 152", sin fuente ni estado.

---

**RN-056** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-002 | Funcionalidad: F-03, F-06

Patrón EARS: Unwanted

SI un horario no proviene de una fuente registrada, ENTONCES el backend DEBE entregarlo con el estado ≈ Estimada o ! Por confirmar.

✅ **Ejemplo:** "Horario: ! Por confirmar — No pudimos verificar este dato. Confírmalo con el establecimiento antes de tu viaje."

❌ **Contraejemplo:** "Abre a las 6:00", sin fuente.

---

**RN-057** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-002 | Funcionalidad: F-03, F-06

Patrón EARS: Unwanted

SI la disponibilidad de una actividad o servicio no proviene de una fuente registrada, ENTONCES el backend DEBE entregarla con el estado ≈ Estimada o ! Por confirmar.

✅ **Ejemplo:** "Disponibilidad del tour el 19/12/2026: ! Por confirmar — No pudimos verificar este dato. Confírmalo con el operador del tour antes de tu viaje."

❌ **Contraejemplo:** "Quedan cupos para el tour del 2026-12-19", sin fuente.

---

**RN-058** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-002 | Funcionalidad: F-03, F-06

Patrón EARS: Unwanted

SI una restricción de actividad (edad, altura, salud o accesibilidad) no proviene de una fuente registrada, ENTONCES el backend DEBE entregarla con el estado ≈ Estimada o ! Por confirmar.

✅ **Ejemplo:** "Accesible en silla de ruedas: ! Por confirmar — No pudimos verificar este dato. Confírmalo con el museo antes de tu viaje."

❌ **Contraejemplo:** "Altura mínima: 1,20 m", generado por la IA sin fuente.

---

**RN-059** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-002, PRINCIPIO-008 | Funcionalidad: F-03, F-06

Patrón EARS: Ubiquitous

El backend NO DEBE entregar al cliente un requisito migratorio o sanitario redactado por el LLM (complementa RN-025; postura fijada por la decisión vigente D-06: solo aviso y enlaces oficiales).

✅ **Ejemplo:** Viaje a Ciudad de México → aviso de RN-024 con enlace oficial, sin listar documentos.

❌ **Contraejemplo:** "Con tu pasaporte peruano entras sin problema a México."

---

**RN-060** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-002; Alcance DEP-05 | Funcionalidad: F-04, F-06

Patrón EARS: State-driven

MIENTRAS no exista una fuente registrada de tiempos de desplazamiento, todo tiempo de traslado DEBE mostrarse con el estado ≈ Estimada.

✅ **Ejemplo:** "Traslado a Sacsayhuamán: 20 min · ≈ Estimada — Valor aproximado basado en la distancia entre puntos…".

❌ **Contraejemplo:** "Traslado: 20 min" sin estado.

---

**RN-061** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-003; Alcance F-06 | Funcionalidad: F-06

Patrón EARS: Ubiquitous

Cada dato factual DEBE mostrar exactamente un estado de confiabilidad: ✓ Verificada, ≈ Estimada o ! Por confirmar.

✅ **Ejemplo:** "Duración: 120 min · ≈ Estimada — Valor aproximado basado en la duración habitual de esta actividad. Te recomendamos confirmarlo antes de tu viaje."

❌ **Contraejemplo:** "Duración: 120 min" sin estado, o con "≈ Estimada" y "! Por confirmar" a la vez.

---

**RN-062** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-003, PRINCIPIO-010 | Funcionalidad: F-06

Patrón EARS: Ubiquitous

Cada estado de confiabilidad DEBE mostrarse con ícono, texto y color.

✅ **Ejemplo:** Ícono "≈", texto "Estimada" y color ámbar con contraste ≥ 4.5:1.

❌ **Contraejemplo:** Solo un punto amarillo junto a "S/ 40–60".

---

**RN-063** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-002 | Funcionalidad: F-06

Patrón EARS: Ubiquitous

El LLM NO DEBE asignar el estado ✓ Verificada a ningún dato.

✅ **Ejemplo:** El LLM devuelve el horario con `reliability_status: "to_confirm"`; la capa de verificación consulta la fuente y lo cambia a `verified`.

❌ **Contraejemplo:** El LLM devuelve `reliability_status: "verified"` y el backend lo acepta.

---

**RN-064** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-002 | Funcionalidad: F-06

Patrón EARS: Unwanted

SI un dato llega al backend con estado `verified` sin una `source` registrada, ENTONCES el backend DEBE rechazar ese dato.

✅ **Ejemplo:** `{ "price": 30, "reliability_status": "verified", "source": null }` → rechazado por la validación del esquema.

❌ **Contraejemplo:** El mismo dato se muestra como "✓ Verificada — Fuente: —".

> Qué significa "rechazar" según el origen del dato (salida del LLM, entrada mal formada, atestación inválida o vigencia vencida): ver la semántica única de RN-106 (auditoría Clase 2, QB-03). Si este ejemplo llega en una entrada del cliente es el caso (b), dato mal formado → 422; si llega en la salida del LLM es el caso (a), salida inválida.

---

**RN-065** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-003 | Funcionalidad: F-06

Patrón EARS: Ubiquitous

Todo dato ✓ Verificada DEBE acompañarse del texto "Fuente: {fuente} · Consultado el {dd/mm/aaaa} a las {HH:mm}".

✅ **Ejemplo:** "✓ Verificada — Fuente: sitio oficial del museo · Consultado el 01/12/2026 a las 10:15".

❌ **Contraejemplo:** "✓ Verificada" sin fuente ni fecha de consulta.

---

**RN-066** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-003 | Funcionalidad: F-06

Patrón EARS: Ubiquitous

Todo dato ≈ Estimada DEBE acompañarse del texto "Valor aproximado basado en {base_de_cálculo}. Te recomendamos confirmarlo antes de tu viaje."

✅ **Ejemplo:** "Almuerzo en Barranco: S/ 40–60 por persona · ≈ Estimada — Valor aproximado basado en precios promedio de la zona. Te recomendamos confirmarlo antes de tu viaje."

❌ **Contraejemplo:** "Almuerzo: S/ 40–60 · ≈ Estimada" sin base de cálculo ni recomendación.

---

**RN-067** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-003 | Funcionalidad: F-06

Patrón EARS: Ubiquitous

Todo dato ! Por confirmar DEBE acompañarse del texto "No pudimos verificar este dato. Confírmalo con {fuente_sugerida} antes de tu viaje."

✅ **Ejemplo:** "Horario del Museo Larco: ! Por confirmar — No pudimos verificar este dato. Confírmalo con el establecimiento antes de tu viaje."

❌ **Contraejemplo:** "Horario: ! Por confirmar" sin la recomendación de confirmarlo.

---

**RN-068** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-003 | Funcionalidad: F-06

Patrón EARS: State-driven

MIENTRAS hayan pasado más de 7 días desde la consulta de un precio, horario o restricción de actividad ✓ Verificada, el sistema DEBE mostrar ese dato como ! Por confirmar.

✅ **Ejemplo:** Horario consultado el 2026-12-01 a las 10:00; visto el 2026-12-08 a las 10:01 → "! Por confirmar".

❌ **Contraejemplo:** El mismo horario se muestra como "✓ Verificada" el 2026-12-10.

---

**RN-069** | Categoría: Confiabilidad de datos | Prioridad: WON'T (v1) | Fuente: Constitución PRINCIPIO-003 | Funcionalidad: F-06

Patrón EARS: State-driven

MIENTRAS hayan pasado más de 24 horas desde la consulta de un requisito migratorio o sanitario ✓ Verificada, el sistema DEBE mostrar ese dato como ! Por confirmar.

✅ **Ejemplo:** Enlace oficial consultado el 2026-12-01 a las 09:00; visto el 2026-12-02 a las 09:01 → "! Por confirmar".

❌ **Contraejemplo:** Un requisito de visa consultado el 2026-12-01 se muestra como "✓ Verificada" el 2026-12-04.

> **WON'T (v1)** por la decisión vigente D-06: la app no muestra requisitos migratorios ni sanitarios de ingreso (solo aviso y enlaces oficiales). Se reactiva (vuelve a MUST) si una versión posterior muestra requisitos. Reclasificada el 2026-11-07 (auditoría Clase 2, QB-06; Anexo D).

---

**RN-070** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-003 | Funcionalidad: F-06

Patrón EARS: Unwanted

SI la fuente de un dato de disponibilidad, clima o tiempo de traslado no declara fecha de vigencia, ENTONCES el sistema NO DEBE mostrar ese dato como ✓ Verificada.

✅ **Ejemplo:** API de rutas sin fecha de vigencia → "Traslado: 25 min · ≈ Estimada — Valor aproximado basado en la ruta calculada por la API de rutas. Te recomendamos confirmarlo antes de tu viaje."

❌ **Contraejemplo:** "Traslado: 25 min · ✓ Verificada" con una fuente que no declara vigencia.

---

**RN-071** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-002 | Funcionalidad: F-06

Patrón EARS: Unwanted

SI el sistema no puede verificar un dato factual, ENTONCES el sistema DEBE informar al viajero que el dato no pudo verificarse.

✅ **Ejemplo:** No hay fuente para el horario del Museo Larco → la actividad muestra "! Por confirmar — No pudimos verificar este dato…".

❌ **Contraejemplo:** El campo de horario queda vacío o desaparece de la actividad.

> Complementa RN-061 y RN-067: RN-071 prohíbe omitir el dato; RN-067 fija el texto con el que se informa.

---

**RN-102** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-002; auditoría QA C-01 | Funcionalidad: F-03, F-06

Patrón EARS: Ubiquitous

El backend NO DEBE entregar al cliente un campo de texto libre (nombre, descripción, nota u orientación) que contenga un dato factual.

✅ **Ejemplo:** Descripción: "Colección de cerámica precolombina"; el horario va en su propio campo, con su estado.

❌ **Contraejemplo:** Descripción: "Abre a las 9:00 y los domingos la entrada es gratis".

> Mecanismo de cumplimiento (esquema sin texto libre, detector o casos del golden set): PREG-05.

---

**RN-103** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-002; auditoría QA C-01 | Funcionalidad: F-03, F-06

Patrón EARS: Unwanted

SI un dato de clima no proviene de una fuente registrada, ENTONCES el backend DEBE entregarlo con el estado ≈ Estimada o ! Por confirmar.

✅ **Ejemplo:** "Clima en diciembre: ! Por confirmar — No pudimos verificar este dato. Confírmalo con el servicio meteorológico oficial antes de tu viaje."

❌ **Contraejemplo:** "En diciembre casi no llueve en Cusco", sin fuente ni estado.

---

**RN-104** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-008; auditoría QA C-01 | Funcionalidad: F-03

Patrón EARS: Unwanted

SI ninguna fuente oficial registrada lo respalda, ENTONCES el sistema NO DEBE describir un destino como seguro.

✅ **Ejemplo:** Sin fuente oficial registrada, el itinerario no califica la seguridad del destino.

❌ **Contraejemplo:** "Barranco es una zona totalmente segura de noche", generado por la IA sin fuente.

---

**RN-106** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-002; Glosario "Fuente registrada"; auditoría QA C-04 | Funcionalidad: F-06

Patrón EARS: Unwanted

SI un dato llega al backend con estado `verified` y su `source` no pertenece al registro de fuentes ni lo respalda una atestación válida emitida por el backend, ENTONCES el backend DEBE rechazar ese dato.

✅ **Ejemplo:** `{ "opening_hours": "09:00–17:00", "reliability_status": "verified", "source": "https://blog-de-viajes.example" }`, fuente no registrada → rechazado.

❌ **Contraejemplo:** El backend acepta el dato porque `source` no es nulo.

> **Semántica única de "rechazar" un `verified` (auditoría Clase 2, QB-03; coherente con PRINCIPIO-002: "un dato con `verified` sin `source` registrada se rechaza").** Aplica a RN-063, RN-064 y RN-106 según el origen del dato:
> - **(a) Salida del LLM con `reliability_status: "verified"`** → la salida completa es inválida por esquema (`LlmItineraryOutput` no admite `verified`). Se aplica la política existente: 1 reintento (RN-085, PAR-10), luego falla con `generation_invalid_output`, sin descontar cupo (RN-089). El LLM nunca asigna `verified` (RN-063).
> - **(b) Dato mal formado o campo no declarado en una entrada** (p. ej., `verified` con `source: null` o `""`, o sin `attestation`) → la solicitud se rechaza con 422.
> - **(c) Dato `verified` recibido del cliente con una atestación inválida** (alterada, ausente o no emitida por el backend) **o con una fuente que no está en el registro y que ninguna atestación válida respalda** → la solicitud se rechaza con 422. Es un rechazo, no una degradación.
> - **(d) Dato `verified` con atestación válida cuya fuente fue retirada del registro después de emitirse, o cuya vigencia venció** → no es un rechazo sino vigencia: el dato se degrada a `to_confirm` con el motivo `source_removed` o `expired` (RN-068, RN-070, RN-107), y nunca vuelve a `verified`.
>
> La atestación válida prueba que el backend emitió el dato con una fuente que estaba registrada en ese momento; por eso (c) se rechaza y (d) se degrada. Códigos HTTP y campos: 07 §3.2.

> Contenido, responsable y proceso del registro de fuentes, y quién genera la fuente sugerida: PREG-06.

---

**RN-107** | Categoría: Confiabilidad de datos | Prioridad: MUST | Fuente: Constitución PRINCIPIO-003; auditoría QA C-08 | Funcionalidad: F-01, F-06

Patrón EARS: Event-driven

CUANDO se abra un itinerario guardado, el sistema DEBE mostrar cada dato con el estado que corresponda a su vigencia en ese momento (RN-068 a RN-070).

✅ **Ejemplo:** Horario ✓ Verificada consultado el 01/12/2026 a las 10:00; el invitado reabre el itinerario el 09/12/2026 → el horario se muestra como ! Por confirmar.

❌ **Contraejemplo:** El itinerario reabierto el 09/12/2026 sigue mostrando "✓ Verificada" para ese horario.

> Dónde se calcula la vigencia sin persistir datos del invitado (PRINCIPIO-005 y PRINCIPIO-009): PREG-07.

---

### 2.8 Acceso e invitados

---

**RN-072** | Categoría: Acceso e invitados | Prioridad: MUST | Fuente: Alcance §0, F-01; Brief §3.4 | Funcionalidad: F-01

Patrón EARS: Ubiquitous

El sistema NO DEBE exigir una cuenta para generar, consultar o ajustar un itinerario.

✅ **Ejemplo:** Un invitado genera un itinerario a Arequipa, elimina una actividad y regenera el Día 2 sin registrarse.

❌ **Contraejemplo:** Al pulsar "Regenerar día" aparece "Crea una cuenta para continuar".

---

**RN-073** | Categoría: Acceso e invitados | Prioridad: MUST | Fuente: Alcance F-01; Constitución PRINCIPIO-005 | Funcionalidad: F-01

Patrón EARS: Event-driven

CUANDO se genere o ajuste el itinerario de un invitado, el sistema DEBE guardarlo en el almacenamiento local del dispositivo.

✅ **Ejemplo:** El invitado regenera el Día 3 → la nueva versión queda guardada en el almacenamiento local del dispositivo (la tecnología se fija en el ADR de stack, D-07).

❌ **Contraejemplo:** El itinerario solo existe en memoria y se pierde al recargar la página.

---

**RN-074** | Categoría: Acceso e invitados | Prioridad: MUST | Fuente: Constitución PRINCIPIO-005 | Funcionalidad: F-01

Patrón EARS: Ubiquitous

El backend NO DEBE persistir datos de viaje de un invitado.

✅ **Ejemplo:** Un invitado genera un viaje a Cusco → el backend registra solo `request_id`, duración y código de estado; la base de datos no tiene filas nuevas.

❌ **Contraejemplo:** El backend guarda los itinerarios de invitados "para analítica".

---

**RN-075** | Categoría: Acceso e invitados | Prioridad: MUST | Fuente: Alcance RES-06 | Funcionalidad: F-01

Patrón EARS: Event-driven

CUANDO el sistema guarde por primera vez un itinerario en el dispositivo de un invitado, el sistema DEBE mostrar un aviso de que el itinerario se perderá si se borran los datos del navegador o de la app.

✅ **Ejemplo:** "Tu itinerario se guardó en este dispositivo. Si borras los datos del navegador, lo perderás."

❌ **Contraejemplo:** El invitado borra el historial y pierde su itinerario sin haber recibido ningún aviso.

---

**RN-076** | Categoría: Acceso e invitados | Prioridad: MUST | Fuente: Alcance F-01, F-11 | Funcionalidad: F-01

Patrón EARS: Event-driven

CUANDO un invitado con un itinerario guardado solicite generar uno nuevo, el sistema DEBE pedir confirmación antes de reemplazar el itinerario guardado.

✅ **Ejemplo:** Itinerario a Cusco guardado; el invitado pide uno a Arequipa → "Esto reemplazará tu itinerario a Cusco. ¿Continuar?"

❌ **Contraejemplo:** El itinerario a Cusco se sobrescribe sin preguntar.

---

**RN-077** | Categoría: Acceso e invitados | Prioridad: SHOULD | Fuente: Alcance F-11; BA (propuesta, a confirmar por PO) — PAR-08 | Funcionalidad: F-11

Patrón EARS: Optional

DONDE esté disponible la gestión de varios itinerarios locales, el sistema DEBE permitir al invitado conservar hasta 5 itinerarios en su dispositivo.

✅ **Ejemplo:** El invitado guarda itinerarios a Cusco, Arequipa y Bogotá y alterna entre ellos.

❌ **Contraejemplo:** Con F-11 activa, generar el segundo itinerario borra el primero.

---

**RN-078** | Categoría: Acceso e invitados | Prioridad: MUST | Fuente: Alcance F-01 | Funcionalidad: F-01

Patrón EARS: Event-driven

CUANDO un invitado abra la app con un itinerario guardado en su dispositivo, el sistema DEBE mostrar ese itinerario sin volver a pedir los datos de la solicitud.

✅ **Ejemplo:** El 2026-12-19, ya en Cusco, el invitado abre la app y ve su itinerario del 2026-12-18 al 2026-12-21.

❌ **Contraejemplo:** La app abre el formulario vacío y el itinerario solo es accesible desde un menú oculto.

---

**RN-079** | Categoría: Acceso e invitados | Prioridad: SHOULD | Fuente: Constitución PRINCIPIO-005; Alcance F-10 | Funcionalidad: F-10

Patrón EARS: Event-driven

CUANDO un invitado con itinerarios locales cree una cuenta o inicie sesión, el sistema DEBE pedir su consentimiento explícito para asociar cada viaje a la cuenta, de uno en uno.

✅ **Ejemplo:** 2 viajes locales → la app muestra "¿Guardar 'Cusco, 2026-12-18' en tu cuenta?" y "¿Guardar 'Arequipa, 2027-01-15' en tu cuenta?" por separado.

❌ **Contraejemplo:** Un único "Guardar todo en mi cuenta" marcado por defecto.

---

**RN-080** | Categoría: Acceso e invitados | Prioridad: SHOULD | Fuente: Constitución PRINCIPIO-005 | Funcionalidad: F-10

Patrón EARS: Ubiquitous

El sistema NO DEBE asociar a una cuenta un viaje local sin el consentimiento explícito para ese viaje.

✅ **Ejemplo:** El usuario registrado consiente solo "Cusco" → solo "Cusco" se sube a la nube.

❌ **Contraejemplo:** Al iniciar sesión, todos los viajes locales se suben automáticamente.

---

**RN-081** | Categoría: Acceso e invitados | Prioridad: SHOULD | Fuente: Constitución PRINCIPIO-005 | Funcionalidad: F-10

Patrón EARS: Unwanted

SI el usuario registrado no da su consentimiento para asociar un viaje local, ENTONCES el sistema DEBE conservar ese viaje solo en el dispositivo.

✅ **Ejemplo:** El usuario registrado rechaza "Arequipa" → el viaje sigue disponible en el dispositivo y no existe en la cuenta.

❌ **Contraejemplo:** El viaje rechazado se borra del dispositivo.

---

**RN-082** | Categoría: Acceso e invitados | Prioridad: SHOULD | Fuente: Constitución PRINCIPIO-002, PRINCIPIO-003 | Funcionalidad: F-10

Patrón EARS: Event-driven

CUANDO el sistema asocie un viaje local a una cuenta, el sistema DEBE conservar el estado de confiabilidad y la fecha de consulta originales de cada dato.

✅ **Ejemplo:** Horario ✓ Verificada consultado el 2026-12-01 a las 10:15 → en la cuenta sigue con esa fecha y pasa a ! Por confirmar el 2026-12-08 a las 10:16.

❌ **Contraejemplo:** Al migrar, el horario se marca "Consultado el 05/12/2026" con la fecha de la migración.

---

### 2.9 Fallas del servicio de IA

---

**RN-083** | Categoría: Fallas del servicio de IA | Prioridad: MUST | Fuente: Alcance DEP-01; BA (propuesta, a confirmar por PO) — PAR-09 | Funcionalidad: F-03

Patrón EARS: Unwanted

SI la generación de un itinerario completo no recibe respuesta en 60 segundos, ENTONCES el sistema DEBE mostrar un mensaje de error con la opción de reintentar.

✅ **Ejemplo:** El proveedor no responde a los 60 s → "No pudimos generar tu itinerario. Reintentar".

❌ **Contraejemplo:** La pantalla de carga sigue indefinidamente después de 3 minutos.

---

**RN-084** | Categoría: Fallas del servicio de IA | Prioridad: MUST | Fuente: Alcance DEP-01; BA (propuesta, a confirmar por PO) — PAR-09 | Funcionalidad: F-05

Patrón EARS: Unwanted

SI una alternativa o la regeneración de un día no recibe respuesta en 30 segundos, ENTONCES el sistema DEBE mostrar un mensaje de error con la opción de reintentar.

✅ **Ejemplo:** "Regenerar Día 2" sin respuesta a los 30 s → "No pudimos regenerar el Día 2. Reintentar".

❌ **Contraejemplo:** El botón queda deshabilitado sin mensaje.

---

**RN-085** | Categoría: Fallas del servicio de IA | Prioridad: MUST | Fuente: Constitución PRINCIPIO-002; BA (propuesta, a confirmar por PO) — PAR-10 | Funcionalidad: F-03, F-05

Patrón EARS: Unwanted

SI la respuesta del LLM no cumple el esquema estructurado, ENTONCES el backend DEBE reintentar la operación 1 vez antes de informar el error al cliente.

✅ **Ejemplo:** Primera respuesta sin `reliability_status` → reintento; la segunda es válida → el viajero recibe el itinerario.

❌ **Contraejemplo:** El backend reintenta 5 veces y agota la cuota diaria de la capa gratuita.

---

**RN-086** | Categoría: Fallas del servicio de IA | Prioridad: MUST | Fuente: Alcance DEP-01; Constitución PRINCIPIO-009 | Funcionalidad: F-03, F-05

Patrón EARS: Optional

DONDE exista un proveedor de IA secundario configurado, el backend PUEDE redirigir la operación al proveedor secundario cuando el principal no responda.

✅ **Ejemplo:** El proveedor principal devuelve HTTP 503 → el backend usa el secundario y registra el proveedor y la versión del modelo usados.

❌ **Contraejemplo:** El endpoint llama directamente al SDK del segundo proveedor, saltándose la capa de abstracción.

---

**RN-087** | Categoría: Fallas del servicio de IA | Prioridad: MUST | Fuente: Alcance F-05 | Funcionalidad: F-05

Patrón EARS: Unwanted

SI falla la regeneración de un día, ENTONCES el sistema DEBE conservar ese día sin cambios.

✅ **Ejemplo:** Falla "Regenerar Día 2" → el Día 2 sigue con sus 3 actividades originales.

❌ **Contraejemplo:** Tras la falla, el Día 2 queda vacío.

---

**RN-088** | Categoría: Fallas del servicio de IA | Prioridad: MUST | Fuente: BA (propuesta, a confirmar por PO) | Funcionalidad: F-02, F-03

Patrón EARS: Unwanted

SI falla la generación de un itinerario, ENTONCES el sistema DEBE conservar los datos ingresados en el formulario de solicitud.

✅ **Ejemplo:** Falla la generación → al pulsar "Reintentar" el formulario mantiene Cusco, 2026-12-18 a 2026-12-21, Familia, 1 menor de 3–5 años, Equilibrado.

❌ **Contraejemplo:** Tras la falla, el formulario aparece vacío.

---

**RN-089** | Categoría: Fallas del servicio de IA | Prioridad: MUST | Fuente: BA (propuesta, a confirmar por PO) | Funcionalidad: F-03, F-05

Patrón EARS: Unwanted

SI una operación de IA falla, ENTONCES el sistema NO DEBE contarla dentro del límite de RN-043.

✅ **Ejemplo:** 10 operaciones exitosas y 2 fallidas → el dispositivo tiene 5 operaciones disponibles.

❌ **Contraejemplo:** Las 2 fallidas se descuentan y el dispositivo queda con 3 operaciones disponibles.

---

**RN-108** | Categoría: Fallas del servicio de IA | Prioridad: MUST | Fuente: Constitución PRINCIPIO-002 ("toda consulta recibe respuesta"); RN-087; auditoría QA C-09 | Funcionalidad: F-05

Patrón EARS: Unwanted

SI no existe una alternativa o un día regenerado que cumpla RN-042, ENTONCES el sistema DEBE informar al viajero que no encontró una opción válida y NO DEBE modificar el itinerario.

✅ **Ejemplo:** Alternativa para "Día 2 · Tarde" sin ninguna opción compatible con un menor de 0–2 años → "No encontramos otra actividad que encaje con tu grupo en esta franja." y la actividad original se mantiene.

❌ **Contraejemplo:** La consulta queda sin respuesta, o la actividad se reemplaza por una que incumple RN-030.

> Qué más se ofrece en ese caso, y qué pasa si la generación completa no encuentra actividades válidas: PREG-08.

---

**RN-109** | Categoría: Fallas del servicio de IA | Prioridad: MUST | Fuente: Constitución PRINCIPIO-002; auditoría QA C-10 | Funcionalidad: F-03, F-05

Patrón EARS: Ubiquitous

El backend NO DEBE asignar un estado de confiabilidad por defecto a un dato factual que el LLM entregue sin estado.

✅ **Ejemplo:** Una actividad llega con precio y sin `reliability_status` → la respuesta es inválida y se aplican RN-022 y RN-085.

❌ **Contraejemplo:** El backend completa el campo con `estimated` para no perder la respuesta.

---

**RN-110** | Categoría: Fallas del servicio de IA | Prioridad: MUST | Fuente: Alcance F-03; RN-017; auditoría QA C-10 | Funcionalidad: F-03

Patrón EARS: Unwanted

SI el itinerario devuelto por el LLM no contiene exactamente un día por cada fecha del viaje, ENTONCES el backend NO DEBE entregarlo al cliente.

✅ **Ejemplo:** Solicitud de 4 días; el LLM devuelve 3 → el backend no la entrega y aplica RN-085.

❌ **Contraejemplo:** El backend entrega 3 días y el viajero no sabe que falta el Día 4.

> Otras respuestas válidas pero incompletas (carga excedida, actividad excluida por edad, salida truncada) y salvado parcial: PREG-09.

---

### 2.10 Pérdida de conexión

---

**RN-090** | Categoría: Pérdida de conexión | Prioridad: MUST | Fuente: BA (propuesta, a confirmar por PO) | Funcionalidad: F-03, F-05

Patrón EARS: State-driven

MIENTRAS el dispositivo no tenga conexión a internet, el sistema DEBE deshabilitar las acciones que requieren IA (generar, pedir alternativa y regenerar día).

✅ **Ejemplo:** Sin conexión → "Regenerar día" aparece deshabilitado con el texto "Necesitas conexión para regenerar".

❌ **Contraejemplo:** Sin conexión, el viajero pulsa "Regenerar día" y la operación queda colgada.

---

**RN-091** | Categoría: Pérdida de conexión | Prioridad: MUST | Fuente: BA (propuesta, a confirmar por PO) | Funcionalidad: F-01

Patrón EARS: State-driven

MIENTRAS el dispositivo no tenga conexión a internet, el sistema DEBE mostrar un indicador de "Sin conexión".

✅ **Ejemplo:** Banner "Sin conexión. Algunas acciones no están disponibles."

❌ **Contraejemplo:** No hay indicador y las acciones fallan sin explicación.

---

**RN-092** | Categoría: Pérdida de conexión | Prioridad: MUST | Fuente: Alcance F-01 | Funcionalidad: F-01, F-05

Patrón EARS: Unwanted

SI la conexión se pierde durante una operación de IA, ENTONCES el sistema NO DEBE alterar el itinerario guardado en el dispositivo.

✅ **Ejemplo:** Se pierde la conexión mientras se regenera el Día 3 → el itinerario local conserva la versión anterior del Día 3.

❌ **Contraejemplo:** El Día 3 queda guardado a medias, con 1 de 3 actividades.

---

**RN-093** | Categoría: Pérdida de conexión | Prioridad: MUST | Fuente: BA (propuesta, a confirmar por PO) | Funcionalidad: F-03, F-05

Patrón EARS: Event-driven

CUANDO se restablezca la conexión después de interrumpir una operación de IA, el sistema DEBE ofrecer al viajero reintentar esa operación.

✅ **Ejemplo:** Vuelve la conexión → "Se interrumpió la regeneración del Día 3. ¿Reintentar?"

❌ **Contraejemplo:** El sistema reenvía la operación sin preguntar y consume una operación del límite de RN-043.

---

**RN-094** | Categoría: Pérdida de conexión | Prioridad: COULD | Fuente: Alcance F-14 | Funcionalidad: F-14

Patrón EARS: Optional

DONDE esté disponible la consulta sin conexión, el sistema DEBE permitir al viajero abrir su itinerario guardado sin conexión a internet.

✅ **Ejemplo:** En modo avión en Machu Picchu, el viajero abre la app y ve el Día 3 completo.

❌ **Contraejemplo:** Con F-14 activa, la app sin conexión muestra "No se pudo cargar tu itinerario".

---

### 2.11 Datos y privacidad

---

**RN-095** | Categoría: Datos y privacidad | Prioridad: MUST | Fuente: Constitución PRINCIPIO-004 | Funcionalidad: F-02

Patrón EARS: Unwanted

SI la solicitud de viaje incluye un campo no definido en el esquema de entrada, ENTONCES el backend DEBE rechazar la solicitud.

✅ **Ejemplo:** Payload con `"child_name": "Mateo"` → HTTP 422.

❌ **Contraejemplo:** El backend ignora `child_name` en la validación y lo reenvía al LLM.

---

**RN-096** | Categoría: Datos y privacidad | Prioridad: MUST | Fuente: Constitución PRINCIPIO-004; Alcance RES-08, RES-09 | Funcionalidad: F-03, F-05

Patrón EARS: Ubiquitous

El backend DEBE enviar al proveedor de IA solo los campos de la lista permitida: destino (la entrada resuelta según RN-007, nunca el texto escrito por el viajero), fechas, tipo de grupo, cantidad de adultos, rangos de edad de los menores, necesidades del grupo y ritmo.

✅ **Ejemplo:** Payload al LLM: `{ "destination": "Cusco", "start_date": "2026-12-18", "end_date": "2026-12-21", "group_type": "family", "adults": 2, "children_age_ranges": ["3-5"], "group_needs": ["stroller"], "pace": "balanced" }`.

❌ **Contraejemplo:** El payload al LLM incluye la IP del dispositivo o un `user_id`, o el campo `destination` lleva el texto escrito "Cusco con mi hijo Mateo".

---

**RN-097** | Categoría: Datos y privacidad | Prioridad: MUST | Fuente: Constitución PRINCIPIO-005 | Funcionalidad: F-01, F-03

Patrón EARS: Ubiquitous

Los logs del sistema NO DEBEN contener el payload del viaje.

✅ **Ejemplo:** Log: `{"request_id": "a1b2", "duration_ms": 8420, "status": 200}`.

❌ **Contraejemplo:** Log: `{"destination": "Cusco", "children": ["3-5"]}`.

---

**RN-098** | Categoría: Datos y privacidad | Prioridad: MUST | Fuente: Constitución PRINCIPIO-006 | Funcionalidad: F-01, F-10

Patrón EARS: Ubiquitous

El almacenamiento local del dispositivo NO DEBE contener tokens de sesión.

✅ **Ejemplo:** El token web está en una cookie `HttpOnly; Secure; SameSite`; en mobile, en el almacén seguro del sistema operativo (la librería se fija en el ADR de stack, D-07).

❌ **Contraejemplo:** `localStorage.setItem('session_token', 'eyJ…')`.

---

**RN-099** | Categoría: Datos y privacidad | Prioridad: MUST | Fuente: Constitución PRINCIPIO-005; Alcance RES-09 | Funcionalidad: F-03, F-05

Patrón EARS: Ubiquitous

El backend NO DEBE enviar datos de viaje a un proveedor de IA cuyas condiciones permitan usar esos datos para entrenar modelos.

✅ **Ejemplo:** El ADR de selección documenta que el proveedor tiene activada la opción de no entrenamiento.

❌ **Contraejemplo:** Se usa la capa gratuita de un proveedor cuyas condiciones permiten entrenar con los prompts.

---

### 2.12 UX

---

**RN-100** | Categoría: UX | Prioridad: MUST | Fuente: Constitución PRINCIPIO-003, PRINCIPIO-011 | Funcionalidad: F-06

Patrón EARS: Ubiquitous

Los textos de los estados de confiabilidad DEBEN provenir de claves de traducción.

✅ **Ejemplo:** `t('reliability.estimated.label')` → "≈ Estimada".

❌ **Contraejemplo:** `<Text>≈ Estimada</Text>` escrito directamente en el componente.

---

**RN-101** | Categoría: UX | Prioridad: MUST | Fuente: BA (propuesta, a confirmar por PO) — PAR-11 | Funcionalidad: F-03, F-05

Patrón EARS: Event-driven

CUANDO una operación de IA lleve más de 3 segundos en curso, el sistema DEBE mostrar un indicador de progreso.

✅ **Ejemplo:** A los 3 s de pulsar "Generar itinerario" aparece "Armando tu itinerario día por día…".

❌ **Contraejemplo:** La pantalla queda sin cambios durante 40 s.

---

## Anexo A. Parámetros propuestos por el BA (a confirmar por el PO)

| ID | Parámetro | Valor propuesto | Reglas | Justificación |
|---|---|---|---|---|
| PAR-01 | Duración máxima del viaje | 14 días | RN-005 | Cubre feriados largos y vacaciones (Brief §2.5); acota tokens por generación (Alcance RES-05). |
| PAR-02 | Anticipación máxima de la fecha de inicio | 365 días | RN-006 | Los datos verificados vencen en 7 días; planificar a más de un año aporta poco al piloto. |
| PAR-03 | Viajeros máximos por solicitud | 10 | RN-010 | Cubre familias extendidas sin abrir el caso de grupos grandes. |
| PAR-04 | Franjas horarias | Mañana 06:00–12:00 · Tarde 12:00–18:00 · Noche 18:00–23:00 | RN-018 | Hace verificable el orden de F-04. |
| PAR-05 | Carga diaria máxima por ritmo | Relajado 360 min · Equilibrado 480 min · Activo 600 min | RN-027 a RN-029, RN-036, RN-044 | Traduce el ritmo a un valor medible. |
| PAR-06 | Bloque de descanso con menores de 0–5 años | ≥ 1 bloque de ≥ 60 min por día | RN-033 | Brief §2.4: siestas y pausas frecuentes. Es orientativo: el viajero lo puede eliminar (RN-038). |
| PAR-07 | Límite de operaciones de IA | 15 exitosas por dispositivo cada 24 h | RN-043, RN-089 | 1 generación + ~14 ajustes. Debe contrastarse con la medición de SUP-03. |
| PAR-08 | Itinerarios locales con F-11 | 5 | RN-077 | Suficiente para comparar destinos sin presionar el almacenamiento local (SUP-04). |
| PAR-09 | Tiempo máximo de espera | Generación completa 60 s · Alternativa o regenerar día 30 s | RN-083, RN-084 | Debe ajustarse tras medir latencias reales del proveedor elegido. |
| PAR-10 | Reintentos automáticos ante respuesta inválida | 1 | RN-085 | Equilibra calidad y consumo de la capa gratuita. |
| PAR-11 | Umbral para mostrar progreso | 3 s | RN-101 | — |

## Anexo B. Pendientes y tensiones detectadas

| # | Tema | Detalle | Acción sugerida |
|---|---|---|---|
| 1 | Nombres del ritmo | 02-alcance F-02 dice "relajado, moderado, intenso"; se decidió usar Relajado, Equilibrado y Activo. | **Resuelto el 2026-11-07:** 02-alcance F-02 actualizado. |
| 2 | Rangos de edad | El Brief §2.4 usa 0–2, 3–6, 7–12 y adolescentes; la Constitución PRINCIPIO-004 fija 0–2, 3–5, 6–11 y 12–17. Este catálogo usa la Constitución. | **Resuelto el 2026-11-07:** Brief §2.4 alineado con la Constitución (v1.1). |
| 3 | Tipo de grupo "Amigos" | El Brief §2.3 y §7 lo incluyen; 02-alcance F-02 solo lista Solo, Pareja y Familia. RN-008 sigue al Alcance. | **Resuelto el 2026-11-07:** "Amigos" (2–10 adultos, 0 menores) se agrega a F-02, RN-008, RN-011 y al glosario. |
| 4 | Almacenamiento local de rangos de edad | PRINCIPIO-005 manda guardar los itinerarios de invitados en IndexedDB; PRINCIPIO-006 prohíbe guardar datos personales en IndexedDB; Alcance RES-08 considera las edades de menores dato personal. | **Resuelto (AL-29, Checkpoint 1):** PRINCIPIO-006 (Constitución 2.0.0) prohíbe en el almacenamiento local credenciales, tokens y datos identificativos; el itinerario del invitado con rangos de edad sin identificadores sí se permite. Ver PREG-11 (Resuelta) y ADR-004. |
| 5 | Vigencia sin conexión (F-14) | Aplicar RN-068 y RN-069 sin backend implica calcular el estado en el cliente, lo que roza el PRINCIPIO-009. | Afecta ya al MVP: RN-107 obliga a recalcular el estado al reabrir un itinerario guardado (F-01). El lugar del cálculo pasa a 05-preguntas-abiertas PREG-07. |
| 6 | "Incluso durante el viaje" (F-01) | En el MVP la consulta durante el viaje requiere conexión, porque la consulta sin conexión (F-14) es Could. | **Resuelto el 2026-11-07:** F-01 reescrito ("con conexión a internet"); F-14 se mantiene como Could porque el piloto se valida antes del viaje (Brief R9). |
| 7 | Parámetros PAR-01 a PAR-11 | Son propuestas del BA. | Aprobación del PO; PAR-07 y PAR-09, además, tras medir SUP-03. |
| 8 | Auditoría QA del 2026-11-07 | 59 hallazgos: 17 corregidos, 14 corregidos en parte (Anexo D) y 40 preguntas abiertas. | Resolver PREG-01 a PREG-19 de 05-preguntas-abiertas.md antes de aprobar specs del MVP. |

## Anexo C. Trazabilidad funcionalidad → reglas

| Funcionalidad | Prioridad | Reglas |
|---|---|---|
| F-01 Modo invitado con conservación local | M | RN-072 a RN-076, RN-078, RN-091, RN-092, RN-097, RN-098, RN-107 |
| F-02 Perfil del viaje y del grupo | M | RN-001 a RN-016, RN-036, RN-037, RN-088, RN-095 |
| F-03 Generación de itinerario con IA adaptado | M | RN-017, RN-021 a RN-025, RN-027 a RN-037, RN-043, RN-055 a RN-059, RN-083, RN-085, RN-086, RN-088 a RN-090, RN-093, RN-096, RN-097, RN-099, RN-101 a RN-105, RN-109 a RN-111 |
| F-04 Vista del itinerario por días | M | RN-018 a RN-020, RN-026, RN-060 |
| F-05 Ajuste esencial del itinerario | M | RN-035, RN-038 a RN-043, RN-084 a RN-087, RN-089, RN-090, RN-092, RN-093, RN-096, RN-099, RN-101, RN-108, RN-109 |
| F-06 Estado de confiabilidad de los datos | M | RN-020, RN-032, RN-055 a RN-071 (RN-069 es WON'T (v1), QB-06), RN-100, RN-102, RN-103, RN-105 a RN-107 |
| F-07 Estimación de presupuesto | S | RN-046 a RN-054 |
| F-08 Recomendaciones de transporte y alimentación | S | Sin reglas propias: se especifican con su spec (ver PREG-15). |
| F-09 Edición completa de actividades | S | RN-044, RN-045 |
| F-10 Cuenta opcional y viajes persistentes | S | RN-079 a RN-082, RN-098 |
| F-11 Varios itinerarios locales | S | RN-077 |
| F-14 Consulta sin conexión | C | RN-094 |

## Anexo D. Cambios por la auditoría QA (2026-11-07)

Ningún cambio introduce una regla de negocio nueva: cada regla agregada aplica una decisión que ya existía en la Constitución, el Alcance o el Brief. Lo que requiere decisión del equipo está en 05-preguntas-abiertas.md.

| Hallazgo QA | Cambio |
|---|---|
| C-01 | Nuevas RN-102 (texto libre sin datos factuales), RN-103 (clima) y RN-104 (seguridad del destino). |
| C-03 | Nueva RN-105 (lugar no contrastado → ! Por confirmar, según el plan B de Alcance DEP-02). |
| C-04 | Nueva RN-106 (`verified` exige una fuente del registro de fuentes). |
| C-08 | Nueva RN-107 (recalcular el estado al abrir un itinerario guardado). Anexo B #5 actualizado. |
| C-09 | Nueva RN-108 (sin opción válida → informar sin modificar). RN-007: ejemplo de destino sin resultados. |
| C-10 | Nuevas RN-109 (sin estado por defecto) y RN-110 (días incompletos). Notas cruzadas en RN-022. |
| A-08 | RN-035, RN-038, RN-045, RN-077 y RN-094: "PUEDE" → "el sistema DEBE permitir". RN-086 conserva PUEDE porque es opcional. |
| A-09 | RN-055 a RN-058 pasan a Unwanted con el backend como sujeto; RN-059 con el backend como sujeto. |
| A-12 | Nueva RN-111 (lugar cerrado en la fecha programada). |
| A-14 | RN-036 pasa a State-driven: el aviso no depende del orden en que se llenan los campos. |
| A-20 | RN-096: al LLM solo se envía el destino resuelto, nunca el texto escrito. |
| A-21 | Ejemplos de RN-020, RN-023, RN-031, RN-049, RN-052, RN-054, RN-057, RN-058, RN-061 y RN-070 con el texto obligatorio de PRINCIPIO-003. Convención en §0.4. |
| M-01 | Nota en RN-031 sobre la edad mínima igual o menor que el límite inferior. |
| M-08 | RN-046 pasa a Unwanted y RN-047 a Event-driven (el patrón Optional se reserva para funcionalidades). |
| M-11 | RN-024 pasa a State-driven y fija el aviso en la vista del itinerario. *(Checkpoint 1, AL-24: pasa a Ubiquitous; el aviso se muestra en todo itinerario.)* |
| B-01 | Encabezado: Brief v1.1. |
| B-02 | RN-080 y RN-081: "usuario" → "usuario registrado". |
| B-07 | Anexo C completado (RN-020, RN-096, RN-097, RN-099, RN-101, reglas nuevas, F-08). |
| B-08 | Notas cruzadas RN-071 ↔ RN-061/RN-067 y RN-022 ↔ RN-085. |
| B-10 | RN-082 y RN-054: fechas en pantalla en dd/mm/aaaa. |
| B-11 | RN-008, RN-012 y RN-016 pasan a Unwanted con respuesta del sistema. |
| B-12 | Convención de atomicidad en §0.2. |
| M-09 | F-06 renombrada "Estado de confiabilidad" en el Anexo C. |
| QB-03 (auditoría Clase 2, vuelta 1) | RN-064 y RN-106: semántica única de "rechazar" un `verified` por origen: (a) salida del LLM → inválida por esquema, reintento RN-085; (b) entrada mal formada o campo no declarado → 422; (c) atestación inválida o fuente no registrada sin atestación válida → 422; (d) atestación válida con fuente retirada o vigencia vencida → `to_confirm` (`source_removed` / `expired`). El enunciado de RN-106 precisa "ni lo respalda una atestación válida". Sin regla nueva. |
| QB-06 (auditoría Clase 2, vuelta 1) | RN-069: Prioridad MUST → **WON'T (v1)** por la decisión vigente D-06; se reactiva si una versión posterior muestra requisitos. Anexo C (F-06) anotado. 06 § Reglas sin HU actualizado. |
