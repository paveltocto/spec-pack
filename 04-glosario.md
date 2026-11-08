# 04 — Glosario (lenguaje ubicuo) · AI Travel Planner

> **Fase BMAD:** Planificación · **Agente:** Business Analyst
> **Insumos:** 00-constitucion.md, 01-project-brief.md, 02-alcance.md, 03-reglas-de-negocio.md
> **Fecha:** 2026-11-07
> **Estado:** Borrador final v1.0 · **Revisión 2026-11-07:** correcciones de la auditoría QA (M-02, M-09, M-10, B-02 a B-06)
> **Revisión 2026-11-07 (Checkpoint 1):** "Aviso migratorio" se muestra en todo itinerario, sin pedir residencia ni nacionalidad (AL-24); "Lista curada" con revisión cada 30 días y referencia DEP-04 (AL-30, AL-27).
> **Revisión Clase 3 (v1.0):** expandido con Sección 8 (Términos de Interfaz y UI) alineada con 09-ui-spec.md y principios de diseño accesible (PRINCIPIO-003, PRINCIPIO-010).

**Cómo usar este glosario.** Cada término tiene un único significado en specs, reglas, UI y código. La columna "En código" propone el identificador en inglés (PRINCIPIO-011). La columna "No usar" lista sinónimos que generan ambigüedad y no deben aparecer en specs ni en la interfaz.

---

## 1. Personas y acceso

| Término | Definición | En código | No usar |
|---|---|---|---|
| **Viajero** | Persona que usa la app para planificar un viaje, con o sin cuenta. Es el actor de las reglas de negocio. | `traveler` | "cliente", "usuario final" |
| **Organizador** | Rol del viajero que arma el plan para el grupo. Usuario principal según el Brief §2.1. Sin reglas propias en v1. | `organizer` | "líder", "admin" |
| **Acompañante** | Integrante del grupo que no organiza, pero consulta el itinerario. Usuario secundario. Sin reglas propias en v1 (compartir es F-12, Could). | `companion` | "invitado" (ese término tiene otro significado) |
| **Invitado** | Viajero que usa la app sin cuenta. Sus datos de viaje viven solo en su dispositivo. | `guest` | "anónimo", "usuario sin login" |
| **Usuario registrado** | Viajero con cuenta opcional (F-10) que puede guardar viajes en la nube. | `registered_user` | "miembro", "suscriptor" |
| **Asociación de viaje a cuenta** | Paso de un viaje local de invitado a la cuenta de un usuario registrado, con consentimiento explícito por viaje (RN-079). | `trip_claim` | "sincronización automática", "importación" |
| **Almacenamiento local** | Espacio del dispositivo donde se guarda el itinerario del invitado: almacenamiento del navegador en web y almacenamiento local de la app en mobile; la tecnología se fija en el ADR de stack (D-07). No contiene tokens de sesión. | `local_store` | "caché", "base de datos local" |
| **Viaje local** | Itinerario guardado de un invitado, con su solicitud de viaje, que todavía no está asociado a una cuenta (RN-079 a RN-081). | `local_trip` | "borrador" |

## 2. Solicitud y perfil

