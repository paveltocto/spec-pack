# 09 — UI Specification · AI Travel Planner

> **Fase BMAD:** Solutioning (UX) · **Agente:** UI Designer
> **Insumos:** 00-constitucion.md (v2.0.0), 06-user-stories.md (HU-001 a HU-012), 07-architecture.md (C-01, C-03, C-18; §3 modelo de datos; §4 contratos de API; §5 flujo del invitado y asociación; ADR-004, ADR-005, ADR-007, ADR-008), 02-alcance.md, 03-reglas-de-negocio.md (RN-NNN y Anexo A, PAR-01 a PAR-11), 04-glosario.md
> **Estado:** Borrador · **Fecha:** 2026-11-07
> **Alcance de plataforma:** v1 = solo web responsive Next.js (D-10, ADR-007): viewport móvil 360–430 px y viewport de escritorio ≥ 1024 px. La app Expo es post-v1 y no se especifica aquí.

---

## 0. Convenciones

- **IDs:** pantallas `P-NNN`; flujos `UF-NN`; componentes `UI-NN`. Las reglas se citan como `RN-NNN`, los parámetros como `PAR-NN`, los principios como `PRINCIPIO-NNN`.
- **Textos:** todo texto visible sale de una clave de i18n del archivo `packages/core/src/i18n/es-419.json` (PRINCIPIO-011, RN-100, DP-08). En los wireframes el texto aparece entre comillas «…» o dentro de los recuadros; la clave está en la §9 (Catálogo de textos).
  - **Texto exacto:** copiado tal cual de 00, 03 o 06. No se puede cambiar sin cambiar la fuente.
  - **`[PROPUESTA UI · PREG-xx]`:** texto que los insumos no fijan. Es una propuesta del UI Designer, **no definitiva**, y está en la §10 (Preguntas del UI Designer). `[PROPUESTA UI]` sin PREG = no existe una PREG que lo cubra.
- **Valores:** los PAR-NN son propuestas del BA (PREG-55). La UI no escribe valores de negocio en el código: los lee de `core/src/params.json`, generado desde el backend (C-03).
- **Fechas y horas:** formato `dd/mm/aaaa` y `HH:mm` con `Intl.DateTimeFormat('es-419')` en la zona America/Lima (PREG-23, PREG-32). Montos con `Intl.NumberFormat` (RN-053, PRINCIPIO-011). Nunca concatenando cadenas.
- **Ejemplo canónico** de todo el documento (ilustrativo, ningún dato es verificado, 07 §0): Cusco, Perú · del 18/12/2026 al 21/12/2026 (4 días) · Familia · 2 adultos · 1 niño de 3–5 años · Cochecito · Equilibrado. Los horarios, precios y fuentes que aparecen son ejemplos.
- **Glifos reservados:** `✓`, `≈` y `!` solo se usan en las insignias de confiabilidad (UI-01). Ningún otro elemento de la interfaz (confirmaciones, validaciones, destino resuelto) usa esos glifos, para no confundirlos con un estado de confiabilidad.
- **"Mobile" en v1** = la web en el navegador del celular (07 §0).

---

## 1. Inventario de Pantallas

| ID | Nombre | Ruta (Next.js App Router) | Actor principal | HUs que implementa | Plataformas | Prioridad |
|---|---|---|---|---|---|---|
| P-001 | Solicitud de viaje | `/plan` | Invitado (viajero) | HU-001, HU-002, HU-003, HU-006 | web — móvil y escritorio | **Must** |
| P-002 | Generación en curso | `/plan` (estado de pantalla completa, sin ruta propia) | Invitado (viajero) | HU-004, HU-005, HU-006 | web — móvil y escritorio | **Must** |
| P-003 | Itinerario | `/itinerary` (invitado) · `/account/trips/[tripId]` (modo cuenta) | Invitado (viajero); usuario registrado en modo cuenta | HU-001, HU-004, HU-005, HU-006, HU-007, HU-008, HU-009, HU-010, HU-011 (modo cuenta: HU-012) | web — móvil y escritorio | **Must** |
| P-004 | Ingresar o crear cuenta | `/account/sign-in` | Invitado | HU-012 | web — móvil y escritorio | Should |
| P-005 | Asociar viajes a la cuenta | `/account/associate` | Usuario registrado | HU-012 | web — móvil y escritorio | Should |
| P-006 | Mis viajes | `/account/trips` | Usuario registrado | HU-012 | web — móvil y escritorio | Should |

- **Ruta `/`** (sin pantalla propia): lee `guest_trips` en C-18. Si hay un viaje guardado → P-003 (RN-078, HU-001); si está vacío → P-001 sin mensajes de error (HU-001, borde).
- **Ninguna pantalla Must exige cuenta** (RN-072). P-004, P-005 y P-006 se ocultan detrás de la bandera `features.account` mientras dure el piloto del MVP: F-10 llega en v1 después del MVP (AL-25).
- Conteo: 3 Must · 3 Should · 0 Could.

---

## 2. User Flows

Notación: `[P-NNN]` pantalla · `(UI-NN)` componente · `→` acción del usuario o del sistema · `⟶ API` llamada a un endpoint de 07 §4.

### UF-01 — Invitado planifica y guarda su primer itinerario (HU-001 a HU-009)

**Camino feliz**

1. Invitado abre `/` → C-18 vacío → `[P-001]` vacío, sin errores (HU-001).
2. Escribe «Cusco» en Destino (UI-08) y sale del campo o pulsa Enter ⟶ API `GET /v1/destinations/resolve?q=Cusco` → `status: resolved` → el campo muestra «Cusco, Perú» con el botón «Cambiar» (RN-007).
3. Elige Fecha de inicio 18/12/2026 y Fecha de fin 21/12/2026 → bajo las fechas aparece «4 días» [PROPUESTA UI].
4. Elige Tipo de grupo «Familia» (UI-09), Adultos 2, Niños 1, Niño 1 «3–5 años» (UI-07), marca «Cochecito», elige Ritmo «Equilibrado».
5. Pulsa «Generar itinerario» (UI-14) → validación de UX en el cliente (core) → C-18 guarda `pending_operation(generate)` ⟶ API `POST /v1/itineraries/generate`.
6. De 0 a 3 s: el botón queda ocupado (`aria-busy="true"`, deshabilitado) sin indicador de texto (HU-006, borde de 2 s).
7. A los 3 s sin respuesta (PAR-11, RN-101) → `[P-002]` con «Armando tu itinerario día por día…» (UI-11).
8. Respuesta 200 en ≤ 60 s (PAR-09) → C-18 escribe el viaje en una sola transacción y borra `pending_operation` → `[P-003]` con el Día 1 abierto.
9. Primer guardado en este dispositivo → (UI-16) «Tu itinerario se guardó en este dispositivo. Si borras los datos del navegador, lo perderás.» → el invitado pulsa «Entendido» [PROPUESTA UI] → C-18 marca `first_save_notice_shown = true` (RN-075). No vuelve a mostrarse.
10. En ningún paso aparece un pedido de registro (RN-072).

**Variantes de la solicitud (antes de llamar a la IA)**

- 2a. Destino ambiguo («San Juan») → `status: ambiguous` → lista de opciones «San Juan de Lurigancho (Lima, Perú)» / «San Juan (Puerto Rico)» bajo «Encontramos varios lugares con ese nombre. Elige uno:» [PROPUESTA UI]. «Generar itinerario» no envía nada hasta que elige (RN-007).
- 2b. Destino sin resultados («Qwzxy») → «No encontramos ese destino. Revisa cómo está escrito.» (RN-007).
- 2c. Fuente de lugares caída → 502 `places_unavailable` → «No pudimos buscar el destino en este momento. Inténtalo de nuevo en unos minutos.» [PROPUESTA UI · PREG-64]. No se genera nada y el texto escrito no va al LLM (RN-096).
- 5a. Error de validación (cliente o 422 del backend) → no se llama a la IA; cada mensaje aparece junto a su campo (UI-26) y el foco va al primer campo con error. Textos en la §3, P-001, estado de error.
- 5b. Con ritmo Activo y un niño de 0–2 o 3–5 años → (UI-10) aviso de RN-036 en cuanto ambos valores están elegidos, en cualquier orden; el ritmo no cambia solo (RN-037).

**Error de IA (después de llamar a la IA)**

- 8a. **Sin respuesta en 60 s** (PAR-09, RN-083) o 504 `generation_timeout` → el cliente aborta (`AbortController`) → vuelve a `[P-001]` con todos los valores (RN-088) y la alerta (UI-12) «No pudimos generar tu itinerario.» + botón «Reintentar» (texto exacto: «No pudimos generar tu itinerario. Reintentar»). «Reintentar» vuelve al paso 5.
- 8b. **502 `generation_invalid_output`** (falló también el único reintento, RN-085) → `[P-001]` con valores conservados y «No pudimos armar un itinerario válido esta vez. Tus datos siguen en el formulario.» + «Reintentar» [PROPUESTA UI · PREG-58]. No descuenta operación (RN-089).
- 8c. **503 `ai_unavailable`** (sin proveedor secundario o ambos caídos) → `[P-001]` con valores conservados y «El servicio de IA no está disponible en este momento. Tus datos siguen en el formulario.» + «Reintentar» [PROPUESTA UI · PREG-58]. No descuenta operación.
- 8d. **429 `quota_exceeded`** → `[P-001]` (o se queda en P-001 si llegó antes de los 3 s) con «Alcanzaste el límite de 15 operaciones. Podrás volver a intentarlo desde el 01/12/2026 a las 20:05.» (RN-043; `{limite}` = `error.params.limit`, fecha y hora = `error.params.retry_at` convertido a America/Lima). Sin botón «Reintentar».
- 8e. **Se corta la conexión durante la generación** → (UI-13) banner «Sin conexión. Algunas acciones no están disponibles.» → `[P-001]` con valores conservados; el viaje guardado (si existía) no cambia (RN-092). Al volver la conexión, si existe `pending_operation` → (UI-15) «Se interrumpió la generación de tu itinerario. ¿Reintentar?» [PROPUESTA UI · PREG-58] con «Reintentar» / «Ahora no» [PROPUESTA UI]. Nunca se reenvía sin confirmar (RN-093).
- 9a. **El almacenamiento local no está disponible o está lleno** → `[P-003]` muestra el itinerario igual, con la alerta «No pudimos guardar tu itinerario en este dispositivo. Puedes verlo ahora, pero se perderá al cerrar o recargar la página.» [PROPUESTA UI · PREG-63]. No se muestra el aviso de primer guardado.

### UF-02 — Invitado crea cuenta y conserva sus viajes (HU-012, Should)

Precondición: `features.account` activa; el invitado tiene viajes locales (en el MVP, 1; con F-11, hasta 5, PAR-08). El ejemplo usa 2 viajes, como HU-012: «Cusco, 2026-12-18» y «Arequipa, 2027-01-15».

1. En cualquier pantalla, el invitado pulsa «Ingresar» en el encabezado (UI-24). Es un enlace pasivo: ningún flujo de invitado lo abre por su cuenta (RN-072).
2. `[P-004]` → pestaña «Crear cuenta» → correo + contraseña → «Crear cuenta» ⟶ API `POST /v1/auth/register` → 201 + cookie `HttpOnly; Secure; SameSite=Lax` (PRINCIPIO-006). Alternativa: pestaña «Iniciar sesión» ⟶ `POST /v1/auth/login`, o botón OIDC ⟶ `GET /v1/auth/oauth/{provider}/start` (métodos: PREG-60).
3. El cliente lista los viajes locales con `associated_trip_id = null`.
   - 3a. **Lista vacía** → no se muestra ninguna pregunta de asociación → `[P-006]` (HU-012, borde).
4. `[P-005]` → (UI-22) **un viaje por vez**, «Viaje 1 de 2» [PROPUESTA UI]: «¿Guardar 'Cusco, 2026-12-18' en tu cuenta?» con «Sí, guardar en mi cuenta» y «No, dejar solo en este dispositivo» [PROPUESTA UI · PREG-61]. Ninguna opción está preseleccionada y no existe «Guardar todo» (RN-079).
5. Pulsa «Sí, guardar en mi cuenta» en Cusco ⟶ API `POST /v1/trips/associations` (un viaje, `consent.granted: true`, `consent_text_key: "account.associate.prompt"`, `trip_label: "Cusco, 2026-12-18"`) → el botón queda ocupado con «Guardando…» [PROPUESTA UI] → 201 → C-18 marca `associated_trip_id` en Cusco; la copia local se conserva (07 §5.1, paso 10) → la tarjeta muestra «Guardado en tu cuenta» [PROPUESTA UI].
6. Avanza a «Viaje 2 de 2»: «¿Guardar 'Arequipa, 2027-01-15' en tu cuenta?» → pulsa «No, dejar solo en este dispositivo» → **no se envía nada** (RN-081) → la tarjeta muestra «Se queda solo en este dispositivo» [PROPUESTA UI].
7. Resumen: «Listo. 1 viaje guardado en tu cuenta. 1 viaje sigue solo en este dispositivo.» [PROPUESTA UI] → «Ver mis viajes» → `[P-006]`.

**Variantes**

- 4a. **Cierra el diálogo sin responder** (Esc, botón «Cerrar» o salir de la ruta) → equivale a rechazar ese viaje: no se envía nada y el viaje sigue en el dispositivo (RN-080, RN-081) [PROPUESTA UI · PREG-61]. Al siguiente inicio de sesión se vuelve a preguntar por los viajes no asociados.
- 5a. **Falla de red a mitad de la asociación** (después de «Sí» en Cusco, el `POST` falla o no responde): Cusco sigue completo en el dispositivo (HU-012). La tarjeta muestra «No pudimos guardar 'Cusco, 2026-12-18' en tu cuenta. Sigue guardado en este dispositivo. Lo intentaremos de nuevo cuando vuelva la conexión.» [PROPUESTA UI · PREG-61] y aparece el banner (UI-13). El cliente guarda en memoria la cola de viajes **ya consentidos** de esta sesión; el usuario puede seguir respondiendo por Arequipa (su «No» no envía nada). Al volver la conexión, el cliente reintenta **solo** Cusco (`POST` idempotente: 201 o 200 → «Guardado en tu cuenta»). Si se cierra la pestaña antes, la cola se pierde y la pregunta por Cusco vuelve en el siguiente inicio de sesión (07 §5.2).
- 5b. **422** (atestación inválida, caso (c) de 07 §3.2) → «No pudimos guardar este viaje en tu cuenta. Sigue guardado en este dispositivo.» [PROPUESTA UI · PREG-61]. No se reintenta.
- 5c. **201 con `reliability_downgrades` > 0** → bajo «Guardado en tu cuenta»: «{n} datos pasaron a ! Por confirmar porque su vigencia venció.» [PROPUESTA UI] (RN-082: el resto conserva su fecha de consulta original).
- 5d. **401 (sesión expirada)** → `[P-004]` con «Tu sesión terminó. Vuelve a iniciar sesión para guardar tus viajes.» [PROPUESTA UI · PREG-61]; los viajes no asociados siguen en el dispositivo.
- 2a. **409 `email_taken`** → «Ya existe una cuenta con este correo. Inicia sesión.» con enlace a la pestaña «Iniciar sesión» [PROPUESTA UI · PREG-60].

