# 06 — Historias de Usuario · AI Travel Planner

> **Fase BMAD:** Planificación · **Agente:** PO
> **Insumos:** 00-constitucion.md, 01-project-brief.md (v1.2), 02-alcance.md, 03-reglas-de-negocio.md
> **Estado:** Borrador · **Fecha:** 2026-11-07
> **País de operación:** Perú (mercado inicial Perú y Latinoamérica; cualquier destino)
> Enriquecido por Agente QA Funcional 2026-11-07
> Triaje de preguntas a 05-preguntas-abiertas.md: 2026-11-07 (marcadores remitidos a PREG-xx o resueltos con 02, 03 o 04)
> **Revisión 2026-11-07: hallazgos auditoría Clase 2 vuelta 1:** QB-04 (criterio de logs de HU-001 = lista blanca de C-14), QB-06 (RN-069 WON'T (v1) en § Reglas sin HU y en HU-008) y QB-32 (nota de F-NN cruzadas en §0).

---

## 0. Convenciones

- **Prioridad:** coincide con la MoSCoW de la F-NN en 02-alcance.md (F-01 a F-06 = Must; F-10 = Should).
- **Actores** (términos de 03): **viajero** (quien planifica, con o sin cuenta), **invitado** (viajero sin cuenta) y **usuario registrado** (viajero con cuenta opcional, F-10).
- **Plataformas:** por la decisión vigente D-10 (2026-11-07), la v1 se implementa solo como web responsive: "web responsive" = la misma web Next.js en navegador móvil (360–430 px) y en escritorio (≥ 1024 px). La app mobile con Expo es post-v1. Donde un criterio dice "web y mobile", en v1 se lee "viewport de escritorio y viewport móvil de la web"; por el PRINCIPIO-012, todo criterio se cumple igual en ambos viewports.
- **Valores:** los PAR-NN del Anexo A de 03 son propuestas del BA, aún sin aprobar por el PO (PREG-55). Las HUs los usan tal cual. Si cambian, se actualizan los criterios.
- **Textos de estado de confiabilidad:** siempre los textos exactos del PRINCIPIO-003, guardados como claves de i18n (RN-100).
- **Ninguna HU Must exige cuenta** (RN-072). La única HU que usa una cuenta es HU-012 (Should).
- **RN de otra F-NN (QB-32):** algunas HU cubren reglas cuya F-NN en 03 es distinta de la suya: RN-091 y RN-092 (F-01) en HU-006 (F-03); RN-023, RN-025 y RN-104 (F-03) en HU-009 (F-06); RN-024 (F-03) en HU-007 (F-04). Todas son Must, así que la prioridad no cambia.
- **Criterios (QA):** cada criterio lleva su tipo, (positivo), (negativo) o (borde). Los criterios agregados por el Agente QA Funcional llevan [NUEVO].
- Un valor que no figura en los insumos se marca como `[valor pendiente: PREG-xx]`, que remite a 05-preguntas-abiertas.md. Las preguntas que el PO y el QA plantearon en este documento se trasladaron a 05 en el triaje del 2026-11-07; la sección 4 y "§ Preguntas del QA" conservan su trazabilidad.

---

## 1. Índice

| HU | Título | Épica | F-NN | Prioridad | Plataformas | RN |
|---|---|---|---|---|---|---|
| HU-001 | Planificar como invitado y reabrir el itinerario guardado en el dispositivo | EP-01 Modo invitado | F-01 | Must | web responsive | RN-072, RN-073, RN-074, RN-075, RN-076, RN-078, RN-097, RN-098 |
| HU-002 | Indicar destino y fechas válidas en la solicitud de viaje | EP-02 Solicitud y perfil del grupo | F-02 | Must | web responsive | RN-001 a RN-007 |
| HU-003 | Describir el grupo (con menores por rango de edad), sus necesidades y el ritmo | EP-02 Solicitud y perfil del grupo | F-02 | Must | web responsive | RN-008 a RN-016, RN-036, RN-037, RN-095 |
| HU-004 | Generar un itinerario día por día que respete el ritmo elegido | EP-03 Generación de itinerario | F-03 | Must | web responsive | RN-017, RN-021, RN-022, RN-027, RN-028, RN-029, RN-085, RN-096, RN-099, RN-109, RN-110, RN-111 |
| HU-005 | Adaptar el itinerario a los rangos de edad de los menores | EP-03 Generación de itinerario | F-03 | Must | web responsive | RN-030 a RN-035 |
| HU-006 | Recibir respuesta clara ante esperas, fallas, límite de operaciones de IA y pérdida de conexión | EP-03 Generación de itinerario | F-03 | Must | web responsive | RN-043, RN-083, RN-086, RN-088 a RN-093, RN-101 |
| HU-007 | Ver el itinerario por días y franjas, con el aviso de sugerencia y el aviso migratorio | EP-04 Vista del itinerario | F-04 | Must | web responsive | RN-018, RN-019, RN-020, RN-024, RN-026, RN-060 |
| HU-008 | Ver cada dato factual con su estado de confiabilidad vigente y los textos exactos | EP-05 Confiabilidad de datos | F-06 | Must | web responsive | RN-061, RN-062, RN-065 a RN-068, RN-070, RN-071, RN-100, RN-107 |
| HU-009 | Recibir del backend solo contenido respaldado: sin Verificada sin fuente ni afirmaciones prohibidas | EP-05 Confiabilidad de datos | F-06 | Must | web responsive | RN-023, RN-025, RN-055 a RN-059, RN-063, RN-064, RN-102 a RN-106 |
| HU-010 | Eliminar una actividad o pedir una alternativa | EP-06 Ajuste esencial | F-05 | Must | web responsive | RN-035, RN-038, RN-039, RN-040, RN-042, RN-084, RN-108 |
| HU-011 | Regenerar un día completo sin tocar los demás días | EP-06 Ajuste esencial | F-05 | Must | web responsive | RN-041, RN-042, RN-084, RN-087, RN-092, RN-108 |
| HU-012 | Crear una cuenta opcional y asociar a ella mis viajes locales | EP-07 Cuenta opcional | F-10 | Should | web responsive | RN-079, RN-080, RN-081, RN-082, RN-098 |

**Conteo:** 11 Must · 1 Should · 0 Could · 0 Won't.

---

## 2. Historias de usuario por épica

### EP-01 — Modo invitado (F-01)

#### HU-001: Planificar como invitado y reabrir el itinerario guardado en el dispositivo

**Prioridad:** Must
**Funcionalidad:** F-01
**Plataformas:** web responsive (móvil 360–430 px y escritorio ≥ 1024 px)

Como **invitado**, quiero generar, consultar y ajustar mi itinerario sin crear una cuenta y encontrarlo guardado en mi dispositivo al volver, para ver el valor del producto antes de identificarme y no tener que volver a cargar mi solicitud de viaje.

**Criterios de aceptación:**
- (positivo) DADO QUE soy un invitado sin cuenta, CUANDO genero un itinerario, elimino una actividad y regenero el Día 2, ENTONCES ninguna de esas acciones muestra un pedido de registro ni de inicio de sesión (texto prohibido de ejemplo: "Crea una cuenta para continuar").
- (positivo) DADO QUE se generó o ajustó mi itinerario, CUANDO recargo la página (web) o cierro y abro la app (mobile), ENTONCES la última versión del itinerario se lee del almacenamiento local del dispositivo.
- (positivo) DADO QUE es la primera vez que se guarda un itinerario en mi dispositivo, CUANDO termina de guardarse, ENTONCES se muestra el aviso "Tu itinerario se guardó en este dispositivo. Si borras los datos del navegador, lo perderás."
- (positivo) DADO QUE tengo guardado un itinerario a Cusco, CUANDO pido generar uno a Arequipa, ENTONCES se muestra "Esto reemplazará tu itinerario a Cusco. ¿Continuar?" y el itinerario a Cusco no cambia hasta que confirmo.
- (positivo) DADO QUE tengo un itinerario guardado del 2026-12-18 al 2026-12-21, CUANDO abro la app con conexión, ENTONCES se muestra ese itinerario directamente, sin abrir el formulario de solicitud de viaje.
- (negativo) DADO QUE un invitado genera un itinerario a Cusco, CUANDO el test de integración revisa la base de datos y los logs, ENTONCES no hay filas nuevas y cada línea de log contiene solo claves de la lista blanca de C-14 (07 §2.1: `request_id`, `route`, `status`, `duration_ms`; `provider` y `model_version` solo si se aprueba PREG-66), sin campos del payload del viaje. `[QA: compatibilidad con el registro de proveedor y versión del modelo exigido por PRINCIPIO-009: PREG-66]`
- (negativo) DADO QUE se inspecciona el almacenamiento local del dispositivo, CUANDO se busca un token de sesión, ENTONCES no se encuentra ninguno: el almacenamiento local guarda solo el itinerario, incluidos los rangos de edad de los menores sin identificadores (PRINCIPIO-006).
- [NUEVO] (negativo) DADO QUE tengo guardado un itinerario a Cusco y se muestra "Esto reemplazará tu itinerario a Cusco. ¿Continuar?", CUANDO cancelo, ENTONCES no se envía ninguna operación de IA, el itinerario a Cusco sigue intacto en el dispositivo y no se descuenta ninguna operación del límite de 15 (PAR-07).
- [NUEVO] (negativo) DADO QUE confirmé reemplazar el itinerario a Cusco por uno a Arequipa, CUANDO se pierde la conexión durante la generación, ENTONCES el itinerario a Cusco sigue guardado sin cambios en el dispositivo (RN-092). `[comportamiento si la generación falla por el servicio de IA y no por la conexión: PREG-62]`
- [NUEVO] (negativo) DADO QUE el almacenamiento local no está disponible o está lleno (p. ej., navegación privada o cuota de SUP-04 agotada), CUANDO termina de generarse el itinerario, ENTONCES `[comportamiento y texto pendientes: PREG-63; umbral de SUP-04: PREG-56]`.
- [NUEVO] (borde) DADO QUE ya se mostró el aviso del primer guardado, CUANDO se guarda un ajuste posterior (p. ej., elimino una actividad del Día 2), ENTONCES el aviso "Tu itinerario se guardó en este dispositivo. Si borras los datos del navegador, lo perderás." no vuelve a mostrarse (RN-075: solo la primera vez).
- [NUEVO] (borde) DADO QUE el almacenamiento local está vacío (primera visita o datos del navegador borrados), CUANDO abro la app con conexión, ENTONCES se muestra el formulario de solicitud de viaje sin mensajes de error.

**Reglas de negocio relacionadas:** RN-072, RN-073, RN-074, RN-075, RN-076, RN-078, RN-097, RN-098

**Notas:**
- En el MVP se guarda **un solo** itinerario activo. Conservar hasta 5 es F-11 (Should, PAR-08), fuera de esta HU.
- La consulta **sin conexión** es F-14 (Could, RN-094) y no es parte de esta HU: aquí la reapertura requiere conexión (Anexo B #6 de 03).
- Al reabrir el itinerario, los estados de confiabilidad se recalculan según su vigencia (RN-107): ver HU-008.
- La tecnología de almacenamiento local se fija en el ADR de stack (D-07). La validación legal de guardar los rangos de edad en el dispositivo sigue recomendada antes del lanzamiento público (PRINCIPIO-006, AL-29).

---

### EP-02 — Solicitud y perfil del grupo (F-02)

#### HU-002: Indicar destino y fechas válidas en la solicitud de viaje

**Prioridad:** Must
**Funcionalidad:** F-02
**Plataformas:** web responsive (móvil 360–430 px y escritorio ≥ 1024 px)

Como **viajero**, quiero indicar mi destino y las fechas de inicio y fin del viaje y saber de inmediato si algo no es válido, para que el itinerario se genere sobre un destino y unas fechas reales.

**Criterios de aceptación:**
- (negativo) DADO QUE el campo Destino está vacío, CUANDO pulso "Generar itinerario", ENTONCES no se genera nada y junto al campo Destino aparece "Indica un destino para continuar".
- (negativo) DADO QUE escribo el destino "San Juan", CUANDO la fuente de lugares devuelve más de un resultado, ENTONCES se muestran las opciones (p. ej., "San Juan de Lurigancho (Lima, Perú)" y "San Juan (Puerto Rico)") y la generación espera a que elija una.
- (negativo) DADO QUE escribo el destino "Qwzxy", CUANDO la fuente de lugares no devuelve resultados, ENTONCES se muestra "No encontramos ese destino. Revisa cómo está escrito." y no se genera.
- (negativo) DADO QUE la fecha de fin es 2027-02-30, CUANDO envío la solicitud, ENTONCES se rechaza con "La fecha 2027-02-30 no existe".
- (negativo) DADO QUE el inicio es 2026-12-21 y el fin es 2026-12-18, CUANDO envío la solicitud, ENTONCES se rechaza con "La fecha de fin debe ser igual o posterior a la fecha de inicio".
- (borde) DADO QUE la fecha actual en America/Lima es 2026-11-07, CUANDO envío inicio 2026-11-05, ENTONCES se rechaza; y CUANDO envío inicio 2026-11-07, ENTONCES se acepta. `[texto de rechazo pendiente: PREG-57]`
- (borde) DADO QUE elijo del 2027-01-10 al 2027-01-25 (16 días), CUANDO envío la solicitud, ENTONCES se rechaza con "El máximo es 14 días por itinerario" (PAR-01); y del 2027-01-10 al 2027-01-23 (14 días) se acepta.
- (negativo) DADO QUE la fecha actual es 2026-11-07, CUANDO envío inicio 2027-11-10 (368 días después), ENTONCES se rechaza con "Puedes planificar viajes que empiecen hasta el 2027-11-07" (PAR-02).
- (negativo) DADO QUE envío directamente a la API una solicitud con fechas inválidas, sin pasar por el cliente, CUANDO el backend la valida, ENTONCES la rechaza con las mismas reglas (PRINCIPIO-009).
- [NUEVO] (positivo) DADO QUE la fecha actual en America/Lima es 2026-11-07, CUANDO escribo "Cusco", la fuente de lugares lo resuelve a un único lugar y elijo del 2026-12-18 al 2026-12-21, ENTONCES la solicitud se acepta y pasa a la generación con el destino resuelto (RN-007, RN-096).
- [NUEVO] (borde) DADO QUE elijo inicio y fin el mismo día, 2026-12-18, CUANDO envío la solicitud, ENTONCES se acepta como viaje de 1 día (RN-003: fin igual o posterior al inicio).
- [NUEVO] (borde) DADO QUE elijo del 2027-01-10 al 2027-01-24 (15 días), CUANDO envío la solicitud, ENTONCES se rechaza con "El máximo es 14 días por itinerario" (PAR-01).
- [NUEVO] (borde) DADO QUE la fecha actual es 2026-11-07, CUANDO envío inicio 2027-11-07 (365 días después), ENTONCES se acepta; y CUANDO envío inicio 2027-11-08 (366 días), ENTONCES se rechaza con "Puedes planificar viajes que empiecen hasta el 2027-11-07" (PAR-02).
- [NUEVO] (borde) DADO QUE la fecha de fin es 2027-02-29 (2027 no es bisiesto), CUANDO envío la solicitud, ENTONCES se rechaza con "La fecha 2027-02-29 no existe" (RN-002).
- [NUEVO] (borde) DADO QUE son las 23:30 del 2026-11-07 en America/Lima (04:30 del 2026-11-08 en UTC), CUANDO envío inicio 2026-11-07, ENTONCES se acepta. `[zona horaria de referencia: PREG-23, PREG-32; momento en que se toma la fecha actual: PREG-67]`
- [NUEVO] (negativo) DADO QUE la fuente de lugares no responde o devuelve un error, CUANDO pulso "Generar itinerario", ENTONCES no se genera nada, el texto escrito no se envía al LLM (RN-096) y se muestra `[texto y plazo pendientes: PREG-64]`.

**Reglas de negocio relacionadas:** RN-001, RN-002, RN-003, RN-004, RN-005, RN-006, RN-007

**Notas:**
- Se acepta cualquier destino (nacional o internacional). El producto no pide país de residencia ni nacionalidad (AL-24).
- Para el LLM, el destino es la entrada resuelta de la fuente de lugares, nunca el texto que escribió el viajero (RN-096, HU-004).
- La zona horaria de referencia (America/Lima) sigue abierta en PREG-23 y PREG-32.
- RN-002 a RN-006 son propuestas del BA (PREG-55).

---

#### HU-003: Describir el grupo (con menores por rango de edad), sus necesidades y el ritmo

**Prioridad:** Must
**Funcionalidad:** F-02
**Plataformas:** web responsive (móvil 360–430 px y escritorio ≥ 1024 px)

Como **viajero**, quiero indicar el tipo de grupo, cuántos adultos y menores viajan, el rango de edad de cada menor, las necesidades del grupo y el ritmo, para que el itinerario se adapte a mi grupo sin que tenga que dar datos identificativos de los menores.

**Criterios de aceptación:**
- (positivo) DADO QUE abro el formulario, CUANDO elijo el tipo de grupo, ENTONCES puedo elegir exactamente uno de Solo, Pareja, Amigos o Familia; y una solicitud a la API sin tipo de grupo o con dos tipos se rechaza. `[texto de rechazo pendiente: PREG-57]`
- (negativo) DADO QUE indico 0 adultos y 2 menores de 12–17 años, CUANDO envío la solicitud, ENTONCES se rechaza con "El grupo debe incluir al menos un adulto".
- (borde) DADO QUE indico 4 adultos y 7 menores (11 viajeros), CUANDO envío la solicitud, ENTONCES se rechaza indicando el máximo de 10 viajeros (PAR-03); y 4 adultos y 6 menores (10) se acepta. `[texto exacto pendiente: PREG-57]`
- (negativo) DADO QUE elijo el tipo Familia con 2 adultos y 0 menores, CUANDO envío la solicitud, ENTONCES se rechaza con "Agrega al menos un menor o cambia el tipo de grupo a Pareja o Amigos"; y el tipo Amigos con 2 adultos y 0 menores se acepta. (composición de cada tipo según 04-glosario: Solo = 1 adulto y 0 menores; Pareja = 2 adultos y 0 menores; Familia = ≥ 1 adulto y ≥ 1 menor) `[textos de los demás casos pendientes: PREG-57]`
- (positivo) DADO QUE agrego 2 menores, CUANDO completo sus datos, ENTONCES cada menor solo tiene un selector con exactamente las opciones "0–2 años", "3–5 años", "6–11 años" y "12–17 años", y no hay ningún campo de nombre, fecha de nacimiento, fotografía, documento, diagnóstico ni texto libre sobre el menor.
- (positivo) DADO QUE quiero indicar necesidades, CUANDO abro "Necesidades del grupo", ENTONCES solo puedo marcar opciones de un catálogo cerrado (cochecito, movilidad reducida, restricciones alimentarias, menor exigencia física, según el PRINCIPIO-004), asociadas al grupo y no a un menor concreto. `[catálogo definitivo pendiente: PREG-25]`
- (positivo) DADO QUE elijo el ritmo, CUANDO abro el selector, ENTONCES puedo elegir exactamente uno de Relajado, Equilibrado o Activo; y una solicitud a la API con "Moderado" o sin ritmo se rechaza.
- (positivo) DADO QUE el grupo incluye al menos un menor de 0–2 o de 3–5 años y el ritmo es Activo, en cualquier orden en que llene los campos, CUANDO ambos valores están elegidos, ENTONCES se muestra "Con ritmo Activo cada día puede sumar hasta 600 min de actividades y traslados. Con niños de 3–5 años quizá prefieras Equilibrado (480 min)." y el ritmo sigue en Activo hasta que yo lo cambie.
- (negativo) DADO QUE envío a la API un payload con `"child_name": "Mateo"`, CUANDO el backend lo valida, ENTONCES responde HTTP 422 y no envía nada al proveedor de IA.
- [NUEVO] (positivo) DADO QUE elijo Familia, 2 adultos, 1 menor de 3–5 años, "Necesidades del grupo: ☑ cochecito" y ritmo Equilibrado, CUANDO envío la solicitud, ENTONCES se acepta y el payload solo contiene `group_type`, `adults`, `children_age_ranges: ["3-5"]`, `group_needs: ["stroller"]` y `pace`, sin ningún identificador del menor.
- [NUEVO] (borde) DADO QUE elijo el tipo Amigos, CUANDO indico 10 adultos y 0 menores, ENTONCES se acepta; y CUANDO indico 1 adulto, o 2 adultos y 1 menor, ENTONCES se rechaza (Amigos = 2–10 adultos y 0 menores, 03 Anexo B #3). `[textos de rechazo pendientes: PREG-57]`
- [NUEVO] (borde) DADO QUE no marco ninguna necesidad del grupo (lista vacía), CUANDO envío la solicitud, ENTONCES se acepta con `group_needs: []` (las necesidades son opcionales, PRINCIPIO-004).
- [NUEVO] (negativo) DADO QUE envío a la API un menor con rango `"3-6"`, CUANDO el backend valida, ENTONCES rechaza la solicitud (RN-012) y no envía nada al proveedor de IA. `[texto y código HTTP pendientes: PREG-57]`
- [NUEVO] (negativo) DADO QUE envío a la API una necesidad asociada a un menor (`"children": [{"age_range": "3-5", "needs": ["stroller"]}]`), CUANDO el backend valida, ENTONCES responde HTTP 422 por campo no definido (RN-015, RN-095).
- [NUEVO] (borde) DADO QUE se muestra el aviso de ritmo Activo con un menor de 3–5 años, CUANDO cambio el ritmo a Equilibrado o quito a ese menor, ENTONCES el aviso deja de mostrarse (RN-036 es State-driven).
- [NUEVO] (borde) DADO QUE el grupo solo tiene menores de 6–11 y 12–17 años, CUANDO elijo ritmo Activo, ENTONCES no se muestra el aviso de RN-036.

**Reglas de negocio relacionadas:** RN-008, RN-009, RN-010, RN-011, RN-012, RN-013, RN-014, RN-015, RN-016, RN-036, RN-037, RN-095

**Notas:**
- Los rangos de edad y la prohibición de datos identificativos vienen del PRINCIPIO-004 y de la Ley N.° 29733 (RES-08). El filtro que construye el contexto del LLM exige 100 % de cobertura (PRINCIPIO-012).
- En el MVP no se pide presupuesto (F-07 es Should).
- El texto de RN-036 menciona "3–5 años". Falta definir si cambia a "0–2 años" cuando el menor es de ese rango (PREG-26).
- RN-009, RN-010 y RN-011 son propuestas del BA (PREG-55).

---

### EP-03 — Generación de itinerario (F-03)

#### HU-004: Generar un itinerario día por día que respete el ritmo elegido

**Prioridad:** Must
**Funcionalidad:** F-03
**Plataformas:** web responsive (móvil 360–430 px y escritorio ≥ 1024 px)

Como **viajero**, quiero que la IA genere un itinerario con un día por cada fecha de mi viaje y con una carga diaria acorde a mi ritmo, para obtener un plan realista que no me obligue a correr.

**Criterios de aceptación:**
- (positivo) DADO QUE la solicitud va del 2026-12-18 al 2026-12-21, CUANDO se genera el itinerario, ENTONCES tiene exactamente 4 días: Día 1 (2026-12-18), Día 2 (2026-12-19), Día 3 (2026-12-20) y Día 4 (2026-12-21).
- (negativo) DADO QUE el LLM devuelve 3 días para una solicitud de 4, CUANDO el backend valida la respuesta, ENTONCES no la entrega al cliente y reintenta la operación 1 vez (PAR-10).
- (positivo) DADO QUE el ritmo es Relajado, Equilibrado o Activo, CUANDO se genera el itinerario, ENTONCES la carga diaria (actividades + traslados) de cada día es ≤ 360, ≤ 480 o ≤ 600 minutos, respectivamente (PAR-05).
- (negativo) DADO QUE una fuente registrada marca un lugar como cerrado permanentemente, CUANDO se genera el itinerario, ENTONCES ese lugar no aparece en ningún día.
- (negativo) DADO QUE el horario ✓ Verificada de un museo indica cierre los lunes y el Día 3 es lunes, CUANDO se genera el itinerario, ENTONCES el museo no está programado el Día 3.
- (negativo) DADO QUE la respuesta del LLM no cumple el esquema (p. ej., una actividad con precio sin `reliability_status`), CUANDO el backend la valida, ENTONCES no la entrega, no completa el estado por defecto y reintenta 1 vez; si el reintento es válido, el viajero recibe el itinerario. `[texto de error tras el reintento fallido pendiente: PREG-58]`
- (negativo) DADO QUE el backend llama al proveedor de IA, CUANDO un test inspecciona el payload, ENTONCES solo contiene `destination` (resuelto), `start_date`, `end_date`, `group_type`, `adults`, `children_age_ranges`, `group_needs` y `pace`, sin IP, `user_id` ni texto escrito por el viajero.
- (positivo) DADO QUE el ADR de selección del proveedor de IA está aprobado, CUANDO se revisa, ENTONCES documenta que el proveedor tiene activada la opción de no entrenamiento con los datos enviados.
- [NUEVO] (borde) DADO QUE la solicitud va del 2026-12-18 al 2026-12-18, CUANDO se genera el itinerario, ENTONCES tiene exactamente 1 día; y para 2027-01-10 a 2027-01-23 (14 días, PAR-01) tiene exactamente 14 días, del Día 1 (2027-01-10) al Día 14 (2027-01-23).
- [NUEVO] (borde) DADO QUE el ritmo es Relajado, CUANDO un día suma exactamente 360 minutos de actividades y traslados, ENTONCES cumple RN-027; y un día de 361 minutos lo incumple. `[tratamiento de una respuesta válida con carga excedida: PREG-09]`
- [NUEVO] (negativo) DADO QUE el LLM devuelve 5 días para una solicitud de 4, CUANDO el backend valida la respuesta, ENTONCES no la entrega y reintenta 1 vez (RN-110: exactamente un día por fecha; PAR-10).
- [NUEVO] (negativo) DADO QUE la primera respuesta y el reintento del LLM no cumplen el esquema, CUANDO termina el reintento, ENTONCES el backend no entrega ningún itinerario, no hace un segundo reintento y la operación no cuenta para el límite de 15 (RN-085, RN-089). `[texto de error pendiente: PREG-58]`
- [NUEVO] (negativo) DADO QUE el viajero escribió "Cusco con mi hijo Mateo" y eligió la entrada resuelta "Cusco", CUANDO un test inspecciona el payload al proveedor de IA, ENTONCES `destination` es "Cusco" y no contiene "Mateo" (RN-096).
- [NUEVO] (positivo) DADO QUE el ADR de selección del proveedor de IA está aprobado, CUANDO se revisa, ENTONCES documenta también la opción de no retención de los datos enviados (PRINCIPIO-005, "Cómo se verifica").

**Reglas de negocio relacionadas:** RN-017, RN-021, RN-022, RN-027, RN-028, RN-029, RN-085, RN-096, RN-099, RN-109, RN-110, RN-111

**Notas:**
- Depende del contrato de la solicitud de HU-002 y HU-003. Para que sea independiente, se puede desarrollar con fixtures del contrato OpenAPI.
- El LLM se llama a través de la capa de abstracción, y cada generación registra el proveedor y la versión del modelo (PRINCIPIO-009).
- Si un PR toca prompts, el modelo o la capa de verificación, debe pasar el 100 % del golden set (PRINCIPIO-012).
- Otras respuestas válidas pero incompletas (carga excedida, salida truncada) quedan en PREG-09. A qué fecha se refiere la verificación del horario (consulta o viaje) queda en PREG-24.
- La viabilidad de un proveedor sin entrenamiento con USD 0 queda en PREG-13.

---

#### HU-005: Adaptar el itinerario a los rangos de edad de los menores

**Prioridad:** Must
**Funcionalidad:** F-03
**Plataformas:** web responsive (móvil 360–430 px y escritorio ≥ 1024 px)

Como **viajero** que viaja con menores, quiero que el itinerario excluya lo que una fuente registrada declara incompatible con todo el rango de edad, me informe de las restricciones dudosas y me deje decidir sobre las sugerencias, para no descubrir en destino que una actividad no es apta para mi grupo.

**Criterios de aceptación:**
- (negativo) DADO QUE el grupo tiene un menor de 6–11 años y el canopy tiene edad mínima 14 años ✓ Verificada, CUANDO se genera el itinerario, ENTONCES el canopy no aparece.
- (borde) DADO QUE el grupo tiene un menor de 6–11 años y un taller tiene edad mínima 8 años ✓ Verificada, CUANDO se genera el itinerario, ENTONCES el taller aparece con "Edad mínima: 8 años · ✓ Verificada — Fuente: {fuente} · Consultado el {dd/mm/aaaa} a las {HH:mm}" y el texto "Comprueba que cada menor la cumple."
- (negativo) DADO QUE la edad mínima de una actividad no tiene fuente registrada, CUANDO se muestra la actividad, ENTONCES se lee "Edad mínima: ! Por confirmar — No pudimos verificar este dato. Confírmalo con {fuente_sugerida} antes de tu viaje."
- (positivo) DADO QUE el grupo incluye al menos un menor de 0–2 o de 3–5 años, CUANDO se genera el itinerario, ENTONCES cada día tiene al menos un bloque de descanso de ≥ 60 minutos (PAR-06).
- (positivo) DADO QUE la IA sugiere que una caminata de 180 min puede ser exigente para un menor de 3–5 años, CUANDO se muestra la actividad, ENTONCES la sugerencia aparece como orientación ("Sugerencia: … Tú decides si la mantienes."), nunca como restricción del tipo "Prohibido para menores de 6 años".
- (positivo) DADO QUE una actividad tiene una orientación por rango de edad, CUANDO decido conservarla y recargo la app, ENTONCES la actividad sigue en el mismo día y en la misma franja horaria.
- [NUEVO] (borde) DADO QUE el grupo tiene un menor de 6–11 años, CUANDO una actividad tiene edad mínima 11 años ✓ Verificada, ENTONCES se incluye con su edad mínima y "Comprueba que cada menor la cumple." (RN-031: ≤ límite superior); y CUANDO tiene edad mínima 12 años ✓ Verificada, ENTONCES se excluye (RN-030).
- [NUEVO] (borde) DADO QUE el grupo tiene un menor de 6–11 años, CUANDO una actividad tiene edad mínima 6 años ✓ Verificada (igual al límite inferior), ENTONCES se incluye y la edad mínima se muestra con su estado (nota de RN-031, RN-061).
- [NUEVO] (borde) DADO QUE el grupo tiene un menor de 3–5 años y otro de 12–17 años, CUANDO una actividad tiene edad mínima 14 años ✓ Verificada, ENTONCES se excluye, porque supera el límite superior del rango de al menos un menor (RN-030).
- [NUEVO] (borde) DADO QUE el grupo incluye un menor de 0–2 años, CUANDO un día tiene un bloque de descanso de exactamente 60 minutos, ENTONCES cumple PAR-06; y si el único bloque dura 59 minutos, el día lo incumple.
- [NUEVO] (negativo) DADO QUE la edad mínima ✓ Verificada de una actividad se consultó el 2026-12-01 a las 10:00, CUANDO se muestra el 2026-12-08 a las 10:01, ENTONCES aparece como `! Por confirmar` (RN-068). `[si la exclusión de RN-030 sigue aplicando con la restricción vencida: PREG-28]`
- [NUEVO] (negativo) DADO QUE el LLM redacta una orientación por rango de edad como restricción (p. ej., "Prohibido para menores de 6 años") sin fuente registrada, CUANDO se ejecuta el golden set, ENTONCES el caso falla y el PR no se fusiona (RN-034, PRINCIPIO-012). `[mecanismo de detección en texto libre: PREG-05]`

**Reglas de negocio relacionadas:** RN-030, RN-031, RN-032, RN-033, RN-034, RN-035

**Notas:**
- Las reglas de adaptación familiar son orientativas: solo se excluye una actividad cuando una fuente registrada la declara incompatible con **todo** el rango (03 §2.4).
- El bloque de descanso es eliminable (RN-038, HU-010).
- Las restricciones de altura, edad máxima y salud, y las restricciones vencidas, quedan en PREG-28. Quién genera `{fuente_sugerida}` queda en PREG-06.
- El ritmo elegido nunca se cambia automáticamente (RN-037, HU-003).

---

#### HU-006: Recibir respuesta clara ante esperas, fallas, límite de operaciones de IA y pérdida de conexión

**Prioridad:** Must
**Funcionalidad:** F-03
**Plataformas:** web responsive (móvil 360–430 px y escritorio ≥ 1024 px)

Como **viajero**, quiero saber qué pasa cuando la generación tarda, falla, alcanzo el límite de operaciones de IA o me quedo sin conexión, para no perder lo que cargué ni quedarme esperando sin respuesta.

**Criterios de aceptación:**
- (positivo) DADO QUE pulsé "Generar itinerario", CUANDO pasan 3 segundos sin respuesta, ENTONCES aparece "Armando tu itinerario día por día…" (PAR-11).
- (negativo) DADO QUE la generación de un itinerario completo no responde en 60 segundos (PAR-09), CUANDO se cumple el plazo, ENTONCES se muestra "No pudimos generar tu itinerario. Reintentar".
- (negativo) DADO QUE falló la generación de un itinerario a Cusco del 2026-12-18 al 2026-12-21, Familia, 1 menor de 3–5 años, Equilibrado, CUANDO pulso "Reintentar", ENTONCES el formulario conserva todos esos valores.
- (borde) DADO QUE un dispositivo hizo 15 operaciones de IA exitosas en las últimas 24 h (PAR-07) y la primera fue el 2026-11-30 a las 20:05, CUANDO pide la operación 16 el 2026-12-01 a las 18:40, ENTONCES se rechaza con "Alcanzaste el límite de 15 operaciones. Podrás volver a intentarlo desde el 01/12/2026 a las 20:05." `[mecanismo de identificación del dispositivo pendiente: PREG-17]`
- (borde) DADO QUE un dispositivo tiene 10 operaciones de IA exitosas y 2 fallidas, CUANDO consulta las operaciones disponibles, ENTONCES le quedan 5.
- (negativo) DADO QUE el dispositivo no tiene conexión, CUANDO veo el itinerario, ENTONCES se muestra el banner "Sin conexión. Algunas acciones no están disponibles." y "Regenerar día" aparece deshabilitado con "Necesitas conexión para regenerar". `[textos para "Generar itinerario" y "Pedir alternativa" deshabilitados pendientes: PREG-58]`
- (negativo) DADO QUE se pierde la conexión mientras se regenera el Día 3, CUANDO vuelve la conexión, ENTONCES el itinerario local conserva la versión anterior del Día 3 y se muestra "Se interrumpió la regeneración del Día 3. ¿Reintentar?"; la operación no se reenvía sin mi confirmación.
- (negativo) DADO QUE hay un proveedor de IA secundario configurado y el principal devuelve HTTP 503, CUANDO el backend redirige la operación, ENTONCES lo hace a través de la capa de abstracción y registra el proveedor y la versión del modelo usados.
- [NUEVO] (borde) DADO QUE pulsé "Generar itinerario", CUANDO la respuesta llega a los 2 segundos, ENTONCES no aparece el indicador "Armando tu itinerario día por día…" (RN-101: más de 3 s, PAR-11).
- [NUEVO] (borde) DADO QUE la generación de un itinerario completo responde a los 59 segundos, CUANDO llega la respuesta válida, ENTONCES se muestra el itinerario y no el mensaje de error (PAR-09: 60 s).
- [NUEVO] (borde) DADO QUE un dispositivo tiene 14 operaciones de IA exitosas en las últimas 24 h, CUANDO pide la operación 15, ENTONCES se acepta; y la 16 se rechaza con el texto de RN-043 (PAR-07).
- [NUEVO] (borde) DADO QUE la primera de las 15 operaciones exitosas fue el 2026-11-30 a las 20:05, CUANDO pido una operación el 2026-12-01 a las 20:05, ENTONCES se acepta. `[definición de la ventana de 24 h: PREG-30]`
- [NUEVO] (negativo) DADO QUE no hay proveedor secundario configurado y el proveedor principal devuelve HTTP 503, CUANDO falla la generación, ENTONCES el formulario conserva los valores ingresados (RN-088), la operación no cuenta para el límite (RN-089) y se muestra `[texto pendiente: PREG-58]` con la opción "Reintentar".
- [NUEVO] (negativo) DADO QUE se muestra "Se interrumpió la regeneración del Día 3. ¿Reintentar?", CUANDO no confirmo, ENTONCES no se envía ninguna operación, el Día 3 conserva su versión anterior y no se descuenta ninguna operación del límite (RN-093).
- [NUEVO] (negativo) DADO QUE el dispositivo no tiene conexión, CUANDO veo el itinerario, ENTONCES "Generar itinerario" y "Pedir alternativa" también están deshabilitados (RN-090). `[textos pendientes: PREG-58]`

**Reglas de negocio relacionadas:** RN-043, RN-083, RN-086, RN-088, RN-089, RN-090, RN-091, RN-092, RN-093, RN-101

**Notas:**
- Esta HU aplica también a las operaciones de F-05 (alternativa y regenerar día), cuyo plazo de 30 s se prueba en HU-010 y HU-011.
- El límite de 15 operaciones exitosas (PAR-07) debe contrastarse con la medición de SUP-03 (PREG-56). Los plazos de PAR-09 deben ajustarse tras medir las latencias reales.
- Falta definir si los 60 s de RN-083 incluyen el reintento automático de RN-085 (PREG-09).
- RN-086 es opcional (PUEDE): el último criterio solo aplica si existe un proveedor secundario configurado.

---

### EP-04 — Vista del itinerario (F-04)

#### HU-007: Ver el itinerario por días y franjas, con el aviso de sugerencia y el aviso migratorio

**Prioridad:** Must
**Funcionalidad:** F-04
**Plataformas:** web responsive (móvil 360–430 px y escritorio ≥ 1024 px)

Como **viajero**, quiero ver el itinerario organizado por día, con las actividades ordenadas por franja horaria y su duración aproximada, y con los avisos obligatorios a la vista, para entender rápido el plan de cada día y saber qué tengo que revisar antes de viajar.

**Criterios de aceptación:**
- (positivo) DADO QUE tengo un itinerario generado, CUANDO abro un día, ENTONCES cada actividad está en exactamente una franja horaria: Mañana (06:00–12:00), Tarde (12:00–18:00) o Noche (18:00–23:00) (PAR-04).
- (positivo) DADO QUE el Día 2 tiene actividades en las tres franjas, CUANDO lo veo, ENTONCES se muestran en el orden Mañana → Tarde → Noche.
- (positivo) DADO QUE una actividad tiene duración, CUANDO la veo, ENTONCES se muestra en minutos con su estado de confiabilidad, p. ej., "Museo Larco · 120 min · ≈ Estimada — Valor aproximado basado en la duración habitual de visitas a museos de la zona. Te recomendamos confirmarlo antes de tu viaje."
- (positivo) DADO QUE no existe una fuente registrada de tiempos de desplazamiento, CUANDO veo un traslado, ENTONCES se muestra con ≈ Estimada, p. ej., "Traslado a Sacsayhuamán: 20 min · ≈ Estimada — Valor aproximado basado en {base_de_cálculo}. Te recomendamos confirmarlo antes de tu viaje."
- (positivo) DADO QUE abro cualquier itinerario, CUANDO veo su encabezado, ENTONCES se muestra "Este itinerario es una sugerencia. Revisa los datos marcados como ≈ Estimada o ! Por confirmar antes de tu viaje."
- (positivo) DADO QUE abro un itinerario de cualquier destino, incluido Perú (p. ej., Cusco o Bogotá), CUANDO veo la vista, ENTONCES se muestra el aviso migratorio "Los requisitos de ingreso pueden cambiar. Consúltalos en la fuente oficial antes de tu viaje" con enlaces de la lista curada (p. ej., Migraciones Perú para Cusco y Migración Colombia para Bogotá), sin que se me haya pedido país de residencia ni nacionalidad. `[enlaces si el país no está en la lista curada o el enlace está roto: PREG-04]`
- (positivo) DADO QUE uso un lector de pantalla (VoiceOver, TalkBack o lector en web), CUANDO recorro el itinerario, ENTONCES cada elemento interactivo tiene etiqueta accesible y un objetivo táctil de al menos 44 × 44 pt, y con el texto ampliado al 200 % no se pierde contenido.
- [NUEVO] (borde) DADO QUE una actividad empieza exactamente a las 12:00 o a las 18:00, CUANDO se asigna su franja, ENTONCES queda en una sola franja. `[franja de los límites 12:00 y 18:00, y tratamiento de actividades entre 23:00 y 06:00: PREG-22]`
- [NUEVO] (borde) DADO QUE el Día 2 no tiene actividades en la franja Noche, CUANDO lo veo, ENTONCES se muestran Mañana → Tarde en ese orden. `[qué se muestra en una franja o un día sin actividades: PREG-65]`
- [NUEVO] (negativo) DADO QUE la respuesta del LLM trae una actividad sin franja horaria o sin duración, CUANDO el backend valida la respuesta, ENTONCES no la entrega al cliente y reintenta 1 vez (RN-018, RN-020, RN-022, PAR-10).
- [NUEVO] (negativo) DADO QUE el país de destino no tiene entrada en la lista curada, CUANDO abro el itinerario, ENTONCES el aviso migratorio "Los requisitos de ingreso pueden cambiar. Consúltalos en la fuente oficial antes de tu viaje" se muestra igual (RN-024: en todo itinerario). `[enlaces en ese caso: PREG-04]`
- [NUEVO] (positivo) DADO QUE uso la web solo con teclado, CUANDO recorro el itinerario, ENTONCES puedo llegar a cada día, actividad y acción con foco visible, y la prueba E2E con axe-core no reporta violaciones *serious* ni *critical* (PRINCIPIO-010).

**Reglas de negocio relacionadas:** RN-018, RN-019, RN-020, RN-024, RN-026, RN-060

**Notas:**
- RN-024 pertenece a F-03 en el Anexo C de 03, pero se prueba en la vista porque la regla fija el aviso "en la vista de todo itinerario". La lista curada se revisa cada 30 días (DEP-04).
- En el MVP las duraciones son aproximadas y se marcan ≈ Estimada sin llamar a la API de rutas (DEP-05).
- Si la dirección, la duración y la distancia entran en la lista canónica de datos factuales: PREG-01.
- El aviso para menores y el aviso sanitario quedan en PREG-04.
- El mapa del día es F-13 (Could) y está fuera de esta HU.

---

### EP-05 — Confiabilidad de datos (F-06)

#### HU-008: Ver cada dato factual con su estado de confiabilidad vigente y los textos exactos

**Prioridad:** Must
**Funcionalidad:** F-06
**Plataformas:** web responsive (móvil 360–430 px y escritorio ≥ 1024 px)

Como **viajero**, quiero que cada precio, horario, disponibilidad, restricción, clima o tiempo de traslado muestre si está Verificada, Estimada o Por confirmar, con su fuente o su recomendación, para saber exactamente qué debo confirmar antes de mi viaje.

**Criterios de aceptación:**
- (positivo) DADO QUE un dato está verificado por una fuente registrada, CUANDO se muestra, ENTONCES lleva la etiqueta `✓ Verificada` y el texto "Fuente: {fuente} · Consultado el {dd/mm/aaaa} a las {HH:mm}", p. ej., "✓ Verificada — Fuente: sitio oficial del museo · Consultado el 01/12/2026 a las 10:15".
- (positivo) DADO QUE un dato es aproximado, CUANDO se muestra, ENTONCES lleva la etiqueta `≈ Estimada` y el texto "Valor aproximado basado en {base_de_cálculo}. Te recomendamos confirmarlo antes de tu viaje."
- (negativo) DADO QUE un dato no se pudo verificar, CUANDO se muestra, ENTONCES lleva la etiqueta `! Por confirmar` y el texto "No pudimos verificar este dato. Confírmalo con {fuente_sugerida} antes de tu viaje.", y el dato no queda vacío ni desaparece de la actividad. `[origen de {fuente_sugerida}: PREG-06]`
- (positivo) DADO QUE se muestra cualquier dato factual, CUANDO se revisa su componente, ENTONCES tiene exactamente un estado, mostrado con ícono + texto + color, con contraste ≥ 4.5:1 para texto normal (≥ 3:1 para texto grande). `[colores de Verificada y Por confirmar pendientes: PREG-36]`
- (borde) DADO QUE un horario ✓ Verificada se consultó el 2026-12-01 a las 10:00, CUANDO se muestra el 2026-12-08 a las 10:01 (más de 7 días), ENTONCES aparece como `! Por confirmar`; lo mismo vale para precios y restricciones de actividad.
- (negativo) DADO QUE la fuente de un dato de disponibilidad, clima o tiempo de traslado no declara fecha de vigencia, CUANDO se muestra, ENTONCES nunca aparece como `✓ Verificada`.
- (borde) DADO QUE un horario ✓ Verificada se consultó el 01/12/2026 a las 10:00 y el invitado reabre su itinerario guardado el 09/12/2026, CUANDO se muestra, ENTONCES aparece como `! Por confirmar`. `[lugar del cálculo de la vigencia: PREG-07]`
- (positivo) DADO QUE se renderiza un estado en el viewport móvil y en el de escritorio para el mismo dato, CUANDO se ejecuta el test de snapshot, ENTONCES ambos viewports muestran la misma etiqueta y el mismo texto, tomados de claves de i18n (p. ej., `t('reliability.estimated.label')` → "≈ Estimada"), sin literales en el componente.
- [NUEVO] (borde) DADO QUE un horario ✓ Verificada se consultó el 2026-12-01 a las 10:00, CUANDO se muestra el 2026-12-08 a las 10:00 (exactamente 7 días), ENTONCES sigue como `✓ Verificada` (RN-068: más de 7 días).
- [NUEVO] (borde) DADO QUE un dato se consultó el 2027-01-05 a las 09:05, CUANDO se muestra como `✓ Verificada`, ENTONCES el texto es "Consultado el 05/01/2027 a las 09:05", con ceros a la izquierda (formato dd/mm/aaaa y HH:mm del PRINCIPIO-003).
- [NUEVO] (borde) DADO QUE la fuente de disponibilidad de un tour declara vigencia hasta el 19/12/2026 a las 23:59, CUANDO el dato se muestra el 20/12/2026 a las 00:00, ENTONCES aparece como `! Por confirmar` (PRINCIPIO-003: vigencia declarada por la fuente).
- [NUEVO] (negativo) DADO QUE llega al backend un dato con dos estados a la vez (`estimated` y `to_confirm`), CUANDO se valida el esquema, ENTONCES el dato se rechaza y no se entrega (RN-061, RN-022).
- [NUEVO] (negativo) DADO QUE se ve el itinerario sin color (escala de grises o simulación de daltonismo), CUANDO comparo los tres estados, ENTONCES se distinguen por su ícono (✓, ≈, !) y su texto (RN-062, PRINCIPIO-010).
- [NUEVO] (negativo) DADO QUE falta una clave de estado de confiabilidad en el archivo es-419, CUANDO corre la CI, ENTONCES el test de claves de traducción falla y el PR no se fusiona (PRINCIPIO-011, RN-100).

**Reglas de negocio relacionadas:** RN-061, RN-062, RN-065, RN-066, RN-067, RN-068, RN-070, RN-071, RN-100, RN-107

**Notas:**
- La función que asigna estado y vigencia exige 100 % de cobertura (PRINCIPIO-003, PRINCIPIO-012).
- RN-069 (vigencia de 24 h para requisitos migratorios y sanitarios) es WON'T (v1) en 03 (QB-06), porque la app no muestra esos requisitos (D-06, PRINCIPIO-008). Por eso no tiene criterio de aceptación.
- La zona horaria en la que se muestra `{HH:mm}` sigue abierta en PREG-23 y PREG-32.

---

#### HU-009: Recibir del backend solo contenido respaldado: sin Verificada sin fuente ni afirmaciones prohibidas

**Prioridad:** Must
**Funcionalidad:** F-06
**Plataformas:** web responsive (móvil 360–430 px y escritorio ≥ 1024 px)

Como **viajero**, quiero que el backend nunca me entregue un dato inventado presentado como hecho, un requisito migratorio redactado por la IA, un enlace de reserva ni una afirmación de seguridad sin fuente, para poder confiar en el itinerario más que en un chat de IA genérico.

**Criterios de aceptación:**
- (negativo) DADO QUE el LLM devuelve un dato con `reliability_status: "verified"`, CUANDO el backend procesa la respuesta, ENTONCES no acepta ese estado del LLM: solo la capa de verificación puede asignar `verified` tras consultar una fuente registrada.
- (negativo) DADO QUE llega al backend `{ "price": 30, "reliability_status": "verified", "source": null }`, CUANDO se valida el esquema, ENTONCES el dato se rechaza.
- (negativo) DADO QUE llega `{ "opening_hours": "09:00–17:00", "reliability_status": "verified", "source": "https://blog-de-viajes.example" }` y esa fuente no pertenece al registro de fuentes, CUANDO se valida, ENTONCES el dato se rechaza.
- (negativo) DADO QUE un precio, horario, disponibilidad, restricción de actividad (edad, altura, salud o accesibilidad) o dato de clima no proviene de una fuente registrada, CUANDO el backend lo entrega, ENTONCES lleva el estado `≈ Estimada` o `! Por confirmar`.
- (negativo) DADO QUE la fuente de lugares no encuentra "Mirador X", CUANDO se entrega la actividad, ENTONCES muestra "Lugar: ! Por confirmar — No pudimos verificar este dato. Confírmalo con la oficina de turismo local antes de tu viaje."
- (negativo) DADO QUE un campo de texto libre (nombre, descripción, nota u orientación) contiene un dato factual, p. ej., "Abre a las 9:00 y los domingos la entrada es gratis", CUANDO el backend valida la respuesta, ENTONCES no la entrega al cliente. `[mecanismo de cumplimiento: PREG-05]`
- (negativo) DADO QUE el viaje es a Santiago de Chile o a Ciudad de México, CUANDO se genera el itinerario, ENTONCES no contiene requisitos migratorios ni sanitarios redactados por el LLM (p. ej., "Para entrar a Chile solo necesitas tu DNI vigente"), solo el aviso migratorio de HU-007.
- (negativo) DADO QUE se genera cualquier itinerario, CUANDO se revisa la respuesta estructurada, ENTONCES no contiene enlaces de reserva, de pago ni de afiliados, ni describe un destino como seguro sin una fuente oficial registrada (p. ej., "Barranco es una zona totalmente segura de noche").
- [NUEVO] (positivo) DADO QUE el LLM devuelve un horario con `reliability_status: "to_confirm"` y la capa de verificación lo consulta en el sitio oficial del museo, que está en el registro de fuentes, CUANDO el backend entrega la actividad, ENTONCES el horario lleva `verified`, la fuente y la fecha y hora de consulta (RN-063).
- [NUEVO] (positivo) DADO QUE la descripción de una actividad es "Colección de cerámica precolombina" y el horario va en su propio campo con su estado, CUANDO el backend valida la respuesta, ENTONCES la entrega al cliente (RN-102).
- [NUEVO] (borde) DADO QUE llega `{ "price": 30, "reliability_status": "verified", "source": "" }` (cadena vacía), CUANDO se valida el esquema, ENTONCES el dato se rechaza igual que con `source: null` (RN-064, RN-106).
- [NUEVO] (negativo) DADO QUE la fuente registrada de horarios no responde durante la generación, CUANDO el backend entrega la actividad, ENTONCES el horario aparece como `! Por confirmar` con su texto obligatorio, nunca vacío ni como `✓ Verificada` (RN-056, RN-071).

**Reglas de negocio relacionadas:** RN-023, RN-025, RN-055, RN-056, RN-057, RN-058, RN-059, RN-063, RN-064, RN-102, RN-103, RN-104, RN-105, RN-106

**Notas:**
- Se verifica con el golden set de no invención (casos de reservas, visas, consultas médicas y evasión de controles) y con tests del esquema (PRINCIPIO-002, PRINCIPIO-008).
- El contenido, el responsable y el proceso del registro de fuentes están en PREG-06. Si la existencia de un lugar es un dato factual y con qué vigencia: PREG-01.
- Es una HU con mucho backend: la parte visible en web y mobile es la que cubre HU-008.

---

### EP-06 — Ajuste esencial (F-05)

#### HU-010: Eliminar una actividad o pedir una alternativa

**Prioridad:** Must
**Funcionalidad:** F-05
**Plataformas:** web responsive (móvil 360–430 px y escritorio ≥ 1024 px)

Como **viajero**, quiero eliminar una actividad que no me sirve o pedir una alternativa en su mismo lugar del plan, para corregir lo que la IA propuso sin rehacer todo el itinerario.

**Criterios de aceptación:**
- (positivo) DADO QUE el Día 2 tiene 3 actividades, una de ellas "Tarde — Museo Inka", CUANDO la elimino, ENTONCES el Día 2 queda con 2 actividades y el cambio se guarda en el dispositivo, sin pedir cuenta. `[confirmación previa o deshacer: PREG-59]`
- (positivo) DADO QUE pido una alternativa para "Día 3 · Tarde — Sacsayhuamán", CUANDO llega el resultado, ENTONCES la alternativa queda en el Día 3, franja Tarde.
- (negativo) DADO QUE "Mercado de San Pedro" ya está en el Día 1, CUANDO pido una alternativa para una actividad del Día 3, ENTONCES la alternativa no es "Mercado de San Pedro" ni otra actividad que ya figure en el itinerario.
- (positivo) DADO QUE el ritmo es Relajado y el grupo incluye un menor de 0–2 años, CUANDO llega una alternativa, ENTONCES el día resultante cumple RN-021, RN-027 a RN-034 y RN-111 (p. ej., carga diaria ≤ 360 min, sin actividades excluidas por edad y con un bloque de descanso de ≥ 60 min).
- (negativo) DADO QUE no existe ninguna alternativa para "Día 2 · Tarde" compatible con un menor de 0–2 años, CUANDO termina la búsqueda, ENTONCES se muestra "No encontramos otra actividad que encaje con tu grupo en esta franja." y la actividad original se mantiene. `[qué más se ofrece: PREG-08]`
- (negativo) DADO QUE pedí una alternativa, CUANDO pasan 30 segundos sin respuesta (PAR-09), ENTONCES se muestra un mensaje de error con la opción "Reintentar" y la actividad original no cambia. `[texto exacto para la alternativa pendiente: PREG-58]`
- (positivo) DADO QUE una actividad tiene una orientación por rango de edad, CUANDO elijo conservarla en lugar de pedir alternativa, ENTONCES sigue en el itinerario tras recargar.
- [NUEVO] (borde) DADO QUE el Día 3 tiene una sola actividad, CUANDO la elimino, ENTONCES el Día 3 sigue en el itinerario con 0 actividades y el cambio se guarda en el dispositivo (RN-017, RN-038). `[presentación de un día vacío: PREG-65]`
- [NUEVO] (borde) DADO QUE el grupo incluye un menor de 3–5 años y el Día 1 tiene "Tarde — Descanso en el alojamiento · 90 min", CUANDO elimino ese bloque y recargo la app, ENTONCES el bloque sigue eliminado (PAR-06 es eliminable, RN-038).
- [NUEVO] (borde) DADO QUE el dispositivo no tiene conexión, CUANDO elimino una actividad, ENTONCES la eliminación se aplica y se guarda, porque no usa IA (RN-090 solo deshabilita generar, pedir alternativa y regenerar).
- [NUEVO] (borde) DADO QUE pedí una alternativa, CUANDO la respuesta válida llega a los 29 segundos, ENTONCES la alternativa reemplaza a la actividad original (PAR-09: 30 s).
- [NUEVO] (negativo) DADO QUE el dispositivo ya hizo 15 operaciones de IA exitosas en las últimas 24 h, CUANDO pido una alternativa, ENTONCES se rechaza con el texto de RN-043 y la actividad original no cambia.
- [NUEVO] (negativo) DADO QUE eliminé una actividad, CUANDO consulto las operaciones de IA disponibles, ENTONCES el número no cambió (eliminar no usa IA, PAR-07).

**Reglas de negocio relacionadas:** RN-035, RN-038, RN-039, RN-040, RN-042, RN-084, RN-108

**Notas:**
- Cada alternativa exitosa cuenta como una operación de IA para el límite de 15 (PAR-07, RN-043, HU-006). Eliminar no usa IA y no cuenta.
- Sin conexión, "Pedir alternativa" queda deshabilitado (RN-090, HU-006).
- Agregar actividades propias, editarlas o reordenarlas es F-09 (Should) y está fuera de esta HU.

---

#### HU-011: Regenerar un día completo sin tocar los demás días

**Prioridad:** Must
**Funcionalidad:** F-05
**Plataformas:** web responsive (móvil 360–430 px y escritorio ≥ 1024 px)

Como **viajero**, quiero regenerar un día completo del itinerario, para reemplazar un día que no me convence sin perder los días que ya me gustan.

**Criterios de aceptación:**
- (positivo) DADO QUE tengo un itinerario de 4 días, CUANDO regenero el Día 2, ENTONCES los Días 1, 3 y 4 quedan idénticos (mismas actividades, franjas, duraciones y estados).
- (positivo) DADO QUE el ritmo es Relajado, CUANDO se regenera el Día 2, ENTONCES su carga diaria es ≤ 360 min (p. ej., 345 min) y cumple RN-021, RN-027 a RN-034 y RN-111.
- (negativo) DADO QUE pulsé "Regenerar Día 2", CUANDO pasan 30 segundos sin respuesta (PAR-09), ENTONCES se muestra "No pudimos regenerar el Día 2. Reintentar" y el Día 2 conserva sus 3 actividades originales.
- (negativo) DADO QUE se pierde la conexión mientras se regenera el Día 3, CUANDO reviso el itinerario guardado, ENTONCES el Día 3 está en su versión anterior completa, no guardado a medias.
- (negativo) DADO QUE no existe un día regenerado que cumpla RN-042, CUANDO termina la operación, ENTONCES se me informa que no se encontró una opción válida y el día no cambia. `[texto exacto pendiente: PREG-58; qué más se ofrece: PREG-08]`
- [NUEVO] (borde) DADO QUE el ritmo es Relajado, CUANDO el Día 2 regenerado suma exactamente 360 minutos, ENTONCES se acepta (RN-027, RN-042).
- [NUEVO] (borde) DADO QUE el itinerario va del 2026-12-18 al 2026-12-21, CUANDO regenero el Día 4, ENTONCES el día regenerado conserva la fecha 2026-12-21 y el itinerario sigue con exactamente 4 días (RN-017).
- [NUEVO] (negativo) DADO QUE regeneré el Día 2 y la respuesta del LLM incluye también cambios en actividades del Día 3, CUANDO el backend procesa la respuesta, ENTONCES el Día 3 queda idéntico (RN-041).
- [NUEVO] (negativo) DADO QUE la respuesta de la regeneración no cumple el esquema, CUANDO falla también el único reintento, ENTONCES el Día 2 conserva sus actividades originales y la operación no cuenta para el límite (RN-085, RN-087, RN-089).
- [NUEVO] (negativo) DADO QUE el dispositivo ya hizo 15 operaciones de IA exitosas en las últimas 24 h, CUANDO pulso "Regenerar Día 2", ENTONCES se rechaza con el texto de RN-043 y el Día 2 no cambia.
- [NUEVO] (positivo) DADO QUE pulsé "Regenerar Día 2", CUANDO pasan 3 segundos sin respuesta, ENTONCES aparece un indicador de progreso (RN-101, PAR-11). `[texto pendiente: PREG-58]`

**Reglas de negocio relacionadas:** RN-041, RN-042, RN-084, RN-087, RN-092, RN-108

**Notas:**
- Una regeneración exitosa cuenta como una operación de IA (PAR-07). Una fallida no cuenta (RN-089).
- Si un PR toca el prompt de regeneración, debe pasar el 100 % del golden set (PRINCIPIO-012).

---

### EP-07 — Cuenta opcional (F-10)

#### HU-012: Crear una cuenta opcional y asociar a ella mis viajes locales

**Prioridad:** Should
**Funcionalidad:** F-10
**Plataformas:** web responsive (móvil 360–430 px y escritorio ≥ 1024 px)

Como **invitado** con viajes guardados en mi dispositivo, quiero crear una cuenta opcional o iniciar sesión y decidir, viaje por viaje, cuáles asocio a la cuenta, para conservarlos y recuperarlos en otro dispositivo sin perder el control de mis datos.

**Criterios de aceptación:**
- (positivo) DADO QUE tengo 2 viajes locales ("Cusco, 2026-12-18" y "Arequipa, 2027-01-15"), CUANDO creo una cuenta o inicio sesión, ENTONCES se muestran por separado "¿Guardar 'Cusco, 2026-12-18' en tu cuenta?" y "¿Guardar 'Arequipa, 2027-01-15' en tu cuenta?", sin una opción "Guardar todo" marcada por defecto.
- (positivo) DADO QUE como usuario registrado consiento solo "Cusco", CUANDO termina la asociación, ENTONCES solo "Cusco" existe en la cuenta y ningún viaje se sube sin su consentimiento explícito.
- (negativo) DADO QUE como usuario registrado rechazo "Arequipa", CUANDO vuelvo a abrir la app en ese dispositivo, ENTONCES "Arequipa" sigue disponible en el dispositivo y no existe en la cuenta.
- (borde) DADO QUE asocio un viaje con un horario ✓ Verificada consultado el 2026-12-01 a las 10:15, CUANDO lo veo en la cuenta el 2026-12-05, ENTONCES conserva "Consultado el 01/12/2026 a las 10:15" (no la fecha de la asociación) y pasa a `! Por confirmar` el 2026-12-08 a las 10:16.
- (negativo) DADO QUE inicié sesión, CUANDO se inspecciona dónde está el token, ENTONCES en web está en una cookie `HttpOnly; Secure; SameSite` y en mobile en el almacén seguro del sistema operativo; el almacenamiento local no contiene tokens, contraseñas, credenciales, nombre, correo, teléfono ni documento (PRINCIPIO-006).
- (positivo) DADO QUE soy invitado y no quiero cuenta, CUANDO uso cualquier funcionalidad de F-01 a F-06, ENTONCES puedo completarla sin registrarme (RN-072).
- [NUEVO] (borde) DADO QUE no tengo viajes guardados en el dispositivo (lista vacía), CUANDO creo una cuenta, ENTONCES no se muestra ninguna pregunta de asociación y la cuenta queda sin viajes.
- [NUEVO] (borde) DADO QUE se muestra "¿Guardar 'Cusco, 2026-12-18' en tu cuenta?", CUANDO cierro el diálogo sin responder, ENTONCES "Cusco" no se asocia a la cuenta y sigue en el dispositivo (RN-080: sin consentimiento explícito no se asocia; RN-081). `[confirmar que cerrar equivale a rechazar: PREG-61]`
- [NUEVO] (negativo) DADO QUE consentí asociar "Cusco", CUANDO se pierde la conexión durante la asociación, ENTONCES "Cusco" sigue completo en el dispositivo. `[estado en la cuenta y reintento: PREG-61]`
- [NUEVO] (negativo) DADO QUE soy usuario registrado, CUANDO pido a la API un viaje que pertenece a otra cuenta, ENTONCES no se devuelve ningún dato de ese viaje. `[código HTTP y texto pendientes: PREG-61]`
- [NUEVO] (negativo) DADO QUE el cliente intenta conectarse por HTTP plano o con TLS 1.1, CUANDO se inicia sesión, ENTONCES la conexión se rechaza (PRINCIPIO-006: TLS 1.2 como mínimo).
- [NUEVO] (positivo) DADO QUE creo una cuenta con contraseña, CUANDO un test inspecciona el hash guardado, ENTONCES empieza por `$argon2id$` con memoria ≥ 19 MiB, 2 iteraciones y paralelismo 1 (PRINCIPIO-006). `[métodos de registro: PREG-60]`

**Reglas de negocio relacionadas:** RN-079, RN-080, RN-081, RN-082, RN-098

**Notas:**
- F-10 es Should: llega en v1 tras el MVP, no en el piloto (AL-25). Por eso la conversión a cuenta se mide después del MVP.
- Las contraseñas se guardan solo con Argon2id (mínimo 19 MiB, 2 iteraciones, paralelismo 1), y el login social usa OAuth 2.0 / OpenID Connect (PRINCIPIO-006). Los métodos de registro concretos están en PREG-60.
- La eliminación de cuenta (máximo 30 días, incluidos los respaldos, PRINCIPIO-005) y la retención de cuentas inactivas (decisión pendiente de la Constitución) quedan fuera de esta HU: ver PREG-60 (eliminación de cuenta) y PREG-43 (retención).
- La sincronización entre dispositivos de los viajes asociados forma parte de F-10, pero no tiene reglas propias en 03: ver PREG-60.
- Si la HU no cabe en una iteración, se puede dividir en (a) registro e inicio de sesión y (b) asociación de viajes locales.

---

## 3. Verificación INVEST

| HU | I | N | V | E | S | T | Observación |
|---|---|---|---|---|---|---|---|
| HU-001 | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | Para probarla de punta a punta necesita un itinerario. Se usa un fixture del contrato. |
| HU-002 | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | Depende de la fuente de lugares (DEP-02) para RN-007. Se usa un mock en las pruebas. |
| HU-003 | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | Tiene pendientes de texto y de catálogo (PREG-57, PREG-25), pero no bloquean la estimación. La composición de los tipos de grupo está en 04-glosario. |
| HU-004 | ~ | ✓ | ✓ | ~ | ✓ | ✓ | Depende del contrato de HU-002 y HU-003 y del proveedor de IA (PREG-13). Se desacopla con fixtures OpenAPI. |
| HU-005 | ~ | ✓ | ✓ | ✓ | ✓ | ✓ | Amplía la generación de HU-004. Puede desarrollarse en paralelo sobre la capa de verificación. |
| HU-006 | ✓ | ✓ | ✓ | ~ | ✓ | ✓ | La estimación depende de PREG-17 (cómo se identifica el dispositivo para el límite). |
| HU-007 | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | Los enlaces de la lista curada dependen de PREG-04. |
| HU-008 | ✓ | ✓ | ✓ | ~ | ✓ | ✓ | La estimación depende de PREG-07 (dónde se calcula la vigencia). |
| HU-009 | ✓ | ✓ | ✓ | ~ | ~ | ✓ | Es la HU más grande. Si no cabe, se divide en "esquema y registro de fuentes" y "golden set de afirmaciones prohibidas". El mecanismo para el texto libre está en PREG-05. |
| HU-010 | ~ | ✓ | ✓ | ✓ | ✓ | ✓ | Reutiliza las validaciones de generación de HU-004 y HU-005 (RN-042). |
| HU-011 | ~ | ✓ | ✓ | ✓ | ✓ | ✓ | Igual que HU-010. |
| HU-012 | ✓ | ✓ | ✓ | ~ | ~ | ✓ | Puede dividirse en registro y asociación. Los métodos de registro están en PREG-60. |

✓ = cumple · ~ = cumple con la salvedad indicada.

---

## 4. Preguntas del PO

> Trazabilidad del triaje del 2026-11-07. Las preguntas se trasladaron a 05-preguntas-abiertas.md: **A** = ya resuelta en 02, 03 o 04 (se reemplazó el marcador por el valor); **B** = cubierta por una PREG existente; **C** = PREG nueva en 05. Las preguntas originales están registradas en la tabla siguiente y consolidadas en el Spec Pack.

| PO-Qn | Tema (resumen) | Destino | Justificación |
|---|---|---|---|
| PO-Q1 | Textos de rechazo de RN-004, RN-008, RN-010, RN-012 y RN-016; código HTTP | C: PREG-57 nueva | 03 no fija esos textos y RN-095 da HTTP 422 solo para campos no definidos; ninguna PREG lo cubre. |
| PO-Q2 | Composición de Solo, Pareja y Familia; textos de RN-011; texto de RN-036 con menores de 0–2 | A: Solo = 1 adulto y 0 menores; Pareja = 2 adultos y 0 menores; Familia = ≥ 1 adulto y ≥ 1 menor (04-glosario) | La composición está textual en 04 ("Tipo de grupo"). Los textos de RN-011 pasan a PREG-57 y el texto del aviso de RN-036, a PREG-26. |
| PO-Q3 | Catálogo cerrado de necesidades del grupo | B: PREG-25 | PREG-25 pregunta los valores del catálogo; 04 solo da ejemplos. |
| PO-Q4 | Aviso migratorio sin enlace curado; avisos para menores y sanitario | B: PREG-04 | Es exactamente lo que PREG-04 deja abierto. |
| PO-Q5 | Texto tras el reintento fallido; si los 60 s incluyen el reintento | C: PREG-58 nueva | El texto no está en 03 ni en ninguna PREG; la parte de los 60 s ya está en PREG-09. |
| PO-Q6 | Identificación del dispositivo para el límite de operaciones | B: PREG-17 | PREG-17 pregunta qué identifica a un dispositivo. |
| PO-Q7 | Textos de acciones deshabilitadas, plazo, progreso y "sin opción válida" | C: PREG-58 nueva | 03 da un solo texto por regla (RN-084, RN-090, RN-101, RN-108); ninguna PREG pide los demás. |
| PO-Q8 | Colores de ✓ Verificada y ! Por confirmar | B: PREG-36 | PREG-36 pregunta la paleta de los estados. |
| PO-Q9 | Confirmación o deshacer al eliminar una actividad | C: PREG-59 nueva | RN-038 no lo define y no hay PREG. |
| PO-Q10 | Métodos de registro y datos de la cuenta (F-10) | C: PREG-60 nueva | No está en 03 ni en PREG-39 o PREG-43; se agrupa con PO-Q11 y PO-Q36 (alcance de F-10). |
| PO-Q11 | Eliminación de cuenta, sincronización y retención de cuentas inactivas | C: PREG-60 nueva | Eliminación y sincronización no tienen reglas ni PREG; la retención ya está en PREG-43. |
| PO-Q12 | Dirección, duración, distancia y existencia del lugar como datos factuales | B: PREG-01 | El PO ya la remitía a PREG-01. |
| PO-Q13 | Origen de {fuente_sugerida} y contenido del registro de fuentes | B: PREG-06 | El PO ya la remitía a PREG-06. |
| PO-Q14 | Dónde se recalcula la vigencia de un itinerario guardado | B: PREG-07 | El PO ya la remitía a PREG-07. |
| PO-Q15 | Qué se ofrece cuando no hay opción válida | B: PREG-08 | El PO ya la remitía a PREG-08. |
| PO-Q16 | Mecanismo para el texto libre (RN-102) | B: PREG-05 | El PO ya la remitía a PREG-05. |
| PO-Q17 | Zona horaria de "fecha actual" y de {HH:mm} | B: PREG-23, PREG-32 | El PO ya la remitía a esas dos PREG. |
| PO-Q18 | Fecha a la que se refiere una verificación | B: PREG-24 | El PO ya la remitía a PREG-24. |
| PO-Q19 | Altura, edad máxima, salud y restricciones vencidas | B: PREG-28 | El PO ya la remitía a PREG-28. |
| PO-Q20 | Aprobación de PAR-01 a PAR-11 y reglas "BA (propuesta)" | B: PREG-55 | El PO ya la remitía a PREG-55. |
| PO-Q21 | Umbrales de SUP-03 y SUP-04 | B: PREG-56 | El PO ya la remitía a PREG-56. |
| PO-Q22 | Mobile en el piloto: PWA o distribución de prueba (RES-11) | B: PREG-16 | El PO no le asignó PREG, pero PREG-16 pregunta lo mismo (PWA, Expo o ambas). |

---

## § Validación INVEST (QA)

> Revisión del Agente QA Funcional (2026-11-07). Complementa la sección 3 del PO. Correcciones **aplicadas** = solo de redacción. Correcciones **sugeridas** = cambian el alcance y quedan para el PO.

| HU | ¿Cumple? | Corrección sugerida |
|---|---|---|
| HU-001 | Sí, con salvedad (T) | **Aplicada:** marcador PREG-66 en el criterio de logs. Con "cada línea de log contiene solo `request_id`, `duration_ms` y `status`" (texto anterior; desde el 2026-11-07 el criterio remite a la lista blanca de C-14, QB-04) y "no hay filas nuevas", no queda dónde registrar el proveedor y la versión del modelo que exige el PRINCIPIO-009. El PRINCIPIO-005 y el PRINCIPIO-009 chocan aquí, y gana la Constitución completa. **Sugerida:** una vez que responda PREG-66, permitir `provider` y `model_version` en el log (no son payload del viaje) o indicar otro destino para ese registro. |
| HU-002 | Sí | Ninguna. Los criterios tienen valores concretos, salvo el texto de rechazo de RN-004 (PREG-57). |
| HU-003 | Sí, con salvedad (S) | **Sugerida:** con 16 criterios es grande. Se puede dividir en (a) tipo y composición del grupo (RN-008 a RN-011) y (b) menores, necesidades y ritmo (RN-012 a RN-016, RN-036, RN-037, RN-095). |
| HU-004 | Parcial (I, T) | **Sugerida:** el criterio "el ADR … documenta la opción de no entrenamiento" (y el nuevo de no retención) revisa un documento, no un comportamiento del sistema. Conviene pasarlo a la Definition of Done de la spec o a un checklist del ADR. Sigue dependiendo de los contratos de HU-002 y HU-003 (fixtures OpenAPI). |
| HU-005 | Sí | Ninguna. |
| HU-006 | Parcial (S, E) | **Sugerida:** reúne cuatro temas independientes. Dividirla en HU-006a, esperas y fallas del servicio de IA (RN-083, RN-086, RN-088, RN-101); HU-006b, límite de operaciones (RN-043, RN-089), que es la que bloquean PREG-17 y PREG-30; y HU-006c, pérdida de conexión (RN-090 a RN-093). |
| HU-007 | Sí, con salvedad (V) | **Sugerida:** el criterio de accesibilidad (y el nuevo de teclado y axe-core) aplica a todas las pantallas. Conviene llevarlo a una Definition of Done transversal (PRINCIPIO-010) y dejar aquí solo lo propio de la vista del itinerario. |
| HU-008 | Sí, con salvedad (E) | Ninguna nueva. La estimación sigue dependiendo de PREG-07. |
| HU-009 | Parcial (S) | **Sugerida:** se mantiene la división que propone el PO ("esquema y registro de fuentes" / "golden set de afirmaciones prohibidas"). El criterio 4 no tiene ejemplo concreto. **Sugerida:** agregar uno por categoría, como el de clima de RN-103. |
| HU-010 | Parcial (S) | **Sugerida:** eliminar (sin IA, sin límite, funciona sin conexión) y pedir alternativa (IA, límite, 30 s) se prueban y se estiman por separado. Se puede dividir en HU-010a, eliminar, y HU-010b, alternativa. |
| HU-011 | Sí | Ninguna. |
| HU-012 | Parcial (I, E, S) | **Aplicada:** el criterio del token agrega "credenciales" y "documento" a lo que el almacenamiento local no puede contener (texto del PRINCIPIO-006). **Sugerida:** el primer criterio supone 2 viajes locales, pero el MVP guarda uno solo (nota de HU-001) y conservar varios es F-11 (Should). HU-012 depende de F-11 o del orden de entrega (PREG-60). Se mantiene la división en registro y asociación. |

---

## § Reglas sin HU

Reglas de 03 que no figuran en la columna RN de ninguna HU ni en sus criterios:

| RN | Prioridad | F-NN | HU sugerida o razón |
|---|---|---|---|
| RN-044 | SHOULD | F-09 | HU-013 (sugerida) |
| RN-045 | SHOULD | F-09 | HU-013 (sugerida) |
| RN-046 | SHOULD | F-07 | HU-014 (sugerida) |
| RN-047 | SHOULD | F-07 | HU-014 (sugerida) |
| RN-048 | SHOULD | F-07 | HU-014 (sugerida) |
| RN-049 | SHOULD | F-07 | HU-015 (sugerida) |
| RN-050 | SHOULD | F-07 | HU-015 (sugerida) |
| RN-051 | SHOULD | F-07 | HU-015 (sugerida) |
| RN-052 | SHOULD | F-07 | HU-015 (sugerida) |
| RN-053 | SHOULD | F-07 | HU-014 (sugerida). El PRINCIPIO-011 obliga a formatear todo monto con la configuración regional aunque F-07 no exista: los precios ≈ Estimada de HU-007 y HU-008 también deberían cumplirla. |
| RN-054 | SHOULD | F-07 | HU-014 (sugerida) |
| RN-069 | WON'T (v1) | F-06 | **Sin HU: WON'T (v1)** por la decisión vigente D-06 (reclasificada en 03 el 2026-11-07, QB-06). La app no muestra requisitos migratorios ni sanitarios (PRINCIPIO-008), y HU-008 lo deja anotado. Se reactiva si una versión posterior muestra requisitos. |
| RN-077 | SHOULD | F-11 | HU-016 (sugerida) |
| RN-094 | COULD | F-14 | **Sin HU: post-MVP.** Es Could, y el piloto se valida antes del viaje (03 Anexo B #6). Se escribe cuando F-14 entre en una iteración. |

Además, F-08 (Should) no tiene reglas propias (03 Anexo C, PREG-15), así que no se le propone HU.

**Reglas Must/Should sin cobertura:** 12 SHOULD (RN-044 a RN-054 y RN-077). Ninguna MUST queda sin HU: RN-069 es WON'T (v1) desde la revisión del 2026-11-07 (QB-06).

---

## Anexo A. HUs candidatas post-MVP (no forman parte del alcance de esta versión de 06)

> Propuesta del QA para el PO. No se insertan en el cuerpo ni en el índice. Todos sus criterios son nuevos. Los valores salen de 03. Las HUs son Should: entran en v1 después del MVP, como HU-012.

#### HU-013: Agregar una actividad propia y conservarla aunque supere el ritmo

**Prioridad:** Should · **Funcionalidad:** F-09 · **Plataformas:** web responsive (móvil 360–430 px y escritorio ≥ 1024 px) · **RN:** RN-044, RN-045

Como **viajero**, quiero agregar una actividad propia a un día y saber si con ella supero la carga de mi ritmo, para armar el plan a mi manera sin que la app me lo impida.

**Criterios de aceptación:**
- (borde) DADO QUE el ritmo es Relajado y el Día 1 suma 330 min, CUANDO agrego una actividad de 30 min (360 min en total), ENTONCES no se muestra ningún aviso (RN-044 aplica solo si se supera el límite).
- (negativo) DADO QUE el ritmo es Relajado y el Día 1 suma 330 min, CUANDO agrego una actividad de 90 min, ENTONCES se muestra "Este día suma 420 min y supera en 60 min el ritmo Relajado (360 min)."
- (positivo) DADO QUE se mostró el aviso de 420 min, CUANDO confirmo, ENTONCES el Día 1 queda con 420 min y se guarda en el dispositivo (RN-045).
- (borde) DADO QUE el ritmo es Activo y el Día 3 suma 600 min, CUANDO agrego una actividad de 1 min, ENTONCES se muestra el aviso con 601 min y 1 min de exceso sobre 600 min (PAR-05).
- (negativo) DADO QUE la actividad propia incluye un precio o un horario que escribió el viajero, CUANDO se muestra, ENTONCES `[estado de confiabilidad pendiente: PREG-68]`.

#### HU-014: Indicar un presupuesto y ver el costo estimado total del viaje

**Prioridad:** Should · **Funcionalidad:** F-07 · **Plataformas:** web responsive (móvil 360–430 px y escritorio ≥ 1024 px) · **RN:** RN-046, RN-047, RN-048, RN-053, RN-054

Como **viajero**, quiero indicar un presupuesto con su moneda y ver el costo estimado total, para saber si el plan encaja con lo que puedo gastar.

**Criterios de aceptación:**
- (positivo) DADO QUE indico un presupuesto de 1500.00 PEN, CUANDO envío la solicitud, ENTONCES se registra `{ "amount": 1500.00, "currency": "PEN" }` y el total se muestra como "Total estimado: S/ 1,320.00 · ≈ Estimada — Valor aproximado basado en … Te recomendamos confirmarlo antes de tu viaje."
- (negativo) DADO QUE indico 0.00 PEN o −200.00 PEN, CUANDO envío la solicitud, ENTONCES se rechaza el presupuesto (RN-046). `[texto pendiente: PREG-40]`
- (borde) DADO QUE indico 0.01 PEN, CUANDO envío la solicitud, ENTONCES se acepta (RN-046: solo se rechaza ≤ 0).
- (negativo) DADO QUE envío a la API un presupuesto "1500" sin moneda, CUANDO el backend valida, ENTONCES no lo interpreta como USD. `[rechazo o moneda por defecto: PREG-40]`
- (negativo) DADO QUE el costo total llega con `✓ Verificada`, CUANDO se valida, ENTONCES no se muestra así: el costo total siempre es ≈ Estimada (RN-048).
- (positivo) DADO QUE un monto en COP se convierte a PEN, CUANDO se muestra, ENTONCES se lee "COP 200,000 ≈ S/ 180.00 · ≈ Estimada — Valor aproximado basado en el tipo de cambio del 01/12/2026. Te recomendamos confirmarlo antes de tu viaje." (RN-054), formateado con `Intl.NumberFormat` y sin concatenar cadenas (RN-053).

#### HU-015: Recibir un aviso de presupuesto insuficiente y tarifas de niño solo verificadas

**Prioridad:** Should · **Funcionalidad:** F-07 · **Plataformas:** web responsive (móvil 360–430 px y escritorio ≥ 1024 px) · **RN:** RN-049, RN-050, RN-051, RN-052

Como **viajero** con menores, quiero saber si mi presupuesto puede quedarse corto, con un cálculo que no invente descuentos para niños, para decidir con datos honestos.

**Criterios de aceptación:**
- (negativo) DADO QUE el presupuesto es S/ 1,200.00 y el costo estimado es S/ 1,450.00, CUANDO veo el itinerario, ENTONCES se muestra "Tu presupuesto podría quedarse corto en S/ 250.00 · ≈ Estimada — Valor aproximado basado en el costo estimado del itinerario. Te recomendamos confirmarlo antes de tu viaje." y el itinerario se muestra completo (RN-049, RN-050).
- (borde) DADO QUE el presupuesto y el costo estimado son de S/ 1,200.00, CUANDO veo el itinerario, ENTONCES no se muestra el aviso (RN-049 aplica solo si el costo supera el presupuesto).
- (positivo) DADO QUE la tarifa de niño de un museo es S/ 15.00 ✓ Verificada (Fuente: sitio oficial del museo · Consultado el 01/12/2026 a las 10:15), CUANDO se calcula el costo, ENTONCES el menor se calcula con S/ 15.00 (RN-051).
- (negativo) DADO QUE la tarifa de niño no tiene fuente y la tarifa general es S/ 30.00 ≈ Estimada, CUANDO se calcula el costo, ENTONCES el menor se calcula con S/ 30.00 y se muestra "Tarifa de niño: ! Por confirmar — No pudimos verificar este dato. Confírmalo con el establecimiento antes de tu viaje." (RN-052).
- (negativo) DADO QUE el LLM propone "50 % de descuento para niños" sin fuente, CUANDO se calcula el costo, ENTONCES el descuento no se aplica (RN-051).
- (borde) DADO QUE la tarifa de niño ✓ Verificada se consultó hace más de 7 días, CUANDO se calcula el costo, ENTONCES ya no está ✓ Verificada (RN-068) y se usa la tarifa general (RN-051, RN-052).

#### HU-016: Conservar hasta 5 itinerarios en mi dispositivo

**Prioridad:** Should · **Funcionalidad:** F-11 · **Plataformas:** web responsive (móvil 360–430 px y escritorio ≥ 1024 px) · **RN:** RN-077

Como **invitado**, quiero conservar varios itinerarios en mi dispositivo y pasar de uno a otro, para comparar destinos sin perder los anteriores.

**Criterios de aceptación:**
- (positivo) DADO QUE tengo guardados itinerarios a Cusco, Arequipa y Bogotá, CUANDO elijo Arequipa, ENTONCES se muestra el itinerario a Arequipa y los otros dos siguen guardados.
- (negativo) DADO QUE F-11 está activa y tengo guardado un itinerario a Cusco, CUANDO genero uno a Arequipa, ENTONCES el itinerario a Cusco no se borra (contraejemplo de RN-077).
- (borde) DADO QUE tengo 4 itinerarios guardados, CUANDO genero el quinto, ENTONCES quedan 5 guardados (PAR-08).
- (borde) DADO QUE tengo 5 itinerarios guardados, CUANDO pido generar el sexto, ENTONCES `[comportamiento y texto pendientes: PREG-69]`.

---

## § Preguntas del QA

> Trazabilidad del triaje del 2026-11-07, con las mismas convenciones que la sección 4.

| PO-Qn | Tema (resumen) | Destino | Justificación |
|---|---|---|---|
| PO-Q23 | Itinerario anterior si falla por la IA la generación que lo reemplaza | C: PREG-62 nueva | RN-092 solo cubre la pérdida de conexión y PREG-37 trata el momento de la confirmación. |
| PO-Q24 | Almacenamiento local no disponible o lleno | C: PREG-63 nueva | RN-073 no cubre el caso; PREG-16 y PREG-56 tratan el borrado automático y el umbral de SUP-04, no esta falla. |
| PO-Q25 | Fuente de lugares caída o lenta al resolver el destino | C: PREG-64 nueva | RN-007 solo cubre destino ambiguo o sin resultados; PREG-50 trata los límites de la capa gratuita. |
| PO-Q26 | Franja de las 12:00 y las 18:00; actividades entre 23:00 y 06:00 | B: PREG-22 | PREG-22 pregunta los límites de las franjas y las actividades de madrugada. |
| PO-Q27 | Franja vacía y día con 0 actividades | C: PREG-65 nueva | Ninguna regla lo define; PREG-08 trata la falta de opción válida de la IA, no un día vaciado por el viajero. |
| PO-Q28 | Texto ante falla del proveedor distinta del plazo, sin secundario | C: PREG-58 nueva | RN-083 y RN-084 solo dan el texto del plazo vencido; se agrupa con PO-Q5 y PO-Q7 (textos de las operaciones de IA). |
| PO-Q29 | Registro de proveedor y versión frente a los logs mínimos | C: PREG-66 nueva | PREG-36 trata el alcance de los logs, no el choque entre PRINCIPIO-005 y PRINCIPIO-009. |
| PO-Q30 | Cierre del diálogo, corte durante la asociación, viaje de otra cuenta y sesión expirada | C: PREG-61 nueva | RN-080 y RN-081 no cubren esos casos; PREG-39 y PREG-21 tratan otros aspectos de F-10. |
| PO-Q31 | Qué cuenta como operación exitosa para PAR-07 | B: PREG-30 | PREG-30 pregunta cómo se cuenta el límite, incluido el reintento de RN-085. |
| PO-Q32 | Ventana de 24 h móvil o fija; minuto de liberación | B: PREG-30 | Es parte del conteo y la comunicación del límite que pregunta PREG-30. |
| PO-Q33 | Apertura sin conexión con itinerario guardado en el MVP | B: PREG-18 | PREG-18 pregunta qué debe funcionar sin red en el MVP, dado que F-14 es Could. |
| PO-Q34 | Momento en que se toma la "fecha actual" (envío o recepción) | C: PREG-67 nueva | PREG-23 y PREG-32 tratan la zona horaria, no el momento de evaluación. |
| PO-Q35 | Cuándo "coincide" una alternativa (RN-040) | B: PREG-29 | PREG-29 pregunta cuándo dos actividades son la misma y si se excluyen las rechazadas. |
| PO-Q36 | Orden de entrega de F-10 frente a F-11 | C: PREG-60 nueva | Ninguna PREG lo trata; se agrupa con PO-Q10 y PO-Q11 (alcance de F-10). |
| PO-Q37 | Estado de un dato factual escrito por el viajero (F-09) | C: PREG-68 nueva | PRINCIPIO-002 y 03 §2.7 solo cubren datos del LLM; no hay PREG. |
| PO-Q38 | Texto de presupuesto ≤ 0; presupuesto sin moneda | B: PREG-40 | PREG-40 pregunta qué representa el presupuesto y en qué moneda se ingresa. |
| PO-Q39 | Sexto itinerario con F-11 y 5 guardados | C: PREG-69 nueva | RN-077 no cubre el límite alcanzado y no hay PREG. |