| Término | Definición | En código | No usar |
|---|---|---|---|
| **Solicitud de viaje** | Conjunto de datos que el viajero envía para generar un itinerario: destino, fecha de inicio, fecha de fin, perfil del grupo y ritmo. | `trip_request` | "búsqueda", "consulta" |
| **Destino** | Lugar resuelto a una única entrada de la fuente de lugares (RN-007). | `destination` | "ciudad" (un destino puede no ser ciudad) |
| **Duración del viaje** | Fecha de fin − fecha de inicio + 1, en días. Máximo 14 días (PAR-01). | `trip_length_days` | "noches" |
| **Perfil del grupo** | Composición del grupo de viaje: tipo de grupo, cantidad de adultos, menores con su rango de edad y necesidades del grupo. | `group_profile` | "perfil del usuario" |
| **Tipo de grupo** | Categoría del grupo. Valores: **Solo** (1 adulto, 0 menores), **Pareja** (2 adultos, 0 menores), **Amigos** (2–10 adultos, 0 menores), **Familia** (≥ 1 adulto y ≥ 1 menor). | `group_type` (`solo`, `couple`, `friends`, `family`) | "modalidad" |
| **Adulto** | Viajero de 18 años o más. Toda solicitud tiene al menos uno (RN-009). | `adult` | "mayor" |
| **Menor** | Viajero de 0 a 17 años. De un menor solo se registra su rango de edad (PRINCIPIO-004). En reglas y specs se escribe "menor"; los textos de interfaz pueden decir "niño" o "niños". | `child` | "hijo", "niño 1" con nombre |
| **Rango de edad** | Intervalo cerrado que describe la edad de un menor: 0–2, 3–5, 6–11 o 12–17 años. No describe sus capacidades individuales. | `age_range` | "edad" (sola), "fecha de nacimiento" |
| **Necesidad del grupo** | Condición práctica del grupo elegida de un catálogo cerrado (p. ej., cochecito, movilidad reducida, menor exigencia física). Se asocia al grupo, no a un menor. | `group_need` | "condición médica", "comentarios" |
| **Ritmo** | Intensidad deseada del viaje. Valores: **Relajado**, **Equilibrado**, **Activo**. Define la carga diaria máxima. | `pace` (`relaxed`, `balanced`, `active`) | "moderado", "intenso" (nombres anteriores) |

## 3. Itinerario

| Término | Definición | En código | No usar |
|---|---|---|---|
| **Itinerario** | Plan generado por la IA y ajustado por el viajero, con un día por cada fecha del viaje. Es una sugerencia (RN-026). | `itinerary` | "plan definitivo", "reserva" |
| **Itinerario activo** | En el MVP, el único itinerario que un invitado conserva en su dispositivo (F-01). | `active_itinerary` | "itinerario actual" |
| **Itinerario guardado** | Itinerario conservado en el almacenamiento local del dispositivo. En el MVP coincide con el itinerario activo; con F-11 puede haber hasta 5 (RN-077). | `saved_itinerary` | "itinerario descargado" |
| **Día del itinerario** | Unidad del itinerario asociada a una fecha concreta del viaje. | `itinerary_day` | "jornada" |
| **Actividad** | Elemento de un día: visita, experiencia, comida o bloque de descanso, con franja horaria y duración aproximada. | `activity` | "evento", "ítem" |
| **Actividad propia** | Actividad agregada por el viajero, no por la IA (F-09). | `custom_activity` | "actividad manual" |
| **Franja horaria** | Tramo del día al que pertenece una actividad: Mañana (06:00–12:00), Tarde (12:00–18:00), Noche (18:00–23:00). | `time_slot` (`morning`, `afternoon`, `evening`) | "turno", "horario" (el horario es un dato factual) |
| **Duración aproximada** | Tiempo estimado de una actividad, en minutos, con estado de confiabilidad. | `duration_min` | "tiempo" (solo) |
| **Tiempo de traslado** | Minutos estimados para ir de una actividad a la siguiente. Es un dato factual (PRINCIPIO-002). | `transfer_min` | "distancia" |
| **Carga diaria** | Suma, en minutos, de las duraciones de las actividades —excepto los bloques de descanso— y de los tiempos de traslado de un día. Límites: Relajado 360, Equilibrado 480, Activo 600. | `daily_load_min` | "intensidad", "esfuerzo" |
| **Bloque de descanso** | Actividad de tipo pausa, de ≥ 60 min, que la generación y la regeneración incluyen en cada día cuando el grupo tiene menores de 0–2 o de 3–5 años (RN-033, RN-042). El viajero la puede eliminar (RN-038). No suma a la carga diaria. Si su duración lleva estado de confiabilidad: PREG-01. | `rest_block` | "tiempo libre" |
| **Orientación por rango de edad** | Sugerencia de la IA sobre si una actividad puede resultar exigente para un rango de edad. No es una restricción y el viajero decide (RN-034, RN-035). En pantalla puede rotularse "Sugerencia:"; fuera de este uso y de RN-026 (el itinerario es una sugerencia), "sugerencia" no designa otro concepto. | `age_guidance` | "restricción", "prohibido" |