### UF-03 — Ajustar una actividad: eliminar o pedir alternativa (HU-010, HU-005)

1. `[P-003]` Día 2 → tarjeta «Tarde — Museo Inka» (UI-05) → «Eliminar».
2. La actividad sale del día y se guarda en C-18 sin llamar a la API y sin consumir operaciones (RN-038, PAR-07); funciona sin conexión.
3. (UI-17) «Eliminaste "Museo Inka" del Día 2.» + «Deshacer» [PROPUESTA UI · PREG-59]. «Deshacer» la restaura en la misma franja y posición.
4. Otra tarjeta: «Día 3 · Tarde — Sacsayhuamán» → «Pedir alternativa» ⟶ API `POST /v1/itineraries/alternative` (viaje vigente completo + `target`).
5. 0–3 s: el botón queda ocupado. Más de 3 s: la tarjeta muestra (UI-11, variante en línea) «Buscando una alternativa…» [PROPUESTA UI · PREG-58]; la versión original sigue visible debajo, atenuada.
6. 200 `replaced` (≤ 30 s, PAR-09) → la alternativa reemplaza a la original en el Día 3, franja Tarde (RN-039) → C-18 guarda → el foco va al título de la nueva tarjeta y un aviso de lector de pantalla anuncia «Nueva actividad: {nombre}» [PROPUESTA UI].

**Variantes**

- 6a. 200 `no_valid_option` → «No encontramos otra actividad que encaje con tu grupo en esta franja.» (RN-108); la original se mantiene (qué más se ofrece: PREG-08).
- 6b. Sin respuesta en 30 s o 504 → «No pudimos encontrar una alternativa.» + «Reintentar» [PROPUESTA UI · PREG-58]; la original no cambia (RN-084).
- 6c. 502 / 503 → mismos textos que UF-01 8b / 8c adaptados a la actividad [PROPUESTA UI · PREG-58]; no descuenta operación.
- 6d. 429 → texto de RN-043 (como UF-01 8d) en la tarjeta; la original no cambia.
- 6e. Actividad con orientación por rango de edad (UI-23) → «Mantener» → la actividad se conserva y se guarda en C-18 (RN-035); sigue en el mismo día y franja tras recargar (HU-005).

### UF-04 — Regenerar un día (HU-011)

1. `[P-003]` Día 2 → «Regenerar Día 2» (UI-14) ⟶ API `POST /v1/itineraries/regenerate-day` (`target: {day_number: 2}`) → C-18 guarda `pending_operation(regenerate_day, day 2)`.
2. Más de 3 s → el contenido del Día 2 queda atenuado con (UI-11) «Armando una nueva versión del Día 2…» [PROPUESTA UI · PREG-58]. Los demás días siguen navegables, pero sus acciones de IA quedan deshabilitadas hasta que termine [PROPUESTA UI].
3. 200 `regenerated` (≤ 30 s) → el cliente reemplaza **solo** el Día 2 en una transacción (RN-041) → borra `pending_operation`.

**Variantes**

- 3a. Sin respuesta en 30 s o 504 → «No pudimos regenerar el Día 2.» + «Reintentar» (texto exacto de RN-084); el Día 2 conserva sus actividades (RN-087).
- 3b. 200 `no_valid_option` → «No encontramos otra versión de este día que encaje con tu grupo. El Día 2 no cambió.» [PROPUESTA UI · PREG-58] (RN-108).
- 3c. **Se pierde la conexión durante la regeneración del Día 3** → banner (UI-13); el Día 3 queda en su versión anterior completa (RN-092). Al volver la conexión → (UI-15) «Se interrumpió la regeneración del Día 3. ¿Reintentar?» (exacto, RN-093) con «Reintentar» / «Ahora no» [PROPUESTA UI]. «Ahora no» borra `pending_operation`, no envía nada y no descuenta operación.
- 3d. 429 → texto de RN-043 junto al botón; el día no cambia.

### UF-05 — Reabrir el itinerario guardado (HU-001, HU-008)

1. Invitado abre `/` con conexión → C-18 tiene un viaje → `[P-003]` directo, sin formulario (RN-078).
2. Render inmediato con el respaldo de ADR-005 punto 4: todo dato con `valid_until` < reloj del dispositivo se presenta como «! Por confirmar» (solo presentación).
3. ⟶ API `POST /v1/itineraries/reliability/refresh` (solo hechos `verified` + `country_codes`) → 200 → se aplican los estados vigentes y los enlaces actualizados del aviso migratorio (RN-107). Ningún dato sube de estado.
4. Ejemplo: horario consultado el 01/12/2026 a las 10:00, reabierto el 09/12/2026 → «! Por confirmar» (HU-008).

**Variantes**

- 3a. El refresh falla por red → se mantiene el respaldo del paso 2 y se reintenta 1 vez al volver la conexión (07 §5.2).
- 3b. El refresh responde 422 → se presentan como «! Por confirmar» **todos** los hechos enviados; ninguno como «✓ Verificada» (QB2-04). No se muestra un mensaje de error técnico.
- 3c. Si el refresh tarda más de 3 s → texto discreto bajo el encabezado «Actualizando el estado de los datos…» [PROPUESTA UI].
- 1a. Abre la app **sin conexión**: la reapertura requiere conexión en el MVP (nota de HU-001; F-14 es Could). Si el navegador llega a cargar la página, se aplica el paso 2 con el banner (UI-13). Qué debe pasar cuando la página no carga queda en PREG-18.

### UF-06 — Reemplazar el itinerario guardado por uno nuevo (HU-001, RN-076)

1. `[P-003]` → «Nuevo itinerario» → `[P-001]` vacío, con el enlace «Volver a mi itinerario a Cusco» [PROPUESTA UI].
2. Completa Arequipa → «Generar itinerario» → (UI-15) «Esto reemplazará tu itinerario a Cusco. ¿Continuar?» con «Continuar» / «Cancelar» [PROPUESTA UI].
3. «Cancelar» → no se envía ninguna operación y Cusco sigue intacto (HU-001).
4. «Continuar» → UF-01 desde el paso 5. Cusco solo se borra en la misma transacción en que se escribe Arequipa, tras un 200 (07 §3.3, regla 2). Si la generación falla por red o por la IA, Cusco queda intacto (RN-092; falla de IA: PREG-62, propuesta alineada con 07).

---

## 3. Screen Specs

### Reglas comunes a todas las pantallas

- **Layout:** móvil de 360 a 430 px = una columna, gutter 16 px. Entre 431 y 1023 px (no objetivo de v1) = layout móvil centrado con ancho máximo 640 px. Escritorio ≥ 1024 px = grilla de 12 columnas, ancho máximo de contenido 1200 px, gutter 24 px.
- **Accesibilidad (PRINCIPIO-010):** HTML semántico (`header`, `main`, `nav`, `section`, `h1`–`h3` en orden); un solo `h1` por pantalla; foco visible (UI tokens §4.6); orden de tabulación = orden visual; área táctil ≥ 48 × 48 px; todo control con etiqueta accesible; texto ampliable al 200 % sin pérdida (unidades `rem`, sin alturas fijas en contenedores de texto, sin scroll horizontal a 320 px CSS); `prefers-reduced-motion` desactiva animaciones; errores anunciados con `role="alert"` y progreso con `role="status"` (`aria-live="polite"`). axe-core sin violaciones *serious* ni *critical* (HU-007).
- **Encabezado global (UI-24)** en todas las pantallas. **Banner sin conexión (UI-13)** debajo del encabezado mientras `navigator.onLine === false` o falle un `fetch` por red (RN-091).
- **Ningún dato factual sin estado** (PRINCIPIO-002, PRINCIPIO-003): todo `FactualDatum` se pinta con UI-02. Un `value: null` se muestra como «! Por confirmar», nunca vacío (RN-071).
- **Privacidad:** ninguna pantalla pide nombre, fecha de nacimiento, foto, documento, diagnóstico ni texto libre sobre un menor (PRINCIPIO-004), ni país de residencia o nacionalidad (RN-024). Ningún correo, nombre ni token se guarda en C-18 (PRINCIPIO-006).

---

### P-001 — Solicitud de viaje (Must)

| Campo | Valor |
|---|---|
| Propósito | Capturar la solicitud de viaje (destino, fechas, perfil del grupo y ritmo) y lanzar la generación. |
| Actor | Invitado (viajero; también el usuario registrado). |
| HUs | HU-001, HU-002, HU-003, HU-006 |
| Componentes | UI-24, UI-13, UI-08, UI-09, UI-07, UI-10, UI-26, UI-14, UI-12, UI-15, UI-20, UI-21 |
| Endpoints | `GET /v1/destinations/resolve` (§4.1) · `POST /v1/itineraries/generate` (§4.2) · `GET /v1/quota` (§4.6, para UI-21) |
| RN | RN-001 a RN-016, RN-036, RN-037, RN-043, RN-072, RN-076, RN-083, RN-088, RN-089, RN-090, RN-091, RN-093, RN-095, RN-096, RN-101 |

**Datos que muestra**

| Dato | Origen | Detalle |
|---|---|---|
| Destino resuelto | `GET /destinations/resolve` → `candidates[].display_name` | p. ej. «Cusco, Perú». Se envía solo `place_ref` (RN-096). |
| Límites de fechas | `core/params.json` (PAR-01, PAR-02) + fecha actual en America/Lima | `start` mín. = hoy; máx. = hoy + 365 días. `end` mín. = `start`; máx. = `start` + 13 días. |
| Duración del viaje | Calculada en el cliente solo para UX | «4 días» [PROPUESTA UI]. |
| Tipos de grupo | Enum `GroupType` | Solo · Pareja · Amigos · Familia |
| Rangos de edad | Enum `AgeRange` | «0–2 años», «3–5 años», «6–11 años», «12–17 años» (exactos, HU-003) |
| Necesidades del grupo | Enum `GroupNeed` (catálogo: PREG-25) | Cochecito · Movilidad reducida · Restricciones alimentarias · Menor exigencia física |
| Ritmos | Enum `Pace` + PAR-05 | Relajado (hasta 360 min por día) · Equilibrado (hasta 480 min) · Activo (hasta 600 min) — sufijos [PROPUESTA UI] |
| Operaciones disponibles | `GET /v1/quota` | UI-21: «Te quedan 14 de 15 operaciones de IA» [PROPUESTA UI · PREG-58] |

**Acciones**

| Acción | Control | Resultado |
|---|---|---|
| Resolver destino | Salir del campo Destino o pulsar Enter (≥ 2 caracteres) | `GET /destinations/resolve`. No se llama mientras se escribe, para no agotar la capa gratuita de la fuente de lugares [PROPUESTA UI]. |
| Elegir entre destinos | Radio en la lista de candidatos | Fija `place_ref`. |
| Cambiar destino | Botón «Cambiar» | Vuelve al campo de texto con el valor anterior. |
| Sumar / restar adultos y niños | Stepper − / + (48 × 48 px) | Adultos 1–10, Niños 0–9. Al sumar un niño aparece «Niño N» con su selector de rango (UI-07). |
| Generar | «Generar itinerario» | Si hay viaje guardado → diálogo de reemplazo (UF-06); si no → UF-01 paso 5. |
| Volver al itinerario | Enlace «Volver a mi itinerario a {destino}» (solo si hay viaje guardado) | P-003 sin cambios. |

**Wireframe — móvil (360–430 px)**

```
┌──────────────────────────────────────┐
│ [≡ logo] AI Travel Planner  Ingresar │ UI-24 · alto 56 px · "Ingresar" solo si features.account
├──────────────────────────────────────┤
│ (UI-13 banner sin conexión, si aplica)│
├──────────────────────────────────────┤
│ ← Volver a mi itinerario a Cusco      │ enlace, solo si hay viaje guardado
│                                      │
│ h1  Planifica tu viaje               │ [PROPUESTA UI] 24/32 bold
│ p   Sin crear cuenta. Lo guardamos   │ [PROPUESTA UI] 16/24 texto secundario
│     en este dispositivo.             │
│                                      │
│ ┌ Destino ─────────────────────────┐ │ label 16 semibold
│ │ [📍 Cusco                      ] │ │ input 48 px alto, ancho 100 %
│ └──────────────────────────────────┘ │
│   (resuelto:)                         │
│   ┌──────────────────────────────┐   │ tarjeta destino resuelto, borde 1 px
│   │ 📍 Cusco, Perú     [Cambiar] │   │ "Cambiar" botón texto 48 px
│   └──────────────────────────────┘   │
│                                      │
│ ┌ Fechas ──────────────────────────┐ │ fieldset, legend "Fechas"
│ │ Fecha de inicio                  │ │ input type=date, 48 px
│ │ [ 18/12/2026              📅 ]   │ │
│ │ Fecha de fin                     │ │
│ │ [ 21/12/2026              📅 ]   │ │
│ │ 4 días                           │ │ texto secundario 14/20
│ └──────────────────────────────────┘ │
│                                      │
│ ┌ Tipo de grupo ───────────────────┐ │ fieldset + radios (UI-09) en grilla 2×2
│ │ [ Solo   ] [ Pareja ]            │ │ cada opción 48 px alto, ancho 50 %
│ │ [ Amigos ] [●Familia]            │ │ seleccionada: fondo primario sutil + borde 2 px
│ └──────────────────────────────────┘ │
│                                      │
│ ┌ ¿Quiénes viajan? ────────────────┐ │ fieldset
│ │ Adultos (18 años o más)  [−] 2 [+]│ │ stepper; valor anunciado por aria-live
│ │ Niños (0 a 17 años)      [−] 1 [+]│ │
│ │ ┌ Niño 1 ──────────────────────┐ │ │ fieldset por menor (UI-07)
│ │ │ ( )0–2 años  (●)3–5 años     │ │ │ radios en grilla 2×2, 48 px
│ │ │ ( )6–11 años ( )12–17 años   │ │ │
│ │ └──────────────────────────────┘ │ │
│ │ Solo te pedimos el rango de edad │ │ texto de ayuda 14/20 [PROPUESTA UI]
│ │ de cada niño.                    │ │
│ └──────────────────────────────────┘ │
│                                      │
│ ▸ Necesidades del grupo (opcional)   │ <details> 48 px; abierto si hay alguna marcada
│   [☑] Cochecito                      │ checkbox 24 px dentro de fila de 48 px
│   [ ] Movilidad reducida             │
│   [ ] Restricciones alimentarias     │
│   [ ] Menor exigencia física         │
│                                      │
│ ┌ Ritmo ───────────────────────────┐ │ fieldset + radios (UI-09) apilados
│ │ ( ) Relajado                     │ │ título 16 semibold
│ │     Hasta 360 min por día        │ │ ayuda 14/20
│ │ (●) Equilibrado                  │ │
│ │     Hasta 480 min por día        │ │
│ │ ( ) Activo                       │ │
│ │     Hasta 600 min por día        │ │
│ └──────────────────────────────────┘ │
│ (UI-10 aviso RN-036, si aplica)       │
│                                      │
│ (UI-12 alerta de error, si aplica)    │
│                                      │
│ [      Generar itinerario      ]     │ UI-14 primario, ancho 100 %, 52 px alto
│ Te quedan 14 de 15 operaciones de IA │ UI-21, 14/20, centrado
│                                      │
│ ▸ ¿Qué significan ✓, ≈ y !?          │ UI-20 (leyenda), cerrada por defecto
└──────────────────────────────────────┘
```