## 4. Ajustes y operaciones de IA

| Término | Definición | En código | No usar |
|---|---|---|---|
| **Operación de IA** | Llamada que genera contenido con el LLM: generación de itinerario, alternativa o regeneración de día. Límite: 15 exitosas por dispositivo cada 24 h (PAR-07). | `ai_operation` | "consulta", "prompt" |
| **Generación** | Operación de IA que crea el itinerario completo desde una solicitud de viaje. | `generate_itinerary` | "búsqueda" |
| **Alternativa** | Actividad que reemplaza a otra en el mismo día y la misma franja (RN-039). | `alternative` | "sugerencia" (sin calificar) |
| **Regeneración de día** | Operación de IA que reemplaza todas las actividades de un día sin tocar los demás (RN-041). | `regenerate_day` | "recalcular", "refrescar" |
| **Proveedor de IA** | Servicio externo del LLM, accedido siempre a través de la capa de abstracción propia (PRINCIPIO-009). | `llm_provider` | nombre comercial del proveedor en specs |

## 5. Confiabilidad de los datos

| Término | Definición | En código | No usar |
|---|---|---|---|
| **Dato factual** | Precio, horario, disponibilidad, restricción de actividad (edad, altura, salud, accesibilidad), requisito migratorio o sanitario, clima o tiempo de traslado. Siempre lleva estado de confiabilidad. La inclusión de dirección, duración, distancia y existencia del lugar está pendiente (PREG-01). | `factual_field` | "información", "detalle" |
| **Estado de confiabilidad** | Marca obligatoria de un dato factual que dice cuánto se puede confiar en él. Valores: Verificada, Estimada, Por confirmar. Se muestra con ícono, texto y color. Las etiquetas son invariables: se escribe "dato en estado ✓ Verificada". | `reliability_status` | "semáforo", "nivel de confianza", "estado de veracidad", "fiabilidad" |
| **✓ Verificada** | Dato obtenido de una fuente registrada y dentro de su vigencia. Solo la capa de verificación lo asigna. | `verified` | "confirmado", "oficial", "real" |
| **≈ Estimada** | Valor aproximado calculado a partir de una base de cálculo declarada. | `estimated` | "aproximado" (como estado), "calculado" |
| **! Por confirmar** | Dato que no se pudo verificar. Siempre recomienda confirmarlo con una fuente sugerida antes del viaje. | `to_confirm` | "desconocido", "N/D", campo vacío |
| **Fuente registrada** | Sitio oficial, organismo público o API contratada, inscrita en el registro de fuentes, de la que puede provenir un dato Verificada. | `source` | "internet", "la IA" |
| **Registro de fuentes** | Inventario de fuentes registradas que mantiene el equipo. El backend rechaza un dato Verificada cuya fuente no figura en él (RN-106). Contenido y proceso: PREG-06. | `source_registry` | "lista blanca" |
| **Fuente sugerida** | Entidad con la que el viajero debe confirmar un dato Por confirmar (p. ej., "el establecimiento", "el operador del tour"). | `suggested_source` | — |
| **Base de cálculo** | Explicación de cómo se obtuvo un dato Estimada (p. ej., "precios promedio de la zona"). | `estimation_basis` | "fórmula" |
| **Vigencia** | Tiempo durante el que un dato Verificada sigue siéndolo: 7 días para precios, horarios y restricciones; 24 h para requisitos migratorios y sanitarios; la que declare la fuente para disponibilidad, clima y traslados. Vencida, el dato pasa a Por confirmar. | `validity_period` | "caducidad", "TTL" (en specs) |
| **Capa de verificación** | Componente del backend que contrasta los datos del LLM con fuentes registradas y es el único que asigna Verificada. | `verification_layer` | "validador de la IA" |
| **Tarifa diferenciada de niño** | Precio distinto para menores. Solo se aplica si está Verificada; si no, se calcula con la tarifa general (RN-051, RN-052). | `child_fare` | "descuento de niño" |
| **Aviso migratorio** | Mensaje de texto genérico que se muestra en todo itinerario, cualquiera sea el destino (incluido Perú), y remite a fuentes oficiales de la lista curada sin redactar requisitos. No depende del país de residencia ni de la nacionalidad del viajero, que no se piden (RN-024, RN-025). | `immigration_notice` | "requisitos de viaje" |
| **Lista curada** | Lista de enlaces a fuentes oficiales de requisitos migratorios, revisada manualmente por el equipo cada 30 días, que usa el aviso migratorio (RN-024, Alcance DEP-04). | `curated_links` | "directorio" |