**Wireframe — escritorio (≥ 1024 px)**

```
┌───────────────────────────────────────────────────────────────────────────────────────┐
│ [logo] AI Travel Planner                                                    Ingresar   │ UI-24 · 64 px
├───────────────────────────────────────────────────────────────────────────────────────┤
│ (UI-13 banner, si aplica — ancho completo)                                             │
├───────────────────────────────────────────────────────────────────────────────────────┤
│  ← Volver a mi itinerario a Cusco                                                      │
│                                                                                        │
│  ┌──────────── columnas 1–7 (formulario, máx. 640 px) ─────┐ ┌── columnas 8–12 ───────┐│
│  │ h1 Planifica tu viaje  (30/36 bold)                     │ │ Cómo funciona          ││ h2 20/28
│  │ Sin crear cuenta. Lo guardamos en este dispositivo.     │ │ 1. Cuéntanos tu viaje. ││ lista [PROPUESTA UI]
│  │                                                         │ │ 2. La IA arma un plan  ││
│  │ Destino                                                 │ │    día por día.        ││
│  │ [📍 Cusco, Perú                           ] [Cambiar]   │ │ 3. Revisa los datos    ││
│  │                                                         │ │    marcados antes de   ││
│  │ Fechas                                                  │ │    viajar.             ││
│  │ Fecha de inicio [18/12/2026 📅]  Fecha de fin [21/12/2026 📅]  4 días │             ││ dos inputs en fila
│  │                                                         │ │ ┌ UI-20 leyenda ─────┐ ││ abierta por defecto
│  │ Tipo de grupo                                           │ │ │ ✓ Verificada  …    │ ││ en escritorio
│  │ [ Solo ] [ Pareja ] [ Amigos ] [●Familia ]              │ │ │ ≈ Estimada    …    │ ││
│  │                                                         │ │ │ ! Por confirmar …  │ ││
│  │ ¿Quiénes viajan?                                        │ │ └────────────────────┘ ││
│  │ Adultos (18 años o más) [−] 2 [+]   Niños (0 a 17 años) [−] 1 [+] │                 ││
│  │ Niño 1: ( )0–2 años (●)3–5 años ( )6–11 años ( )12–17 años        │                 ││ radios en una fila
│  │ Solo te pedimos el rango de edad de cada niño.          │ │ No te pedimos nombres  ││ nota de privacidad
│  │                                                         │ │ ni fechas de           ││ [PROPUESTA UI]
│  │ ▸ Necesidades del grupo (opcional)                      │ │ nacimiento de los      ││
│  │   [☑] Cochecito  [ ] Movilidad reducida                 │ │ niños.                 ││
│  │   [ ] Restricciones alimentarias  [ ] Menor exigencia física │                     ││ grilla 2 columnas
│  │                                                         │ │                        ││
│  │ Ritmo                                                   │ │                        ││
│  │ [ Relajado          ] [●Equilibrado       ] [ Activo           ] │                 ││ 3 tarjetas radio
│  │   Hasta 360 min/día     Hasta 480 min/día    Hasta 600 min/día   │                 ││ en fila, 96 px alto
│  │ (UI-10, si aplica)                                      │ │                        ││
│  │ (UI-12, si aplica)                                      │ │                        ││
│  │ [ Generar itinerario ]   Te quedan 14 de 15 operaciones de IA    │                 ││ botón ancho auto
│  └─────────────────────────────────────────────────────────┘ └────────────────────────┘│ mín. 240 px
└───────────────────────────────────────────────────────────────────────────────────────┘
```

**Estados**

| Estado | Comportamiento y textos |
|---|---|
| **Vacío** | Primera visita o C-18 vacío: todos los campos vacíos salvo Adultos = 1 y Niños = 0 [PROPUESTA UI]; ningún tipo de grupo ni ritmo preseleccionado (el viajero elige exactamente uno, RN-008, RN-016). Sin mensajes de error (HU-001). |
| **Cargando** | (a) Resolviendo destino: spinner de 20 px dentro del campo + `aria-busy`; si tarda más de 3 s, texto «Buscando el destino…» [PROPUESTA UI · PREG-64]. (b) Generando, 0–3 s: botón «Generar itinerario» deshabilitado con `aria-busy="true"`, sin texto de progreso (borde de 2 s de HU-006). (c) A los 3 s (PAR-11) → P-002. |
| **Con datos** | Formulario completo o repoblado tras un error (RN-088): Cusco, 18/12/2026 a 21/12/2026, Familia, 2 adultos, Niño 1 3–5 años, Cochecito, Equilibrado. |
| **Error de validación (cliente o 422)** | Mensaje bajo cada campo (UI-26), foco al primer error, resumen `role="alert"` arriba del botón: «Revisa los campos marcados.» [PROPUESTA UI · PREG-57]. Textos por campo: ver tabla siguiente. |
| **Error de IA** | (UI-12) sobre el botón, con los valores conservados: 60 s / 504 → «No pudimos generar tu itinerario.» + «Reintentar» (RN-083); 502 → «No pudimos armar un itinerario válido esta vez. Tus datos siguen en el formulario.» + «Reintentar» [PROPUESTA UI · PREG-58]; 503 → «El servicio de IA no está disponible en este momento. Tus datos siguen en el formulario.» + «Reintentar» [PROPUESTA UI · PREG-58]. |
| **429 límite de operaciones** | (UI-12, variante sin «Reintentar»): «Alcanzaste el límite de 15 operaciones. Podrás volver a intentarlo desde el 01/12/2026 a las 20:05.» (RN-043). UI-21 muestra «Te quedan 0 de 15 operaciones de IA» [PROPUESTA UI · PREG-58]. El botón sigue habilitado: el backend vuelve a rechazar con 429 sin llamar al proveedor (07 §4.6). |
| **Sin conexión** | Banner UI-13 «Sin conexión. Algunas acciones no están disponibles.» (RN-091). «Generar itinerario» deshabilitado (`aria-disabled="true"`) con el texto bajo el botón «Necesitas conexión para generar» [PROPUESTA UI · PREG-58] (RN-090). La resolución del destino no se lanza; bajo el campo: «Necesitas conexión para buscar el destino» [PROPUESTA UI · PREG-58]. El formulario sigue editable. |
| **Operación interrumpida** | Al volver la conexión con `pending_operation(generate)`: UI-15 «Se interrumpió la generación de tu itinerario. ¿Reintentar?» [PROPUESTA UI · PREG-58] (RN-093). |

**Textos de validación de P-001**

| Caso (RN) | Campo | Texto | Origen |
|---|---|---|---|
| Destino vacío (RN-001) | Destino | «Indica un destino para continuar» | Exacto, HU-002 |
| Destino sin resultados (RN-007) | Destino | «No encontramos ese destino. Revisa cómo está escrito.» | Exacto, HU-002 |
| Destino escrito sin resolver/elegir (RN-007) | Destino | «Elige una de las opciones para continuar» | [PROPUESTA UI] |
| Fuente de lugares caída (502) | Destino | «No pudimos buscar el destino en este momento. Inténtalo de nuevo en unos minutos.» | [PROPUESTA UI · PREG-64] |
| Fecha inexistente (RN-002) | La fecha | «La fecha 2027-02-30 no existe» | Exacto, HU-002 (formato de fecha: ver §10, P-Q03) |
| Fin antes del inicio (RN-003) | Fecha de fin | «La fecha de fin debe ser igual o posterior a la fecha de inicio» | Exacto, HU-002 |
| Inicio anterior a hoy (RN-004) | Fecha de inicio | «La fecha de inicio no puede ser anterior a hoy (07/11/2026).» | [PROPUESTA UI · PREG-57] |
| Más de 14 días (RN-005) | Fecha de fin | «El máximo es 14 días por itinerario» | Exacto, HU-002 |
| Inicio a más de 365 días (RN-006) | Fecha de inicio | «Puedes planificar viajes que empiecen hasta el 2027-11-07» | Exacto, HU-002 (formato: §10, P-Q03) |
| Sin tipo de grupo (RN-008) | Tipo de grupo | «Elige un tipo de grupo» | [PROPUESTA UI · PREG-57] |
| 0 adultos (RN-009) | Adultos | «El grupo debe incluir al menos un adulto» | Exacto, HU-003 |
| Más de 10 viajeros (RN-010) | ¿Quiénes viajan? | «El grupo puede tener como máximo 10 viajeros» | [PROPUESTA UI · PREG-57] |
| Familia sin menores (RN-011) | Tipo de grupo | «Agrega al menos un menor o cambia el tipo de grupo a Pareja o Amigos» | Exacto, HU-003 |
| Solo ≠ 1 adulto y 0 menores (RN-011) | Tipo de grupo | «Solo es para 1 adulto sin niños. Cambia el tipo de grupo o los viajeros.» | [PROPUESTA UI · PREG-57] |
| Pareja ≠ 2 adultos y 0 menores (RN-011) | Tipo de grupo | «Pareja es para 2 adultos sin niños. Cambia el tipo de grupo o los viajeros.» | [PROPUESTA UI · PREG-57] |
| Amigos fuera de 2–10 adultos o con menores (RN-011) | Tipo de grupo | «Amigos es para 2 a 10 adultos sin niños. Si viajan niños, elige Familia.» | [PROPUESTA UI · PREG-57] |
| Menor sin rango (RN-012) | Niño N | «Elige el rango de edad del Niño 1» | [PROPUESTA UI · PREG-57] |
| Sin ritmo (RN-016) | Ritmo | «Elige un ritmo» | [PROPUESTA UI · PREG-57] |
| 422 no mapeado a un campo (`field_not_allowed`, otros) | Resumen | «No pudimos procesar tu solicitud. Revisa los datos e inténtalo de nuevo.» | [PROPUESTA UI · PREG-57] |

**Aviso de RN-036 (UI-10)** — exacto: «Con ritmo Activo cada día puede sumar hasta 600 min de actividades y traslados. Con niños de 3–5 años quizá prefieras Equilibrado (480 min).» Variante con un menor de 0–2 años: PREG-26 (hasta que se resuelva, se muestra el mismo texto).

---

### P-002 — Generación en curso (Must)

| Campo | Valor |
|---|---|
| Propósito | Informar que el itinerario se está generando cuando la operación supera 3 s, sin dejar al viajero sin respuesta. |
| Actor | Invitado (viajero). |
| HUs | HU-004, HU-005, HU-006 |
| Componentes | UI-24, UI-13, UI-11 |
| Endpoints | `POST /v1/itineraries/generate` (en curso, iniciado en P-001) |
| RN | RN-083, RN-089, RN-092, RN-101; RN-088 al salir con error |

**Datos que muestra:** resumen de la solicitud en una línea (sin datos factuales): «Cusco, Perú · 18/12/2026 – 21/12/2026 · Familia · Equilibrado» [PROPUESTA UI].

**Acciones:** ninguna en v1 (cancelar abortaría el `fetch` mientras el servidor quizá completa y descuenta la operación, 07 §5.2, ARQ-Q-09; ver §10, P-Q08).

**Wireframe — móvil**

```
┌──────────────────────────────────────┐
│ [logo] AI Travel Planner             │ UI-24 (sin "Ingresar" durante la carga)
├──────────────────────────────────────┤
│                                      │
│              (ilustración)           │ SVG decorativo 120 px, aria-hidden
│                                      │
│  Armando tu itinerario día por día…  │ UI-11, h1 visual 20/28 semibold, role=status
│                                      │
│  ▓▓▓▓▓▓░░░░░░░░░░░░░░░░░░░░░░░░░     │ barra indeterminada 4 px, sin % (no hay avance real)
│                                      │
│  Cusco, Perú · 18/12/2026 – 21/12/2026│ 14/20 texto secundario
│  Familia · Equilibrado               │
│                                      │
│  (a los 20 s:) Esto puede tardar     │ [PROPUESTA UI · PREG-58] 14/20
│  hasta un minuto.                    │
└──────────────────────────────────────┘
```

**Wireframe — escritorio:** mismo contenido centrado en una tarjeta de 560 px de ancho, a 96 px del encabezado; título 24/32.

**Estados**

| Estado | Comportamiento |
|---|---|
| Vacío | No aplica: P-002 solo existe con una generación en curso. |
| Cargando | Estado principal, desde los 3 s (PAR-11) hasta la respuesta o los 60 s (PAR-09). A los 20 s se agrega «Esto puede tardar hasta un minuto.» [PROPUESTA UI · PREG-58]. Animación desactivada con `prefers-reduced-motion` (la barra queda estática con el texto). |
| Con datos | 200 → C-18 escribe → P-003. |
| Error | 504 / 60 s, 502, 503, 429 → vuelve a P-001 con valores conservados (RN-088) y la alerta correspondiente (tabla de P-001). 422 no llega aquí (se decide antes de llamar al proveedor), pero si llegara → P-001 con errores por campo. |
| Sin conexión | El `fetch` falla → P-001 con valores, banner UI-13 y, al volver la conexión, el diálogo de operación interrumpida (UF-01 8e). |

---

### P-003 — Itinerario (Must)

| Campo | Valor |
|---|---|
| Propósito | Mostrar el itinerario por días y franjas, con cada dato factual y su estado de confiabilidad, los avisos obligatorios y los ajustes esenciales. |
| Actor | Invitado (viajero). En modo cuenta: usuario registrado (HU-012). |
| HUs | HU-001, HU-004, HU-005, HU-006, HU-007, HU-008, HU-009, HU-010, HU-011; modo cuenta: HU-012 |
| Componentes | UI-24, UI-13, UI-19, UI-03, UI-04, UI-16, UI-18, UI-05, UI-06, UI-01, UI-02, UI-23, UI-14, UI-11, UI-12, UI-15, UI-17, UI-20, UI-21, UI-25 |
| Endpoints | `POST /v1/itineraries/reliability/refresh` (§4.5) · `POST /v1/itineraries/alternative` (§4.3) · `POST /v1/itineraries/regenerate-day` (§4.4) · `GET /v1/quota` (§4.6) · modo cuenta: `GET /v1/trips/{trip_id}` (§4.9) |
| RN | RN-017 a RN-020, RN-024, RN-026, RN-030 a RN-035, RN-038 a RN-043, RN-060 a RN-068, RN-070 a RN-073, RN-075, RN-078, RN-084, RN-087, RN-089 a RN-093, RN-100, RN-101, RN-107, RN-108 |

**Datos que muestra** (de `Itinerary`, `Notices` y `quota`, 07 §3.2)