## 6. Presupuesto (F-07, post-MVP)

| Término | Definición | En código | No usar |
|---|---|---|---|
| **Presupuesto del viaje** | Monto opcional mayor que 0, con moneda ISO 4217, que el viajero indica como tope de gasto. | `trip_budget` | "límite", "plata disponible" |
| **Costo estimado** | Suma de los costos del itinerario, por día y total. Siempre se muestra como Estimada. | `estimated_cost` | "precio total", "costo real" |
| **Presupuesto insuficiente** | Situación en la que el costo estimado total supera el presupuesto del viaje. Genera un aviso con la diferencia y no oculta el itinerario. | `budget_shortfall` | "sin presupuesto", "fuera de presupuesto" |

## 7. Proceso y gobierno

| Término | Definición | En código | No usar |
|---|---|---|---|
| **Spec** | Documento `specs/NNN-nombre/spec.md` con criterios Given / When / Then. Sin estado `approved` no se escribe código (PRINCIPIO-001). | `spec` | "ticket", "historia" (como sustituto) |
| **Golden set** | Conjunto de casos de evaluación que falla si aparece un dato factual sin estado o fuera de los límites del producto. | `golden_set` | "tests de la IA" |
| **Regla de negocio (RN)** | Enunciado atómico en notación EARS que fija un comportamiento verificable del producto (03-reglas-de-negocio.md). | `business_rule` | "requisito" (sin calificar) |
| **Parámetro (PAR)** | Valor numérico propuesto por el BA que usan las reglas y debe aprobar el PO (Anexo A del 03). | `config` | "constante mágica" |

---

## 8. Términos de interfaz y UI (lenguaje ubicuo de UI)

> Incorporados desde `09-ui-spec.md` (Clase 3) para garantizar trazabilidad consistente entre wireframes, componentes de frontend, pruebas e2e y reglas de accesibilidad.