| Bloque | Campos | Presentación |
|---|---|---|
| Encabezado (UI-19) | `destination.display_name`, `start_date`, `end_date`, días, `trip_request.group_type`, `adults`, `children_age_ranges`, `group_needs`, `pace` | «Cusco, Perú» · «18/12/2026 – 21/12/2026 · 4 días» · «Familia · 2 adultos · 1 niño (3–5 años) · Cochecito · Equilibrado» |
| Aviso de sugerencia (UI-03) | `notices.suggestion.message_key` | Texto exacto de RN-026. |
| Aviso migratorio (UI-04) | `notices.immigration` (`message_key`, `links[]`) | Texto exacto de RN-024 + enlaces de la lista curada. |
| Días (UI-18) | `days[].day_number`, `date` | «Día 1 · vie 18/12» (pestaña) / «Día 1 · viernes 18/12/2026» (título). |
| Ritmo del día | `pace`, `load_limit_min` | «Ritmo Equilibrado: hasta 480 min de actividades y traslados por día» [PROPUESTA UI]. `daily_load_min` no se muestra (§10, P-Q10). |
| Franjas | `activities[].slot` | Encabezados «Mañana · 06:00–12:00», «Tarde · 12:00–18:00», «Noche · 18:00–23:00» (PAR-04), en ese orden (RN-019). Una franja sin actividades no se muestra (HU-007, borde) [PROPUESTA UI · PREG-65]. |
| Actividad (UI-05) | `kind`, `name`, `description`, `place.existence`, `duration_min`, `opening_hours`, `price`, `min_age`, `min_age_notice`, `accessibility`, `age_guidance` | Ver UI-05. Cada `FactualDatum` con UI-02. |
| Traslado (UI-06) | `travel_from_previous_min` | Fila entre dos tarjetas. |
| Operaciones (UI-21) | `quota.remaining`, `quota.limit` | «Te quedan 13 de 15 operaciones de IA» [PROPUESTA UI · PREG-58]. |

**Ejemplo de render del Día 1** (datos ilustrativos de 07 §4.2; checked_at 17:40Z = 12:40 en Lima)

- **Mañana · 06:00–12:00**
  - **Qorikancha** — Templo inca sobre el que se levantó el convento de Santo Domingo.
    - Duración: 90 min · `≈ Estimada` — Valor aproximado basado en la duración habitual de actividades similares. Te recomendamos confirmarlo antes de tu viaje.
    - Horario: 08:30–17:30 · `✓ Verificada` — Fuente: sitio oficial del recinto · Consultado el 07/11/2026 a las 12:40
    - Precio: `! Por confirmar` — No pudimos verificar este dato. Confírmalo con el establecimiento antes de tu viaje.
    - Lugar: `! Por confirmar` — No pudimos verificar este dato. Confírmalo con la oficina de turismo local antes de tu viaje.
    - [Pedir alternativa] [Eliminar]
- Traslado: 20 min · `≈ Estimada` — Valor aproximado basado en la distancia entre los puntos. Te recomendamos confirmarlo antes de tu viaje.
- **Tarde · 12:00–18:00**
  - **Descanso en el alojamiento** (bloque de descanso) — Pausa para la siesta y la comida del menor.
    - Duración: 90 min · `≈ Estimada` — Valor aproximado basado en la pausa recomendada para niños pequeños. Te recomendamos confirmarlo antes de tu viaje.
    - [Eliminar] (sin «Pedir alternativa»: es un bloque de descanso, §10, P-Q11)

Los textos de `{base_de_cálculo}` («la duración habitual de actividades similares», «la pausa recomendada para niños pequeños», «la distancia entre los puntos») son [PROPUESTA UI] para las claves `basis.*` de 07; «el establecimiento» y «la oficina de turismo local» son exactos (07 §3.2, HU-009).

**Acciones**

| Acción | Control | IA / cupo | Sin conexión | Resultado |
|---|---|---|---|---|
| Cambiar de día | Pestaña (móvil) / ítem del riel (escritorio) de UI-18 | No | Disponible | Muestra el día; foco en el título del día. |
| Eliminar actividad | «Eliminar» en UI-05 | No / no cuenta | **Disponible** | Local (C-03 + C-18), UI-17 «Deshacer» (UF-03). |
| Deshacer eliminación | «Deshacer» en UI-17 | No | Disponible | Restaura la actividad en su franja y posición. |
| Pedir alternativa | «Pedir alternativa» en UI-05 | Sí / cuenta si tiene éxito | Deshabilitado: «Necesitas conexión para pedir una alternativa» [PROPUESTA UI · PREG-58] | UF-03. |
| Mantener (orientación por edad) | «Mantener» en UI-23 | No | Disponible | La actividad se conserva (RN-035). |
| Regenerar día | «Regenerar Día N» en la cabecera del día | Sí / cuenta si tiene éxito | Deshabilitado: «Necesitas conexión para regenerar» (exacto, RN-090) | UF-04. |
| Nuevo itinerario | «Nuevo itinerario» en UI-19 | — | Navega; «Generar» queda deshabilitado en P-001 | UF-06. |
| Abrir enlace oficial | Enlace en UI-04 | No | Disponible (el navegador decide) | Abre en pestaña nueva con `rel="noopener noreferrer"` y aviso «(se abre en otra pestaña)» para lectores de pantalla [PROPUESTA UI]. |

**Wireframe — móvil (360–430 px)**

```
┌──────────────────────────────────────┐
│ [logo] AI Travel Planner   Ingresar  │ UI-24
├──────────────────────────────────────┤
│ (UI-13 banner sin conexión)           │ sticky bajo el encabezado
├──────────────────────────────────────┤
│ (UI-16) ┌──────────────────────────┐ │ solo el primer guardado (RN-075)
│         │ ⓘ Tu itinerario se guardó│ │ fondo info, role=status
│         │ en este dispositivo. Si  │ │
│         │ borras los datos del     │ │
│         │ navegador, lo perderás.  │ │
│         │            [Entendido]   │ │ botón 48 px
│         └──────────────────────────┘ │
│ h1 Cusco, Perú                       │ UI-19 · 24/32 bold
│ 18/12/2026 – 21/12/2026 · 4 días     │ 16/24
│ Familia · 2 adultos · 1 niño         │ 14/20 secundario
│ (3–5 años) · Cochecito · Equilibrado │
│ [Nuevo itinerario]                   │ botón secundario 48 px
│                                      │
│ (UI-03) ┌──────────────────────────┐ │ fondo aviso, borde izq. 4 px acento
│         │ Este itinerario es una   │ │
│         │ sugerencia. Revisa los   │ │ los glifos ≈ y ! van con su
│         │ datos marcados como      │ │ color e insignia en línea (UI-01 compacto)
│         │ ≈ Estimada o             │ │
│         │ ! Por confirmar antes de │ │
│         │ tu viaje.                │ │
│         └──────────────────────────┘ │
│ (UI-04) ┌──────────────────────────┐ │ fondo info, ícono pasaporte aria-hidden
│         │ 🛂 Los requisitos de     │ │
│         │ ingreso pueden cambiar.  │ │
│         │ Consúltalos en la fuente │ │
│         │ oficial antes de tu viaje│ │
│         │ → Superintendencia       │ │ enlace subrayado, 48 px alto mín.
│         │   Nacional de Migraciones│ │
│         │   (Perú) ↗               │ │
│         └──────────────────────────┘ │
│                                      │
│ ┌Día 1 ┐┌Día 2 ┐┌Día 3 ┐┌Día 4 ┐ →   │ UI-18 tablist, scroll horizontal, sticky
│ │vie   ││sáb   ││dom   ││lun   │     │ cada pestaña 72×56 px
│ │18/12 ││19/12 ││20/12 ││21/12 │     │ activa: borde inferior 3 px primario + negrita
│ └──────┘└──────┘└──────┘└──────┘     │
│                                      │
│ h2 Día 1 · viernes 18/12/2026        │ 20/28 semibold
│ Ritmo Equilibrado: hasta 480 min de  │ 14/20 secundario
│ actividades y traslados por día      │
│ [↻ Regenerar Día 1]                  │ UI-14 secundario, ancho 100 %, 48 px
│                                      │
│ h3 Mañana · 06:00–12:00              │ 16/24 semibold, separador superior
│ ┌──────────────────────────────────┐ │ UI-05 tarjeta, padding 16, radio 12
│ │ 🏛 Qorikancha                    │ │ h4 18/28 semibold; ícono por kind
│ │ Templo inca sobre el que se      │ │ 16/24
│ │ levantó el convento de Santo     │ │
│ │ Domingo.                         │ │
│ │ ──────────────────────────────── │ │
│ │ Duración: 90 min                 │ │ UI-02: etiqueta+valor 16 semibold
│ │ [≈ Estimada]                     │ │ UI-01 insignia
│ │ Valor aproximado basado en la    │ │ texto obligatorio 14/20 secundario
│ │ duración habitual de actividades │ │
│ │ similares. Te recomendamos       │ │
│ │ confirmarlo antes de tu viaje.   │ │
│ │                                  │ │
│ │ Horario: 08:30–17:30             │ │
│ │ [✓ Verificada]                   │ │
│ │ Fuente: sitio oficial del recinto│ │ el nombre de la fuente es enlace
│ │ · Consultado el 07/11/2026 a las │ │ si source.url existe
│ │ 12:40                            │ │
│ │                                  │ │
│ │ Precio:                          │ │ valor null → solo la insignia
│ │ [! Por confirmar]                │ │
│ │ No pudimos verificar este dato.  │ │
│ │ Confírmalo con el establecimiento│ │
│ │ antes de tu viaje.               │ │
│ │                                  │ │
│ │ Lugar:                           │ │
│ │ [! Por confirmar]                │ │
│ │ No pudimos verificar este dato.  │ │
│ │ Confírmalo con la oficina de     │ │
│ │ turismo local antes de tu viaje. │ │
│ │ ──────────────────────────────── │ │
│ │ [Pedir alternativa] [ Eliminar ] │ │ dos botones 48 px, 50 % c/u
│ └──────────────────────────────────┘ │
│   ┆ 🚶 Traslado: 20 min              │ UI-06, línea punteada vertical a la izq.
│   ┆ [≈ Estimada] Valor aproximado    │
│   ┆ basado en la distancia entre los │
│   ┆ puntos. Te recomendamos          │
│   ┆ confirmarlo antes de tu viaje.   │
│ h3 Tarde · 12:00–18:00               │
│ ┌──────────────────────────────────┐ │ UI-05 variante descanso: fondo superficie
│ │ ☾ Descanso en el alojamiento     │ │ atenuada, etiqueta "Bloque de descanso"
│ │ Bloque de descanso               │ │ 14/20 [PROPUESTA UI]
│ │ Pausa para la siesta y la comida │ │
│ │ del menor.                       │ │
│ │ Duración: 90 min [≈ Estimada]    │ │
│ │ Valor aproximado basado en la    │ │
│ │ pausa recomendada para niños     │ │
│ │ pequeños. Te recomendamos        │ │
│ │ confirmarlo antes de tu viaje.   │ │
│ │                     [ Eliminar ] │ │
│ └──────────────────────────────────┘ │
│                                      │
│ Te quedan 13 de 15 operaciones de IA │ UI-21
│ ▸ ¿Qué significan ✓, ≈ y !?          │ UI-20
│                                      │
│ (UI-17 snackbar, fijo abajo, 16 px   │
│  de margen, si aplica)               │
└──────────────────────────────────────┘
```

**Tarjeta con edad mínima y orientación por edad (ejemplo de HU-005, Día 2)**

```
┌──────────────────────────────────┐
│ 🎨 Taller de cerámica            │
│ Taller para hacer una pieza de   │
│ cerámica con técnicas andinas.   │
│ Edad mínima: 8 años              │ UI-02
│ [✓ Verificada]                   │
│ Fuente: {fuente} · Consultado el │
│ {dd/mm/aaaa} a las {HH:mm}       │
│ Comprueba que cada menor la      │ min_age_notice (exacto, RN-031), 14/20 negrita
│ cumple.                          │
│ ┌ (UI-23) ─────────────────────┐ │ fondo neutro, ícono 💡 aria-hidden
│ │ Sugerencia: esta caminata de │ │ "Sugerencia: {message_text} Tú decides
│ │ 180 min puede resultar       │ │  si la mantienes." (RN-034)
│ │ exigente para niños de 3–5   │ │
│ │ años. Tú decides si la       │ │
│ │ mantienes.                   │ │
│ │ [ Mantener ]                 │ │ tras pulsar: "Decidiste mantener esta
│ └──────────────────────────────┘ │  actividad." [PROPUESTA UI]
│ [Pedir alternativa] [ Eliminar ] │
└──────────────────────────────────┘
```

(El texto de la orientación del ejemplo es el de RN-034 y corresponde a una caminata; en la tarjeta real `message_text` es el que corresponda a esa actividad.)

**Wireframe — escritorio (≥ 1024 px)**

```
┌──────────────────────────────────────────────────────────────────────────────────────────┐
│ [logo] AI Travel Planner                                                       Ingresar  │ UI-24
├──────────────────────────────────────────────────────────────────────────────────────────┤
│ (UI-13 banner)                                                                           │
├──────────────────────────────────────────────────────────────────────────────────────────┤
│ (UI-16 aviso de primer guardado, ancho de contenido, botón "Entendido" a la derecha)    │
│                                                                                          │
│ h1 Cusco, Perú (30/36)                                              [Nuevo itinerario]   │ UI-19
│ 18/12/2026 – 21/12/2026 · 4 días · Familia · 2 adultos · 1 niño (3–5 años) · Cochecito · Equilibrado │
│                                                                                          │
│ ┌ col 1–3 (240 px) ┐ ┌ col 4–9 (contenido del día) ──────────────┐ ┌ col 10–12 (320 px) ┐│
│ │ nav "Días"       │ │ h2 Día 1 · viernes 18/12/2026             │ │ (UI-03) Este       ││ riel derecho sticky
│ │ ┌──────────────┐ │ │ Ritmo Equilibrado: hasta 480 min de       │ │ itinerario es una  ││ (top: 80 px)
│ │ │▌Día 1        │ │ │ actividades y traslados por día           │ │ sugerencia. …      ││
│ │ │ vie 18/12/26 │ │ │                     [↻ Regenerar Día 1]   │ │                    ││
│ │ ├──────────────┤ │ │ h3 Mañana · 06:00–12:00                   │ │ (UI-04) 🛂 Los     ││
│ │ │ Día 2        │ │ │ ┌───────────────────────────────────────┐ │ │ requisitos de      ││
│ │ │ sáb 19/12/26 │ │ │ │ 🏛 Qorikancha                         │ │ │ ingreso pueden     ││
│ │ ├──────────────┤ │ │ │ Templo inca sobre el que se levantó…  │ │ │ cambiar. …         ││
│ │ │ Día 3        │ │ │ │ ┌ datos en grilla 2 columnas ───────┐ │ │ │ → Superintendencia ││
│ │ │ dom 20/12/26 │ │ │ │ │ Duración: 90 min  │ Horario:       │ │ │ │   Nacional de      ││
│ │ ├──────────────┤ │ │ │ │ [≈ Estimada]      │ 08:30–17:30    │ │ │ │   Migraciones ↗    ││
│ │ │ Día 4        │ │ │ │ │ Valor aproximado… │ [✓ Verificada] │ │ │ │                    ││
│ │ │ lun 21/12/26 │ │ │ │ │                   │ Fuente: … ·    │ │ │ │ ┌ UI-20 leyenda ─┐ ││
│ │ └──────────────┘ │ │ │ │ Precio:           │ Lugar:         │ │ │ │ │ ✓ Verificada … │ ││
│ │                  │ │ │ │ [! Por confirmar] │ [! Por confir…]│ │ │ │ │ ≈ Estimada …   │ ││
│ │ Te quedan 13 de  │ │ │ │ No pudimos …      │ No pudimos …   │ │ │ │ │ ! Por confirm… │ ││
│ │ 15 operaciones   │ │ │ └───────────────────┴────────────────┘ │ │ │ └────────────────┘ ││
│ │ de IA (UI-21)    │ │ │              [Pedir alternativa] [Eliminar]│ │ │                    ││
│ └──────────────────┘ │ └───────────────────────────────────────┘ │ └────────────────────┘│
│                      │   ┆ 🚶 Traslado: 20 min [≈ Estimada] Valor aproximado basado en …  │
│                      │ h3 Tarde · 12:00–18:00                                             │
│                      │ ┌ ☾ Descanso en el alojamiento … ──────────────── [Eliminar] ┐     │
│                      └─────────────────────────────────────────────────────────────┘     │
└──────────────────────────────────────────────────────────────────────────────────────────┘
```

- En escritorio el riel de días es un `nav` con lista de enlaces (`aria-current="true"` en el día activo); en móvil, un `tablist` con flechas izquierda/derecha para el teclado. Ambos muestran el mismo día y los mismos datos (PRINCIPIO-012).
- En escritorio los avisos UI-03 y UI-04 están en el riel derecho y **también** visibles sin desplazarse al cargar; en móvil van antes de las pestañas de días. En ambos viewports aparecen en todo itinerario (RN-024, RN-026).

**Estados**

| Estado | Comportamiento y textos |
|---|---|
| **Vacío** | (a) Sin viaje en C-18 → redirección a P-001 sin mensaje (HU-001). (b) Día con 0 actividades (el viajero las eliminó, RN-017, RN-038) → UI-25 «No hay actividades en este día.» + «Regenerar Día 3» [PROPUESTA UI · PREG-65]; el día sigue en la lista. (c) Franja sin actividades → no se muestra su encabezado [PROPUESTA UI · PREG-65]. |
| **Cargando** | (a) Lectura de C-18: esqueleto del encabezado y de 2 tarjetas (sin texto inventado), normalmente < 300 ms. (b) Refresh de estados: el itinerario se muestra con el respaldo de ADR-005; si tarda > 3 s, «Actualizando el estado de los datos…» [PROPUESTA UI]. (c) Alternativa > 3 s: UI-11 en la tarjeta «Buscando una alternativa…» [PROPUESTA UI · PREG-58]. (d) Regenerar > 3 s: UI-11 sobre el día «Armando una nueva versión del Día 2…» [PROPUESTA UI · PREG-58]. Durante (c) o (d) las demás acciones de IA quedan deshabilitadas con «Espera a que termine la operación en curso» [PROPUESTA UI]; Eliminar sigue disponible salvo en la actividad o el día en curso. |
| **Con datos** | Render completo descrito arriba. |
| **Error (IA lenta / caída)** | Alternativa: 30 s / 504 → «No pudimos encontrar una alternativa.» + «Reintentar» [PROPUESTA UI · PREG-58]; 502 → «No pudimos encontrar una alternativa válida esta vez. La actividad no cambió.» + «Reintentar» [PROPUESTA UI · PREG-58]; 503 → «El servicio de IA no está disponible en este momento. La actividad no cambió.» + «Reintentar» [PROPUESTA UI · PREG-58]. Regenerar: 30 s / 504 → «No pudimos regenerar el Día 2.» + «Reintentar» (exacto, RN-084); 502 / 503 → «No pudimos regenerar el Día 2. El día no cambió.» + «Reintentar» [PROPUESTA UI · PREG-58]. Sin opción válida → RN-108 (alternativa, exacto) / [PROPUESTA UI · PREG-58] (día). Nada cambia en C-18 (RN-087, RN-092). |
| **Error 422** | Alternativa / regenerar con 422 (dato alterado, caso (c)) → «No pudimos procesar este itinerario. Vuelve a generarlo para seguir ajustándolo.» [PROPUESTA UI · PREG-57]. Refresh con 422 → todos los hechos enviados pasan a «! Por confirmar», sin mensaje técnico (QB2-04). |
| **Error 429** | En la tarjeta o junto a «Regenerar Día N»: «Alcanzaste el límite de 15 operaciones. Podrás volver a intentarlo desde el 01/12/2026 a las 20:05.» (RN-043). Nada cambia. |
| **Error de almacenamiento local** | No se pudo escribir en C-18 → alerta «No pudimos guardar tu itinerario en este dispositivo. Puedes verlo ahora, pero se perderá al cerrar o recargar la página.» [PROPUESTA UI · PREG-63]. |
| **Sin conexión** | Banner UI-13 (RN-091). «Pedir alternativa» y «Regenerar Día N» deshabilitados con su texto (RN-090); «Eliminar», «Deshacer», «Mantener», cambiar de día y leer siguen disponibles. Los datos con `valid_until` vencido se presentan como «! Por confirmar» (ADR-005 punto 4). Al volver la conexión con `pending_operation` → UI-15 (RN-093). |
| **Modo cuenta** (`/account/trips/[tripId]`, Should) | Datos de `GET /v1/trips/{trip_id}` con estados ya recalculados (RN-107). Solo lectura en v1: no hay endpoint de edición de viajes de la cuenta (PREG-60, ARQ-Q-23); los botones de ajuste no se muestran y aparece «Para ajustar este viaje, ábrelo en el dispositivo donde lo creaste.» [PROPUESTA UI · PREG-60]. 404 → «No encontramos este viaje.» [PROPUESTA UI · PREG-61]. 401 → P-004. |

---

### P-004 — Ingresar o crear cuenta (Should)

| Campo | Valor |
|---|---|
| Propósito | Crear una cuenta opcional o iniciar sesión para conservar viajes en la nube. |
| Actor | Invitado. |
| HUs | HU-012 |
| Componentes | UI-24, UI-13, UI-26, UI-12 |
| Endpoints | `POST /v1/auth/register`, `POST /v1/auth/login`, `GET /v1/auth/oauth/{provider}/start` (§4.7) |
| RN | RN-072 (la cuenta es opcional), RN-098; PRINCIPIO-006 |

**Datos que muestra:** campos Correo y Contraseña; botones OIDC solo si PREG-60 los aprueba; nota «Crear una cuenta es opcional. Puedes seguir usando la app sin cuenta.» [PROPUESTA UI].

**Acciones:** cambiar de pestaña («Crear cuenta» / «Iniciar sesión»), enviar, «Continuar con {proveedor}» (condicional), «Seguir sin cuenta» (vuelve a la pantalla anterior), «Mostrar contraseña» (botón 48 px con `aria-pressed`).

**Wireframe — móvil**

```
┌──────────────────────────────────────┐
│ [logo] AI Travel Planner             │
├──────────────────────────────────────┤
│ h1 Guarda tus viajes en una cuenta   │ [PROPUESTA UI] 24/32
│ Crear una cuenta es opcional. Puedes │ 16/24
│ seguir usando la app sin cuenta.     │
│                                      │
│ ┌[Crear cuenta]┬[Iniciar sesión]┐    │ tablist, 48 px, 50 % c/u
│ └──────────────┴────────────────┘    │
│ Correo                               │
│ [ viajera@example.com            ]   │ type=email, autocomplete=email
│ Contraseña                           │
│ [ ••••••••••            ] [Mostrar]  │ autocomplete=new-password / current-password
│ (requisitos de contraseña: PREG-60)  │ 14/20
│                                      │
│ (UI-12 error, si aplica)             │
│ [          Crear cuenta          ]   │ primario 52 px
│                                      │
│ ─────────────── o ───────────────    │ solo si hay OIDC (PREG-60)
│ [  Continuar con {proveedor}     ]   │ secundario 48 px
│                                      │
│ Seguir sin cuenta                    │ enlace 48 px de alto
└──────────────────────────────────────┘
```

**Wireframe — escritorio:** tarjeta centrada de 480 px de ancho con el mismo contenido; a la derecha (columnas 8–12) texto «Con una cuenta puedes recuperar tus viajes en otro dispositivo. Tú decides qué viajes guardar, uno por uno.» [PROPUESTA UI].

**Estados**

| Estado | Comportamiento |
|---|---|
| Vacío | Pestaña «Crear cuenta» activa, campos vacíos. |
| Cargando | Botón ocupado; si pasa de 3 s, «Creando tu cuenta…» / «Iniciando sesión…» [PROPUESTA UI]. |
| Con datos | Tras 201/200 → si hay viajes locales sin asociar → P-005; si no → P-006. |
| Error | 409 → «Ya existe una cuenta con este correo. Inicia sesión.» [PROPUESTA UI · PREG-60]; 401 → «Correo o contraseña incorrectos.» [PROPUESTA UI · PREG-60] (mismo texto si el correo no existe, 07 §4.7); 422 correo → «Escribe un correo válido.» [PROPUESTA UI · PREG-60]; 422 contraseña → [PROPUESTA UI · PREG-60] según la política; 400 `oauth_failed` → «No pudimos iniciar sesión con {proveedor}. Inténtalo de nuevo.» [PROPUESTA UI · PREG-60]; red → «No pudimos conectarnos. Revisa tu conexión e inténtalo de nuevo.» [PROPUESTA UI]. |
| Sin conexión | Banner UI-13; botón deshabilitado con «Necesitas conexión para iniciar sesión» [PROPUESTA UI · PREG-58]. |

---

### P-005 — Asociar viajes a la cuenta (Should)

| Campo | Valor |
|---|---|
| Propósito | Pedir el consentimiento explícito, viaje por viaje, para guardar en la cuenta los viajes locales del dispositivo. |
| Actor | Usuario registrado (recién registrado o con sesión iniciada). |
| HUs | HU-012 |
| Componentes | UI-24, UI-13, UI-22, UI-12 |
| Endpoints | `POST /v1/trips/associations` (§4.8) |
| RN | RN-079, RN-080, RN-081, RN-082, RN-098; PRINCIPIO-005 |

**Datos que muestra:** por viaje local sin asociar: `title` («Cusco, 2026-12-18»), destino, fechas y grupo (de `trip_request`); posición «Viaje 1 de 2» [PROPUESTA UI].

**Acciones:** «Sí, guardar en mi cuenta» / «No, dejar solo en este dispositivo» [PROPUESTA UI · PREG-61] por viaje; «Cerrar» (equivale a «No», PREG-61); «Ver mis viajes» al terminar.

**Wireframe — móvil**

```
┌──────────────────────────────────────┐
│ [logo] AI Travel Planner   Mi cuenta │
├──────────────────────────────────────┤
│ h1 Tus viajes en este dispositivo    │ [PROPUESTA UI] 24/32
│ Elige, uno por uno, cuáles guardar   │ [PROPUESTA UI] 16/24
│ en tu cuenta. Los que no guardes     │
│ seguirán solo en este dispositivo.   │
│                                      │
│ Viaje 1 de 2                         │ 14/20, aria-live
│ ┌ (UI-22) ─────────────────────────┐ │ role=group, aria-labelledby=pregunta
│ │ h2 ¿Guardar 'Cusco, 2026-12-18'  │ │ exacto (RN-079), 20/28
│ │    en tu cuenta?                 │ │
│ │ Cusco, Perú · 18/12/2026 –       │ │ 14/20 secundario
│ │ 21/12/2026 · Familia             │ │
│ │ [ Sí, guardar en mi cuenta     ] │ │ secundario 48 px (ninguno es primario
│ │ [ No, dejar solo en este       ] │ │  ni preseleccionado: mismo peso visual)
│ │ [   dispositivo                ] │ │
│ └──────────────────────────────────┘ │
│                                      │
│ ┌ Arequipa, 2027-01-15 ────────────┐ │ viajes siguientes en lista atenuada,
│ │ Pendiente                        │ │ no interactivos hasta su turno
│ └──────────────────────────────────┘ │ [PROPUESTA UI]
└──────────────────────────────────────┘
```

**Wireframe — escritorio:** misma secuencia en una columna de 640 px centrada; a la derecha, lista resumen con el estado de cada viaje: «Pendiente», «Guardado en tu cuenta», «Se queda solo en este dispositivo», «No se pudo guardar» [PROPUESTA UI].

**Estados**

| Estado | Comportamiento |
|---|---|
| Vacío | 0 viajes locales sin asociar → P-005 no se muestra; se va a P-006 (HU-012, borde). |
| Cargando | Tras «Sí»: botón ocupado «Guardando…» [PROPUESTA UI]; los botones del viaje quedan deshabilitados. |
| Con datos | Secuencia de UI-22 y, al final, el resumen «Listo. 1 viaje guardado en tu cuenta. 1 viaje sigue solo en este dispositivo.» [PROPUESTA UI] + «Ver mis viajes». |
| Error | Red → texto de UF-02 5a [PROPUESTA UI · PREG-61] + reintento automático **solo** de los viajes ya consentidos al volver la conexión; 422 → UF-02 5b; 401 → UF-02 5d; 413 → «Este viaje es demasiado grande para guardarlo en tu cuenta. Sigue guardado en este dispositivo.» [PROPUESTA UI · PREG-61]. |
| Sin conexión | Banner UI-13; «Sí, guardar en mi cuenta» deshabilitado con «Necesitas conexión para guardar en tu cuenta» [PROPUESTA UI · PREG-58]; «No» sigue disponible (no envía nada). |

---

### P-006 — Mis viajes (Should)

| Campo | Valor |
|---|---|
| Propósito | Listar los viajes guardados en la cuenta y los del dispositivo, y abrirlos. |
| Actor | Usuario registrado. |
| HUs | HU-012 |
| Componentes | UI-24, UI-13, UI-12 |
| Endpoints | `GET /v1/trips`, `GET /v1/trips/{trip_id}` (§4.9) · `POST /v1/auth/logout` · `GET /v1/me` (§4.7) |
| RN | RN-080, RN-081, RN-082, RN-107 |

**Datos que muestra:** sección «En tu cuenta» (`items[]`: `title`, `start_date`, `end_date`); sección «En este dispositivo» (viajes de C-18, con «También en tu cuenta» si tienen `associated_trip_id`) [PROPUESTA UI]; correo del usuario (de `GET /v1/me`, solo en memoria, nunca en C-18).

**Acciones:** abrir un viaje de la cuenta → P-003 modo cuenta; abrir el viaje del dispositivo → P-003; «Guardar en mi cuenta» en un viaje local no asociado → P-005 con ese viaje; «Cerrar sesión» → `POST /v1/auth/logout` → P-003 o P-001 como invitado (los viajes locales siguen).

**Wireframe — móvil**

```
┌──────────────────────────────────────┐
│ [logo] AI Travel Planner   Mi cuenta │
├──────────────────────────────────────┤
│ h1 Mis viajes                        │ 24/32
│ viajera@example.com                  │ 14/20 secundario
│                                      │
│ h2 En tu cuenta                      │ 20/28
│ ┌──────────────────────────────────┐ │ tarjeta-enlace 72 px mín.
│ │ Cusco, 2026-12-18             ›  │ │
│ │ 18/12/2026 – 21/12/2026          │ │
│ └──────────────────────────────────┘ │
│                                      │
│ h2 En este dispositivo               │
│ ┌──────────────────────────────────┐ │
│ │ Arequipa, 2027-01-15          ›  │ │
│ │ Solo en este dispositivo         │ │ [PROPUESTA UI]
│ │ [Guardar en mi cuenta]           │ │ secundario 48 px
│ └──────────────────────────────────┘ │
│                                      │
│ [ Cerrar sesión ]                    │ terciario 48 px
└──────────────────────────────────────┘
```