| Término | Definición | En código | No usar |
|---|---|---|---|
| **Insignia de confiabilidad** | Elemento visual accesible que muestra el estado de confiabilidad de un dato con glifo (`✓`, `≈`, `!`), etiqueta textual exacta, color y borde (UI-01). No es interactiva. | `reliability_badge` | "semáforo", "chip de color", "tag" |
| **Texto obligatorio del estado** | Frase requerida por PRINCIPIO-003 que acompaña siempre a la insignia de confiabilidad (fuente y fecha consultada, base de cálculo o recomendación con fuente sugerida). | `reliability_detail` | "tooltip", "nota al pie", "popover" |
| **Fila de dato factual** | Componente que presenta la etiqueta del dato, su valor formateado con `Intl`, la insignia y el texto obligatorio (UI-02). | `factual_datum_row` | "campo", "propiedad" |
| **Tarjeta de actividad** | Bloque visual estructurado que presenta una actividad con sus datos factuales y acciones permitidas según perfil y conectividad (UI-05). | `activity_card` | "ítem", "evento", "tarjetita" |
| **Fila de traslado** | Elemento conector entre dos actividades que muestra el tiempo estimado de traslado con su estado de confiabilidad (UI-06). | `travel_connector` | "distancia", "ruta" |
| **Navegador de días** | Control de navegación temporal: pestañas táctiles con desplazamiento horizontal en móvil y riel de navegación accesible en escritorio (UI-18). | `day_nav` | "calendario", "paginador" |
| **Viewport móvil / escritorio** | Ancho de pantalla de 360–430 px para móvil y ≥ 1024 px para escritorio de la web responsive Next.js (D-10, ADR-007). | `mobile` / `desktop` | "versión móvil / versión web" |
| **Indicador de progreso de IA** | Mensaje de espera con barra indeterminada accesible (`aria-busy="true"`) mostrado únicamente cuando una operación de IA supera los 3 s (UI-11, PAR-11). | `ai_progress` | "loader", "spinner", "ruedita" |
| **Banner sin conexión** | Franja fija persistente debajo del encabezado que informa la pérdida de conectividad a internet (UI-13, RN-091). | `offline_banner` | "alerta modal", "modo avión" |
| **Acción deshabilitada con motivo** | Control interactivo deshabilitado pero enfocable por teclado que expone mediante `aria-describedby` el motivo de su bloqueo (UI-14). | `disabled_reason` | "botón gris", "botón mudo" |
| **Operación interrumpida** | Operación de IA cortada por falla de red, registrada en almacenamiento local para permitir reintento voluntario al recuperar conexión (UI-15, RN-093). | `pending_operation` | "operación pendiente de envío", "borrador" |
| **Diálogo de confirmación de reemplazo** | Modal accesible con foco atrapado que previene sobrescribir un itinerario activo sin confirmación del viajero (UI-15, RN-076). | `replace_confirm_dialog` | "sobrescribir", "popup de borrar" |
| **Aviso de primer guardado** | Notificación informativa mostrada una sola vez por dispositivo tras generar el primer itinerario local (UI-16, RN-075). | `first_save_notice` | "aviso de cookies", "modal de bienvenida" |
| **Deshacer eliminación** | Mensaje temporal accesible (`role="status"`) que permite restaurar una actividad eliminada en su misma franja y posición (UI-17, RN-038). | `undo_delete` | "papelera de reciclaje", "recovery" |
| **Contador de operaciones de IA** | Indicador cuantitativo que informa el número de operaciones de IA disponibles dentro de la ventana de 24 horas (UI-21, RN-043). | `quota_meter` | "créditos", "tokens restantes" |
| **Diálogo de asociación de viaje** | Interfaz individual por viaje que solicita consentimiento explícito («Sí» / «No») para vincular un viaje local a una cuenta (UI-22, RN-079). | `trip_association_dialog` | "importar todo", "sincronizar viajes" |
| **Leyenda de estados** | Componente desplegable accesible que explica la definición de los tres estados de confiabilidad (UI-20, SUP-02). | `reliability_legend` | "guía de colores", "ayuda flotante" |
| **Modo cuenta** | Vista de solo lectura de un itinerario asociado y almacenado en la nube abierta desde Mis viajes (UI-19, P-006). | `account_view` | "modo nube", "vista sincronizada" |
| **Área táctil mínima** | Superficie interactiva de al menos 48 × 48 px requerida para controles táctiles en móvil (PRINCIPIO-010, `size.touch-min`). | `touch_target` | "botón chico", "enlace inline" |
| **Estado vacío** | Presentación estructurada con ilustración y acción correctiva cuando una pantalla o día no contiene actividades (UI-25). | `empty_state` | "pantalla en blanco", "pantalla rota" |