**Wireframe — escritorio:** dos columnas de tarjetas (columnas 1–6 «En tu cuenta», 7–12 «En este dispositivo»).

**Estados**

| Estado | Comportamiento |
|---|---|
| Vacío | Cuenta sin viajes → «Todavía no guardaste viajes en tu cuenta.» [PROPUESTA UI]; dispositivo sin viajes → «No hay viajes en este dispositivo.» + «Planificar un viaje» → P-001 [PROPUESTA UI]. |
| Cargando | Esqueleto de 2 tarjetas; > 3 s → «Cargando tus viajes…» [PROPUESTA UI]. |
| Con datos | Ver wireframe. |
| Error | 401 → P-004 con «Tu sesión terminó. Vuelve a iniciar sesión.» [PROPUESTA UI · PREG-61]; red / 5xx → «No pudimos cargar tus viajes.» + «Reintentar» [PROPUESTA UI]; la sección del dispositivo se sigue mostrando. |
| Sin conexión | Banner UI-13; sección «En tu cuenta» con «Necesitas conexión para ver los viajes de tu cuenta» [PROPUESTA UI · PREG-58]; sección del dispositivo disponible. |

---

## 4. Design Tokens

Tema claro único en v1 [PROPUESTA UI] (§10, P-Q12). Los tokens se exponen como variables CSS (`--color-*`, `--space-*`, …) y como objeto TypeScript en `packages/core` para la futura app Expo.

### 4.1 Colores base

Ratios calculados con la fórmula de luminancia relativa de WCAG 2.x. Umbrales: texto normal ≥ 4.5:1, texto grande (≥ 24 px, o ≥ 18.66 px en negrita) ≥ 3:1, componentes de interfaz y bordes significativos ≥ 3:1 (1.4.11).

| Token | Hex | Uso | Par verificado | Ratio | AA |
|---|---|---|---|---|---|
| `color.bg` | `#FAF8F5` | Fondo de página | — | — | — |
| `color.surface` | `#FFFFFF` | Tarjetas, campos | — | — | — |
| `color.surface-muted` | `#F1EEE9` | Bloque de descanso, avisos neutros | `text` sobre `surface-muted` | 14.29:1 | ✔ |
| `color.text` | `#1C1F23` | Texto principal | sobre `bg` / `surface` | 15.60:1 / 16.54:1 | ✔ |
| `color.text-muted` | `#4A5058` | Texto secundario, textos obligatorios de estado | sobre `bg` / `surface` / `surface-muted` | 7.68:1 / 8.14:1 / 7.03:1 | ✔ |
| `color.primary` | `#0F5E6E` | Botón primario, enlaces, selección | `#FFFFFF` sobre `primary`; `primary` sobre `surface` / `bg` | 7.38:1 / 7.38:1 / 6.96:1 | ✔ |
| `color.primary-hover` | `#0B4A57` | Hover / pressed del primario | `#FFFFFF` sobre él | 9.85:1 | ✔ |
| `color.primary-subtle` | `#E3F0F2` | Fondo de opción seleccionada | `primary` sobre él / `text` sobre él | 6.33:1 / 14.19:1 | ✔ |
| `color.secondary` | `#3B3F8C` | Pestaña de día activa, acentos de navegación | sobre `surface`; `#FFFFFF` sobre él | 9.23:1 | ✔ |
| `color.accent` | `#A8430E` | Borde del aviso de sugerencia, ilustraciones | sobre `surface` / `bg`; `#FFFFFF` sobre él | 6.04:1 / 5.70:1 / 6.04:1 | ✔ |
| `color.error` | `#B42318` | Texto e ícono de error | sobre `surface` / `bg` / `error-bg` | 6.57:1 / 6.20:1 / 6.05:1 | ✔ |
| `color.error-bg` | `#FEF3F2` | Fondo de alerta de error | — | — | — |
| `color.error-border` | `#D92D20` | Borde del campo con error | sobre `surface` (no texto) | 4.83:1 | ✔ (≥ 3:1) |
| `color.success` | `#1E6B3A` | Confirmaciones («Guardado en tu cuenta») | sobre `surface` / `success-bg` | 6.52:1 / 5.90:1 | ✔ |
| `color.success-bg` | `#ECF6EF` | Fondo de confirmación | — | — | — |
| `color.info-fg` | `#1E4E8C` | Texto de UI-04 y UI-16 | sobre `info-bg` | 7.37:1 | ✔ |
| `color.info-bg` | `#EAF2FC` | Fondo de UI-04 y UI-16 | enlace `primary` sobre él | 6.54:1 | ✔ |
| `color.notice-fg` | `#7A4100` | Texto de UI-03 y UI-10 | sobre `notice-bg` | 7.62:1 | ✔ |
| `color.notice-bg` | `#FFF7E6` | Fondo de UI-03 y UI-10 | enlace `primary` sobre él | 6.92:1 | ✔ |
| `color.border` | `#8A9099` | Borde de campos y tarjetas | sobre `surface` (no texto) | 3.22:1 | ✔ (≥ 3:1) |
| `color.border-strong` | `#6B7280` | Borde de radios/checkbox | sobre `surface` (no texto) | 4.83:1 | ✔ |
| `color.focus` | `#1A56DB` | Anillo de foco | sobre `surface` / `bg` | 6.18:1 / 5.83:1 | ✔ |
| `color.disabled-fg` | `#545B64` | Texto de control deshabilitado | sobre `disabled-bg` | 5.55:1 | ✔ (aunque 1.4.3 exime a los deshabilitados) |
| `color.disabled-bg` | `#E5E7EB` | Fondo de control deshabilitado | — | — | — |
| `color.offline-bg` | `#33363B` | Banner sin conexión | `#FFFFFF` sobre él | 12.13:1 | ✔ |

> El anillo de foco tiene 1.19:1 contra `primary`, por eso se dibuja con `outline: 3px solid var(--color-focus); outline-offset: 2px`: el hueco de 2 px muestra el fondo (`surface`/`bg`), contra el que el anillo tiene ≥ 5.83:1 (2.4.7, 2.4.13 de WCAG 2.2 como buena práctica).

### 4.2 Tokens de los 3 estados de confiabilidad (PRINCIPIO-003, RN-062)

Cada estado se distingue por **ícono + texto + color + borde**, nunca solo por color (PRINCIPIO-010, RN-062). En escala de grises se distinguen por el glifo (✓, ≈, !) y la etiqueta (HU-008). Colores: [PROPUESTA UI · PREG-36] (el ámbar de Estimada sigue el ejemplo de RN-062).

| Token | ✓ Verificada (`verified`) | ≈ Estimada (`estimated`) | ! Por confirmar (`to_confirm`) |
|---|---|---|---|
| Glifo (`reliability.*.icon`) | `✓` (U+2713) | `≈` (U+2248) | `!` (U+0021) dentro de un triángulo SVG |
| Forma del contenedor del glifo | Círculo 16 px | Rombo / onda 16 px | Triángulo 16 px |
| Etiqueta (clave i18n) | `reliability.verified.label` → «✓ Verificada» | `reliability.estimated.label` → «≈ Estimada» | `reliability.to_confirm.label` → «! Por confirmar» |
| Texto obligatorio (clave i18n) | `reliability.verified.detail` → «Fuente: {fuente} · Consultado el {dd/mm/aaaa} a las {HH:mm}» | `reliability.estimated.detail` → «Valor aproximado basado en {base_de_cálculo}. Te recomendamos confirmarlo antes de tu viaje.» | `reliability.to_confirm.detail` → «No pudimos verificar este dato. Confírmalo con {fuente_sugerida} antes de tu viaje.» |
| Nombre accesible (clave i18n) | `reliability.verified.a11y` → «Estado: Verificada» [PROPUESTA UI] | `reliability.estimated.a11y` → «Estado: Estimada» [PROPUESTA UI] | `reliability.to_confirm.a11y` → «Estado: Por confirmar» [PROPUESTA UI] |
| `--rel-*-fg` (texto de la insignia) | `#1E6B3A` | `#8A4B00` | `#9E1F4F` |
| `--rel-*-bg` (fondo de la insignia) | `#E7F4EC` | `#FFF3DC` | `#FCE9F0` |
| `--rel-*-border` (borde 1.5 px) | `#2E8B57` | `#B26A00` | `#C2185B` |
| Contraste fg / bg | **5.76:1** ✔ | **6.19:1** ✔ | **6.53:1** ✔ |
| Contraste fg / `surface` (uso sin fondo) | 6.52:1 ✔ | 6.80:1 ✔ | 7.60:1 ✔ |
| Contraste borde / `surface` (no texto, ≥ 3:1) | 4.25:1 ✔ | 4.24:1 ✔ | 5.87:1 ✔ |
| Contraste borde / bg de la insignia (≥ 3:1) | 3.75:1 ✔ | 3.86:1 ✔ | 5.05:1 ✔ |
| Texto obligatorio (`text-muted`) sobre `surface` | 8.14:1 ✔ | 8.14:1 ✔ | 8.14:1 ✔ |

Reglas de render:

- La etiqueta se pinta completa desde su clave (`t('reliability.estimated.label')`), sin literales en el componente (RN-100). El glifo visual queda con `aria-hidden="true"` y la insignia expone el nombre accesible `reliability.*.a11y`, para que el lector no lea «aproximadamente igual».
- El texto obligatorio va siempre visible, inmediatamente después de la insignia (no en tooltip ni colapsado) [PROPUESTA UI] (§10, P-Q01).
- Mismo componente, misma etiqueta y mismo texto en ambos viewports (test de snapshot, HU-008, PRINCIPIO-012).

### 4.3 Tipografía

Familia: `"Inter", system-ui, -apple-system, "Segoe UI", Roboto, sans-serif`, autoalojada con `next/font` (sin CDN en tiempo de ejecución) [PROPUESTA UI]. Inter cubre los glifos `✓`, `≈`; si una fuente no tiene `✓`, el fallback del sistema lo dibuja. Base `font-size: 100 %` (16 px) y todos los tamaños en `rem`.

| Token | Tamaño / interlineado | Peso | Uso |
|---|---|---|---|
| `font.h1-desktop` | 1.875 rem (30 px) / 36 px | 700 | `h1` en escritorio |
| `font.h1` | 1.5 rem (24 px) / 32 px | 700 | `h1` en móvil |
| `font.h2` | 1.25 rem (20 px) / 28 px | 600 | Título del día, secciones |
| `font.h3` | 1 rem (16 px) / 24 px | 600 | Franja horaria |
| `font.title-card` | 1.125 rem (18 px) / 28 px | 600 | Nombre de la actividad |
| `font.body` | 1 rem (16 px) / 24 px | 400 | Texto general, descripción, valor del dato |
| `font.body-strong` | 1 rem (16 px) / 24 px | 600 | Etiqueta + valor del dato factual |
| `font.small` | 0.875 rem (14 px) / 20 px | 400 | Texto obligatorio de estado, ayudas, metadatos |
| `font.badge` | 0.875 rem (14 px) / 20 px | 600 | Insignia de confiabilidad |
| `font.button` | 1 rem (16 px) / 24 px | 600 | Botones |

Ningún texto por debajo de 14 px. Números con `font-variant-numeric: tabular-nums` en horarios y duraciones.

### 4.4 Espaciado y forma

| Token | Valor | Token | Valor |
|---|---|---|---|
| `space.1` | 4 px | `space.6` | 24 px |
| `space.2` | 8 px | `space.8` | 32 px |
| `space.3` | 12 px | `space.12` | 48 px |
| `space.4` | 16 px (gutter móvil) | `space.16` | 64 px |
| `radius.sm` | 8 px (campos, insignias: 999 px) | `radius.md` | 12 px (tarjetas, avisos) |
| `shadow.card` | `0 1px 2px rgba(28,31,35,.08)` | `border.width` | 1 px (1.5 px insignias; 2 px opción seleccionada) |
| `breakpoint.mobile` | 360–430 px (objetivo) | `breakpoint.desktop` | ≥ 1024 px |
| `content.max` | 1200 px | `form.max` | 640 px |

### 4.5 Tamaños de componentes (PRINCIPIO-010)

| Token | Valor | Nota |
|---|---|---|
| `size.touch-min` | **48 × 48 px** | Mínimo para todo objetivo táctil (supera los 44 × 44 de PRINCIPIO-010 y el ejemplo de 48 × 48 de la Constitución). Si el elemento visible es menor (checkbox de 24 px), el área clicable se extiende a 48 × 48 con padding. |
| `size.touch-gap` | ≥ 8 px | Separación entre objetivos táctiles adyacentes. |
| `size.button-h` | 48 px (52 px el CTA principal) | Padding horizontal 16–24 px. |
| `size.input-h` | 48 px | Incluye `date`, `email`, `password`, texto. |
| `size.tab-day` | 72 × 56 px (móvil) | Pestaña de día. |
| `size.icon` | 20 px (24 px en encabezados) | Íconos decorativos con `aria-hidden`. |
| `size.badge-h` | 28 px | Insignia de confiabilidad; no es interactiva. |
| `size.header-h` | 56 px móvil / 64 px escritorio | UI-24. |

### 4.6 Foco, movimiento y estados interactivos

| Token | Valor |
|---|---|
| `focus.ring` | `3px solid #1A56DB`, `outline-offset: 2px`, visible siempre con `:focus-visible` |
| `motion.duration` | 150 ms (200 ms en diálogos); 0 ms con `prefers-reduced-motion: reduce` |
| `state.hover` | Primario → `primary-hover`; secundario → fondo `primary-subtle` |
| `state.disabled` | `disabled-fg` sobre `disabled-bg`, `cursor: not-allowed`; se usa `aria-disabled="true"` (no `disabled`) cuando el botón debe seguir enfocable para leer su motivo (UI-14) |

---

## 5. Navigation Map

```
/  (sin UI: decide por C-18)
├─ C-18 vacío ───────────────────────────► P-001 Solicitud de viaje  (/plan)
│                                           ├─ [Generar itinerario]
│                                           │    ├─ ¿hay viaje guardado? → UI-15 «Esto reemplazará tu itinerario a {destino}. ¿Continuar?»
│                                           │    │     ├─ Cancelar → P-001 (sin cambios)
│                                           │    │     └─ Continuar ↓
│                                           │    ├─ < 3 s → (botón ocupado)
│                                           │    ├─ ≥ 3 s → P-002 Generación en curso (estado de /plan)
│                                           │    │     ├─ 200 → P-003
│                                           │    │     └─ 504 / 502 / 503 / 429 / red → P-001 (valores conservados + UI-12)
│                                           │    └─ 422 / validación → P-001 (UI-26)
│                                           └─ [← Volver a mi itinerario a {destino}] → P-003
│
└─ C-18 con viaje ───────────────────────► P-003 Itinerario  (/itinerary)
                                            ├─ UI-18 Día 1 … Día N   (misma pantalla)
                                            ├─ [Eliminar] → (local) UI-17 [Deshacer]
                                            ├─ [Pedir alternativa] → (en la tarjeta) UI-11 / UI-12
                                            ├─ [Regenerar Día N] → (en el día) UI-11 / UI-12
                                            ├─ [Mantener] → (local)
                                            ├─ UI-15 operación interrumpida → [Reintentar] / [Ahora no]
                                            ├─ enlaces de UI-04 → sitio oficial (pestaña nueva)
                                            └─ [Nuevo itinerario] → P-001

UI-24 encabezado (todas las pantallas)
├─ [logo] → /
└─ (solo con features.account)
   ├─ sin sesión: [Ingresar] → P-004 Ingresar o crear cuenta  (/account/sign-in)
   │                             ├─ [Seguir sin cuenta] → pantalla anterior
   │                             └─ 201 / 200 ─┬─ hay viajes locales sin asociar → P-005 Asociar viajes  (/account/associate)
   │                                           │                                   └─ [Ver mis viajes] → P-006
   │                                           └─ no hay → P-006
   └─ con sesión: [Mi cuenta] → P-006 Mis viajes  (/account/trips)
                                  ├─ viaje de la cuenta → P-003 modo cuenta  (/account/trips/[tripId], solo lectura)
                                  ├─ viaje del dispositivo → P-003  (/itinerary)
                                  ├─ [Guardar en mi cuenta] → P-005 (ese viaje)
                                  └─ [Cerrar sesión] → / (como invitado)
```

---

## 6. Componentes de UI reutilizables

Todos los textos vienen de claves i18n (§9). Todos los componentes interactivos cumplen `size.touch-min` y `focus.ring`.

| ID | Nombre (código) | Props | Estados | RN / principios |
|---|---|---|---|---|
| **UI-01** | Insignia de confiabilidad (`ReliabilityBadge`) | `status: 'verified' \| 'estimated' \| 'to_confirm'`; `size?: 'md' \| 'sm'` (sm = en línea dentro de UI-03) | 3 variantes visuales (§4.2). No interactiva. | PRINCIPIO-003, PRINCIPIO-010, RN-061, RN-062, RN-100 |
| **UI-02** | Dato factual con estado (`FactualDatumRow`) | `label: i18nKey` (p. ej. «Horario»); `datum: FactualDatum<T>`; `format: (value) => string` (Intl); `effectiveStatus` (de refresh o respaldo ADR-005) | `verified` (fuente con enlace si `source.url`, fecha y hora en America/Lima); `estimated` (`estimation_basis.key` → `{base_de_cálculo}`); `to_confirm` con valor (`Horario: 08:30–17:30` + insignia) o con `value: null` (solo «Precio:» + insignia; nunca vacío, RN-071); `downgraded` (era `verified` y venció: se pinta como `to_confirm`, sin texto extra) | PRINCIPIO-002, PRINCIPIO-003, RN-061, RN-065 a RN-068, RN-070, RN-071, RN-107 |
| **UI-03** | Aviso de sugerencia (`SuggestionNotice`) | `messageKey` (= `itinerary.notice.suggestion`) | Siempre visible en P-003; no se puede cerrar. | RN-026, RES-10 |
| **UI-04** | Aviso migratorio (`ImmigrationNotice`) | `messageKey` (= `itinerary.notice.immigration`); `links: {name, url, country_code, reviewed_at}[]` | Con enlaces; sin enlaces (país sin entrada en la lista curada: el texto se muestra igual + «No tenemos un enlace oficial para este destino en nuestra lista. Búscalo en el sitio oficial de migraciones del país de destino.» [PROPUESTA UI · PREG-04]). No se puede cerrar. No pide residencia ni nacionalidad. | RN-024, RN-025, PRINCIPIO-008, DEP-04 |
| **UI-05** | Tarjeta de actividad (`ActivityCard`) | `activity: Activity`; `dayNumber`; `onDelete`; `onAlternative`; `onKeep`; `aiDisabledReason?: i18nKey`; `busy?: boolean`; `error?: ErrorBody`; `readOnly?: boolean` (modo cuenta) | Normal; descanso (`kind: rest`, fondo `surface-muted`, sin «Pedir alternativa»); ocupada (alternativa en curso, UI-11 en línea); error (UI-12 en línea); deshabilitada sin conexión; solo lectura. Ícono por `kind`: visita 🏛, comida 🍽, descanso ☾, traslado 🚶 (SVG con `aria-hidden`). | RN-018, RN-020, RN-030 a RN-035, RN-038, RN-039, RN-102 |
| **UI-06** | Fila de traslado (`TravelConnector`) | `datum: FactualDatum<int> \| null` | Con dato (siempre ≈ Estimada en el MVP, RN-060); `null` → no se pinta. | RN-060, PRINCIPIO-002 |
| **UI-07** | Selector de menores por rango de edad (`ChildrenAgeRanges`) | `count: 0..9`; `value: (AgeRange \| null)[]`; `onChange`; `errors?: (i18nKey \| null)[]` | Sin menores (oculto); un fieldset por menor «Niño N» con 4 radios «0–2 años», «3–5 años», «6–11 años», «12–17 años»; con error. **No admite** nombre, fecha, foto, documento ni texto libre. | PRINCIPIO-004, RN-012, RN-013, RN-095 |
| **UI-08** | Campo de destino con desambiguación (`DestinationField`) | `query`; `resolution: {status: 'idle' \| 'loading' \| 'resolved' \| 'ambiguous' \| 'not_found' \| 'unavailable', candidates[]}`; `onResolve`; `onPick` | idle, cargando (spinner + `aria-busy`), resuelto (tarjeta «📍 {display_name}» + «Cambiar»), ambiguo (radios con candidatos), sin resultados, fuente caída, sin conexión | RN-001, RN-007, RN-096 |
| **UI-09** | Opción única segmentada (`SingleChoice`) | `name`; `options: {value, labelKey, hintKey?}[]`; `value`; `layout: 'grid' \| 'row' \| 'stack'` | Sin selección, seleccionada (borde 2 px `primary` + fondo `primary-subtle` + marca ● visible, no solo color), con error, enfocada. Implementado como `fieldset` + `input type=radio`. | RN-008, RN-016 |
| **UI-10** | Aviso de ritmo Activo con niños pequeños (`PaceAdvisory`) | `visible` (= `pace == 'active'` y algún menor `0-2` o `3-5`) | Visible / oculto; se recalcula con cada cambio (State-driven). No cambia el ritmo. `role="status"`. | RN-036, RN-037 |
| **UI-11** | Indicador de progreso de operación de IA (`AiProgress`) | `operation: 'generate' \| 'alternative' \| 'regenerate_day'`; `startedAt`; `dayNumber?`; `variant: 'fullscreen' \| 'inline'` | Oculto (< 3 s); visible (≥ 3 s, PAR-11) con su texto; aviso extendido a los 20 s (solo generate) | RN-101, PAR-09, PAR-11 |
| **UI-12** | Alerta de error de operación (`OperationErrorAlert`) | `error: ErrorBody \| 'timeout' \| 'network'`; `operation`; `onRetry?` | Mapea `error.code` → texto: `generation_timeout`/timeout, `generation_invalid_output`, `ai_unavailable`, `quota_exceeded` (sin «Reintentar», con `params.retry_at` formateado), `validation_error`, `places_unavailable`, `not_found`, red. `role="alert"`; el foco no se mueve salvo en P-001 (al resumen). | RN-043, RN-083, RN-084, RN-087, RN-088, RN-089, RN-108 |
| **UI-13** | Banner sin conexión (`OfflineBanner`) | `online: boolean` | Oculto / visible (sticky bajo UI-24, `role="status"`). Al reconectar desaparece sin mensaje adicional. | RN-090, RN-091 |
| **UI-14** | Botón de acción de IA (`AiActionButton`) | `labelKey`; `onClick`; `busy`; `disabledReasonKey?` | Habilitado; ocupado (`aria-busy`, 0–3 s sin texto); deshabilitado con motivo visible debajo y en `aria-describedby` (p. ej. «Necesitas conexión para regenerar»). | RN-090, RN-101 |
| **UI-15** | Diálogo de confirmación (`ConfirmDialog`) | `titleKey`, `params`; `confirmKey`; `cancelKey`; `onConfirm`; `onCancel` | Abierto / cerrado. `role="alertdialog"`, foco atrapado, foco inicial en «Cancelar»/«Ahora no» (la opción sin efecto), Esc = cancelar. Usos: reemplazo (RN-076) y operación interrumpida (RN-093). | RN-076, RN-093 |
| **UI-16** | Aviso de primer guardado (`FirstSaveNotice`) | `visible` (= escritura exitosa y `first_save_notice_shown == false`) | Visible hasta pulsar «Entendido» (sin tiempo límite, WCAG 2.2.1); luego nunca más en ese dispositivo. | RN-073, RN-075, RES-06 |
| **UI-17** | Deshacer eliminación (`UndoSnackbar`) | `activityName`; `dayNumber`; `onUndo` | Visible hasta la siguiente acción, hasta cerrarlo o hasta cambiar de día [PROPUESTA UI · PREG-59]; `role="status"`; alcanzable por teclado (no desaparece por tiempo). | RN-038 |
| **UI-18** | Navegador de días (`DayNav`) | `days: {day_number, date}[]`; `current`; `onSelect` | Móvil: `tablist` horizontal con scroll; escritorio: riel `nav`. Día activo: borde 3 px `secondary` + negrita + `aria-selected`/`aria-current`. Día ocupado (regenerando): indicador «…» + texto accesible «regenerando». | RN-017, RN-019 |
| **UI-19** | Encabezado del itinerario (`ItineraryHeader`) | `destination`, `startDate`, `endDate`, `tripRequest`; `onNew`; `readOnly?` | Normal; modo cuenta (sin «Nuevo itinerario»). Muestra rangos de edad, nunca identificadores. | RN-017, PRINCIPIO-004 |
| **UI-20** | Leyenda de estados (`ReliabilityLegend`) | `defaultOpen` (móvil `false`, escritorio `true`) | `<details>` con los 3 estados: insignia + «Dato de una fuente registrada, dentro de su vigencia.» / «Valor aproximado; confírmalo antes de viajar.» / «No pudimos verificarlo; confírmalo con la fuente sugerida.» [PROPUESTA UI] | PRINCIPIO-003, SUP-02 |
| **UI-21** | Contador de operaciones de IA (`QuotaMeter`) | `remaining`, `limit` (de `quota` en cada 200 o de `GET /v1/quota`) | Con valor; desconocido (no se muestra); 0 restantes. Texto: «Te quedan {restantes} de {limite} operaciones de IA» [PROPUESTA UI · PREG-58]. | RN-043, RN-089, PAR-07 |
| **UI-22** | Diálogo de asociación de viaje (`TripAssociationDialog`) | `tripLabel` («Cusco, 2026-12-18»); `summary`; `index`, `total`; `onAccept`; `onDecline`; `status: 'pending' \| 'saving' \| 'saved' \| 'declined' \| 'failed_network' \| 'failed'`; `downgrades?` | Pendiente (dos botones con el mismo peso visual, ninguno preseleccionado); guardando; guardado (+ aviso de `reliability_downgrades`); rechazado; falla de red en cola; falla definitiva. Cerrar = rechazar [PROPUESTA UI · PREG-61]. Nunca existe «Guardar todo». | RN-079, RN-080, RN-081, RN-082, PRINCIPIO-005 |
| **UI-23** | Orientación por rango de edad (`AgeGuidanceNote`) | `messageText`; `appliesTo: AgeRange[]`; `kept: boolean`; `onKeep` | Pendiente («Sugerencia: {message_text} Tú decides si la mantienes.» + «Mantener»); mantenida («Decidiste mantener esta actividad.» [PROPUESTA UI]). Nunca se rotula como restricción. | RN-034, RN-035 |
| **UI-24** | Encabezado global (`AppHeader`) | `accountEnabled`; `session: 'none' \| 'active'` | Sin cuenta habilitada (solo logo); invitado («Ingresar»); con sesión («Mi cuenta»). Enlace pasivo: nunca abre un pedido de registro por su cuenta. | RN-072 |
| **UI-25** | Día sin actividades (`EmptyDayState`) | `dayNumber`; `onRegenerate`; `aiDisabledReason?` | «No hay actividades en este día.» + «Regenerar Día N» [PROPUESTA UI · PREG-65]. | RN-017, RN-038 |
| **UI-26** | Campo de formulario con error (`FormField`) | `labelKey`; `hintKey?`; `errorKey?`, `errorParams?`; `required` | Normal, enfocado, con error (borde `error-border` 2 px + ícono de error + texto `error` vinculado con `aria-describedby` y `aria-invalid="true"`), deshabilitado. El error nunca usa los glifos ✓, ≈ o !. | RN-001 a RN-016, PRINCIPIO-010 |

---

## 7. Glosario de UI (términos nuevos para llevar a 04)

| Término | Definición | En código | No usar |
|---|---|---|---|
| **Insignia de confiabilidad** | Elemento visual que muestra el estado de confiabilidad de un dato con glifo, etiqueta exacta, color y borde. | `reliability_badge` | "semáforo", "chip de color" |
| **Texto obligatorio del estado** | Frase de PRINCIPIO-003 que acompaña siempre a la insignia (fuente y fecha, base de cálculo o fuente sugerida). | `reliability_detail` | "tooltip", "nota" |
| **Fila de dato factual** | Línea de la tarjeta que reúne la etiqueta del dato, su valor, la insignia y el texto obligatorio. | `factual_datum_row` | "campo" |
| **Tarjeta de actividad** | Bloque que presenta una actividad con sus datos factuales y sus acciones de ajuste. | `activity_card` | "ítem", "evento" |
| **Fila de traslado** | Línea entre dos tarjetas que muestra el tiempo de traslado con su estado. | `travel_connector` | "distancia" |
| **Navegador de días** | Control para pasar de un día del itinerario a otro: pestañas en móvil, riel lateral en escritorio. | `day_nav` | "calendario" |
| **Viewport móvil / viewport de escritorio** | Ancho de 360–430 px / ≥ 1024 px de la misma web responsive en v1. | `mobile` / `desktop` (proyectos E2E) | "app móvil" (en v1) |
| **Indicador de progreso** | Mensaje y barra indeterminada que aparecen cuando una operación de IA supera 3 s (PAR-11). | `ai_progress` | "loader", "spinner" (en specs) |
| **Banner sin conexión** | Franja fija bajo el encabezado que indica que el dispositivo no tiene conexión (RN-091). | `offline_banner` | "modo avión" |
| **Acción deshabilitada con motivo** | Botón no disponible que muestra junto a él por qué no se puede usar (p. ej., «Necesitas conexión para regenerar»). | `disabled_reason` | "botón gris" |
| **Operación interrumpida** | Operación de IA cortada por pérdida de conexión que el viajero puede reintentar al volver la red (RN-093). | `pending_operation` | "operación pendiente de envío" |
| **Diálogo de reemplazo** | Confirmación previa a generar un itinerario que reemplazaría al guardado (RN-076). | `replace_confirm_dialog` | "sobrescribir" |
| **Aviso de primer guardado** | Mensaje que se muestra una sola vez por dispositivo al guardar el primer itinerario (RN-075). | `first_save_notice` | "aviso de cookies" |
| **Deshacer eliminación** | Mensaje con la opción de restaurar la actividad recién eliminada. | `undo_delete` | "papelera" |
| **Contador de operaciones de IA** | Texto que indica cuántas operaciones de IA le quedan al dispositivo en la ventana de 24 h. | `quota_meter` | "créditos", "tokens" |
| **Diálogo de asociación** | Pregunta por viaje, con «Sí» y «No» de igual peso, para asociar un viaje local a la cuenta (RN-079). | `trip_association_dialog` | "importar viajes", "sincronizar todo" |
| **Leyenda de estados** | Explicación breve de los tres estados de confiabilidad, disponible en el formulario y en el itinerario. | `reliability_legend` | "ayuda de colores" |
| **Modo cuenta** | Presentación de solo lectura de un viaje guardado en la cuenta, abierta desde Mis viajes. | `account_view` | "modo nube" |
| **Área táctil** | Superficie interactiva mínima de 48 × 48 px de un control, aunque su parte visible sea menor. | `touch_target` | "botón chico" |
| **Estado vacío** | Presentación de una pantalla o un día sin contenido, con un mensaje y, si aplica, una acción. | `empty_state` | "pantalla en blanco" |

---

## 8. Matriz HU → P → UI-NN

| HU | Prioridad | Pantallas | Componentes |
|---|---|---|---|
| HU-001 | Must | P-001, P-003 | UI-13, UI-14, UI-15, UI-16, UI-17, UI-24 |
| HU-002 | Must | P-001 | UI-08, UI-14, UI-26 |
| HU-003 | Must | P-001 | UI-07, UI-09, UI-10, UI-26 |
| HU-004 | Must | P-002, P-003 | UI-05, UI-11, UI-18, UI-19 |
| HU-005 | Must | P-003 | UI-02, UI-05, UI-23 |
| HU-006 | Must | P-001, P-002, P-003 | UI-11, UI-12, UI-13, UI-14, UI-15, UI-21 |
| HU-007 | Must | P-003 | UI-03, UI-04, UI-05, UI-06, UI-18, UI-19, UI-25 |
| HU-008 | Must | P-003 | UI-01, UI-02, UI-20 |
| HU-009 | Must | P-003 | UI-02, UI-04, UI-05 (sin enlaces de reserva, pago ni afiliados; ningún campo libre muestra datos factuales sin estado) |
| HU-010 | Must | P-003 | UI-05, UI-12, UI-14, UI-17, UI-23 |
| HU-011 | Must | P-003 | UI-11, UI-12, UI-14, UI-15, UI-18, UI-25 |
| HU-012 | Should | P-004, P-005, P-006, P-003 (modo cuenta) | UI-12, UI-13, UI-22, UI-24, UI-26 |

**HUs Must sin pantalla:** ninguna (lista vacía). Las 11 HUs Must (HU-001 a HU-011) tienen al menos una pantalla Must, y ninguna de esas pantallas exige cuenta.

---

## 9. Catálogo de textos (claves i18n es-419)

Solo los textos de interfaz definidos aquí; los de validación de P-001 están en su tabla. «Exacto» = no se cambia sin cambiar la fuente.

| Clave | Texto | Origen |
|---|---|---|
| `form.submit` | Generar itinerario | Exacto, HU-002 |
| `form.destination.label` | Destino | Exacto, HU-002 |
| `form.destination.ambiguous` | Encontramos varios lugares con ese nombre. Elige uno: | [PROPUESTA UI] |
| `form.dates.start` / `form.dates.end` | Fecha de inicio / Fecha de fin | HU-002 |
| `form.group_type.*` | Solo · Pareja · Amigos · Familia | Exacto, HU-003 |
| `form.children.item` | Niño {n} | PRINCIPIO-004 (ejemplo) |
| `form.age_range.*` | 0–2 años · 3–5 años · 6–11 años · 12–17 años | Exacto, HU-003 |
| `form.needs.title` | Necesidades del grupo | Exacto, HU-003 |
| `form.pace.*` | Relajado · Equilibrado · Activo | Exacto, HU-003 |
| `form.pace_advisory` | Con ritmo Activo cada día puede sumar hasta 600 min de actividades y traslados. Con niños de 3–5 años quizá prefieras Equilibrado (480 min). | Exacto, RN-036 |
| `progress.generate` | Armando tu itinerario día por día… | Exacto, RN-101 |
| `progress.generate.long` | Esto puede tardar hasta un minuto. | [PROPUESTA UI · PREG-58] |
| `progress.alternative` | Buscando una alternativa… | [PROPUESTA UI · PREG-58] |
| `progress.regenerate` | Armando una nueva versión del Día {n}… | [PROPUESTA UI · PREG-58] |
| `errors.generation.timeout` | No pudimos generar tu itinerario. + `actions.retry` | Exacto, RN-083 |
| `errors.generation.failed` | No pudimos armar un itinerario válido esta vez. Tus datos siguen en el formulario. | [PROPUESTA UI · PREG-58] |
| `errors.ai_unavailable` | El servicio de IA no está disponible en este momento. Tus datos siguen en el formulario. | [PROPUESTA UI · PREG-58] |
| `adjust.regenerate.timeout` | No pudimos regenerar el Día {n}. + `actions.retry` | Exacto, RN-084 |
| `adjust.alternative.timeout` | No pudimos encontrar una alternativa. | [PROPUESTA UI · PREG-58] |
| `adjust.alternative.no_valid_option` | No encontramos otra actividad que encaje con tu grupo en esta franja. | Exacto, RN-108 |
| `adjust.regenerate.no_valid_option` | No encontramos otra versión de este día que encaje con tu grupo. El Día {n} no cambió. | [PROPUESTA UI · PREG-58] |
| `quota.exceeded` | Alcanzaste el límite de {limite} operaciones. Podrás volver a intentarlo desde el {dd/mm/aaaa} a las {HH:mm}. | Exacto, RN-043 |
| `quota.remaining` | Te quedan {restantes} de {limite} operaciones de IA | [PROPUESTA UI · PREG-58] |
| `actions.retry` | Reintentar | Exacto, RN-083 |
| `offline.banner` | Sin conexión. Algunas acciones no están disponibles. | Exacto, RN-091 |
| `offline.regenerate` | Necesitas conexión para regenerar | Exacto, RN-090 |
| `offline.generate` | Necesitas conexión para generar | [PROPUESTA UI · PREG-58] |
| `offline.alternative` | Necesitas conexión para pedir una alternativa | [PROPUESTA UI · PREG-58] |
| `interrupted.regenerate` | Se interrumpió la regeneración del Día {n}. ¿Reintentar? | Exacto, RN-093 |
| `interrupted.generate` | Se interrumpió la generación de tu itinerario. ¿Reintentar? | [PROPUESTA UI · PREG-58] |
| `interrupted.alternative` | Se interrumpió la búsqueda de una alternativa para "{actividad}". ¿Reintentar? | [PROPUESTA UI · PREG-58] |
| `guest.replace_confirm` | Esto reemplazará tu itinerario a {destino}. ¿Continuar? | Exacto, RN-076 |
| `guest.first_save_notice` | Tu itinerario se guardó en este dispositivo. Si borras los datos del navegador, lo perderás. | Exacto, RN-075 |
| `guest.storage_unavailable` | No pudimos guardar tu itinerario en este dispositivo. Puedes verlo ahora, pero se perderá al cerrar o recargar la página. | [PROPUESTA UI · PREG-63] |
| `itinerary.notice.suggestion` | Este itinerario es una sugerencia. Revisa los datos marcados como ≈ Estimada o ! Por confirmar antes de tu viaje. | Exacto, RN-026 |
| `itinerary.notice.immigration` | Los requisitos de ingreso pueden cambiar. Consúltalos en la fuente oficial antes de tu viaje | Exacto, RN-024 (sin punto final en la fuente) |
| `itinerary.notice.immigration.no_links` | No tenemos un enlace oficial para este destino en nuestra lista. Búscalo en el sitio oficial de migraciones del país de destino. | [PROPUESTA UI · PREG-04] |
| `itinerary.slot.*` | Mañana · Tarde · Noche | Exacto, PAR-04 |
| `activity.min_age.check_each_child` | Comprueba que cada menor la cumple. | Exacto, RN-031 / HU-005 |
| `activity.age_guidance` | Sugerencia: {message_text} Tú decides si la mantienes. | Exacto, RN-034 |
| `activity.keep` | Mantener | RN-035 (contraejemplo) |
| `activity.delete.undo` | Eliminaste "{actividad}" del Día {n}. · Deshacer | [PROPUESTA UI · PREG-59] |
| `day.empty` | No hay actividades en este día. | [PROPUESTA UI · PREG-65] |
| `reliability.*` | Ver §4.2 | Exacto, PRINCIPIO-003 |
| `suggested_source.establishment` / `.local_tourism_office` | el establecimiento / la oficina de turismo local | Exacto, 07 §3.2 / HU-009 |
| `basis.typical_duration_similar_activities` | la duración habitual de actividades similares | [PROPUESTA UI] |
| `basis.rest_block_for_young_children` | la pausa recomendada para niños pequeños | [PROPUESTA UI] |
| `basis.distance_between_points` | la distancia entre los puntos | [PROPUESTA UI] |
| `account.associate.prompt` | ¿Guardar '{viaje}' en tu cuenta? | Exacto, RN-079 |
| `account.associate.accept` / `.decline` | Sí, guardar en mi cuenta / No, dejar solo en este dispositivo | [PROPUESTA UI · PREG-61] |
| `account.associate.network_failed` | No pudimos guardar '{viaje}' en tu cuenta. Sigue guardado en este dispositivo. Lo intentaremos de nuevo cuando vuelva la conexión. | [PROPUESTA UI · PREG-61] |

---

## 10. Preguntas del UI Designer

| # | Pregunta | Pantalla / componente | PREG si existe |
|---|---|---|---|
| P-Q01 | ¿El texto obligatorio de cada estado debe estar siempre visible junto a la insignia (propuesta) o puede revelarse con un control (expandir) para reducir la longitud de las tarjetas en móvil? PRINCIPIO-003 dice que "acompaña al dato". | P-003, UI-02 | — |
| P-Q02 | Aprobar los textos de rechazo propuestos para RN-004, RN-008, RN-010, RN-011 (Solo, Pareja, Amigos), RN-012, RN-016, el resumen de errores y el 422 genérico. | P-001, UI-26 | PREG-57 |
| P-Q03 | Los textos exactos de HU-002 usan fechas ISO («La fecha 2027-02-30 no existe», «…hasta el 2027-11-07»), pero el resto de la UI usa `dd/mm/aaaa` (PRINCIPIO-003, PRINCIPIO-011). ¿Se cambian a «30/02/2027» y «07/11/2027»? | P-001 | PREG-57 |
| P-Q04 | Aprobar los textos propuestos para: 502 y 503 de generar, alternativa y regenerar; plazo de la alternativa; progreso de alternativa y regeneración; aviso de 20 s; acciones deshabilitadas sin conexión (generar, alternativa, buscar destino, iniciar sesión, guardar en la cuenta); operación interrumpida de generar y alternativa; regeneración sin opción válida; contador de operaciones. | P-001, P-002, P-003, UI-11, UI-12, UI-14, UI-21 | PREG-58 |
| P-Q05 | Eliminar actividad: ¿confirmación previa o «Deshacer» (propuesta: «Deshacer» sin límite de tiempo, hasta la siguiente acción)? | P-003, UI-17 | PREG-59 |
| P-Q06 | Métodos de registro (correo + contraseña, OIDC y con qué proveedores), política de contraseña y textos de 401, 409, 422 y `oauth_failed`. | P-004 | PREG-60 |
| P-Q07 | Asociación: ¿cerrar el diálogo equivale a rechazar? Textos de los botones «Sí» / «No», de la falla de red, del 422, del 413, de la sesión expirada y del viaje de otra cuenta (404). | P-005, P-006, P-003 modo cuenta, UI-22 | PREG-61 |
| P-Q08 | ¿P-002 debe ofrecer «Cancelar»? Abortar puede descontar una operación si el servidor completó (07 §5.2, ARQ-Q-09). La propuesta es no ofrecerlo en v1. | P-002 | — |
| P-Q09 | Si la generación que reemplaza a un itinerario falla por la IA (no por la red), ¿se conserva el anterior? La propuesta sigue 07 §3.3 regla 2 (sí). | P-001, UF-06 | PREG-62 |
| P-Q10 | ¿Se muestra la carga diaria (`daily_load_min`) frente al límite del ritmo («330 de 480 min»)? Es una suma de datos ≈ Estimada y el backend no la envía como `FactualDatum`, así que hoy la UI no puede ponerle estado sin asignarlo en el cliente. Propuesta: no mostrarla en v1. | P-003 | PREG-01 |
| P-Q11 | ¿Un bloque de descanso admite «Pedir alternativa»? Propuesta: solo «Eliminar». | P-003, UI-05 | — |
| P-Q12 | ¿v1 necesita tema oscuro? Propuesta: solo tema claro; los tokens están listos para agregar uno con su verificación de contraste. | §4 | — |
| P-Q13 | Paleta de los estados de confiabilidad: aprobar verde (Verificada), ámbar (Estimada) y magenta (Por confirmar, distinto del rojo de error) con los ratios de §4.2. | UI-01, §4.2 | PREG-36 |
| P-Q14 | Comportamiento y texto cuando el almacenamiento local no está disponible o está lleno (propuesta: mostrar el itinerario con una alerta de que se perderá al recargar). | P-003 | PREG-63 |
| P-Q15 | Texto y plazo cuando la fuente de lugares falla o tarda al resolver el destino. | P-001, UI-08 | PREG-64 |
| P-Q16 | Presentación de un día con 0 actividades y de una franja vacía (propuesta: estado vacío con «Regenerar Día N»; franja vacía oculta). | P-003, UI-25 | PREG-65 |
| P-Q17 | Aviso migratorio cuando el país no tiene entrada en la lista curada o el enlace está roto; ¿se agregan avisos para menores o sanitarios? | P-003, UI-04 | PREG-04 |
| P-Q18 | Aviso de RN-036 con un menor de 0–2 años: ¿cambia «niños de 3–5 años»? | P-001, UI-10 | PREG-26 |
| P-Q19 | Catálogo definitivo de necesidades del grupo y sus etiquetas visibles. | P-001 | PREG-25 |
| P-Q20 | ¿Qué ve el invitado si abre la app sin conexión en el MVP (F-14 es Could y la PWA no cachea el itinerario)? | P-003 | PREG-18 |
| P-Q21 | Textos de la base de cálculo (`basis.*`) y responsable del catálogo de `{base_de_cálculo}` y `{fuente_sugerida}`. | P-003, UI-02 | PREG-06 |
| P-Q22 | ¿Qué más se ofrece cuando no hay alternativa o día válido (además del mensaje de RN-108)? | P-003 | PREG-08 |
| P-Q23 | Zona horaria de las fechas y horas mostradas (`{HH:mm}`, `retry_at`): la propuesta usa America/Lima. | Todas | PREG-23, PREG-32 |
| P-Q24 | Viajes de la cuenta: ¿son de solo lectura en v1 o se pueden ajustar (requiere un endpoint de edición)? | P-003 modo cuenta, P-006 | PREG-60 |
| P-Q25 | Textos de UI sin PREG: título y subtítulo de P-001, «Cómo funciona», nota de privacidad, «4 días», sufijos de ritmo, «Entendido», «Cambiar», «Continuar» / «Cancelar» / «Ahora no», «Bloque de descanso», «Decidiste mantener esta actividad.», leyenda de estados, nombres accesibles de las insignias, textos de P-004 a P-006 y del resumen de asociación. ¿Los aprueba el PO o un revisor de UX copy? | Todas | — |

---

*Fin de 09-ui-spec.md.*
