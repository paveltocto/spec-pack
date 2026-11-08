# 02 — Alcance del MVP · AI Travel Planner

> **Fase BMAD:** Planificación · **Agente:** PM
> **Insumos:** 00-constitucion.md, 01-project-brief.md, capacidades MVP definidas por el equipo
> **Estado:** Borrador para revisión (aprobación pendiente en PREG-54) · **Revisión 2026-11-07:** correcciones de la auditoría QA (A-02, A-07, B-03, M-09); preguntas abiertas en 05-preguntas-abiertas.md
> **Revisión 2026-11-07 (Checkpoint 1):** restricciones R-NN → RES-NN y dependencias D-NN → DEP-NN (AL-27); F-10 se mantiene en Should y cubre la asociación de viajes de invitado (AL-25); F-11 con máximo de 5 itinerarios (AL-30, PAR-08); lista curada revisada cada 30 días (AL-30); aviso migratorio en todo itinerario (AL-24); umbrales de SUP-01, 03, 04, 05 y 06 diferidos a las pruebas internas (PREG-56).

---

## 0. Contexto que condiciona el alcance

| Factor | Valor | Impacto en el alcance |
|---|---|---|
| Equipo | 1 persona | Máximo 6 Must; cada Must debe ser construible y probable por una sola persona |
| Presupuesto | USD 0 (solo capas gratuitas) | Se descartan APIs de pago y todo lo que requiera tarifas fijas |
| Piloto | 20–50 usuarios | El MVP se diseña para validar, no para escalar |
| Idioma | Solo español (es-419) | Sin otros idiomas en v1; todo texto visible pasa igualmente por archivos de traducción (PRINCIPIO-011). Idioma del itinerario: PREG-20 |
| Segmento de validación | Parejas y jóvenes que viajan solos | Familias con niños se **soportan** desde v1, pero el éxito se mide en el segmento principal |
| Principio no negociable | La IA nunca inventa datos | Todo dato factual lleva estado: **Verificada**, **Estimada** o **Por confirmar** |
| Acceso | Sin registro obligatorio | Ningún Must puede depender de una cuenta |

---

## 1. IN-SCOPE

### 1.1 Must have (MVP)

| ID | Funcionalidad | Descripción | Capacidades que cubre |
|---|---|---|---|
| **F-01** | Modo invitado con conservación local | El viajero planifica sin registrarse y su itinerario activo se conserva en el dispositivo para consultarlo después, con conexión a internet. La consulta sin conexión es F-14. | 1, 9 |
| **F-02** | Perfil del viaje y del grupo | El viajero indica destino, fechas, tipo de grupo (solo, pareja, amigos, familia), rango de edad de cada menor (0–2, 3–5, 6–11, 12–17) y ritmo deseado (relajado, equilibrado, activo). | 2 |
| **F-03** | Generación de itinerario con IA adaptado | La IA genera un itinerario día por día cuyas actividades respetan los rangos de edad de los menores y el ritmo elegido. | 3, 4 |
| **F-04** | Vista del itinerario por días | El itinerario se muestra organizado por día, con actividades ordenadas por franja horaria y duración aproximada. | 5 |
| **F-05** | Ajuste esencial del itinerario | El viajero puede eliminar una actividad, pedir una alternativa para ella o regenerar un día completo. | 6 (parcial) |
| **F-06** | Estado de confiabilidad de los datos | Cada dato factual (según el glosario; la inclusión de dirección, duración y distancia está pendiente en PREG-01) se muestra con su estado: Verificada, Estimada o Por confirmar, y nunca sin él. | Transversal (constitución) |

### 1.2 Should have (post-MVP, primera iteración)

| ID | Funcionalidad | Descripción | Por qué no es Must |
|---|---|---|---|
| **F-07** | Estimación de presupuesto | Muestra un costo aproximado por día y total, siempre marcado como Estimada. | El itinerario es útil sin presupuesto; además, sin fuentes de precios gratuitas y fiables, casi todo sería "Estimada" y aporta poco en el piloto. |
| **F-08** | Recomendaciones de transporte y alimentación | Sugiere cómo moverse entre actividades y dónde comer cerca, con su estado de confiabilidad. | Las actividades ya forman el núcleo del itinerario; transporte y comida enriquecen, pero exigen fuentes de datos que aún no están resueltas. |
| **F-09** | Edición completa de actividades | Agregar actividades propias, modificar sus detalles y reordenarlas manualmente. | F-05 ya cubre la corrección de errores de la IA; la edición libre es comodidad, no condición de utilidad. |
| **F-10** | Cuenta opcional y viajes persistentes | Registro voluntario para guardar viajes en la nube, sincronizarlos y recuperarlos en otro dispositivo, y asociar a la cuenta los viajes creados como invitado (el invitado crea cuenta y conserva sus viajes, RN-079 a RN-082). | Es complementario por definición: el producto funciona sin cuenta. La cuenta opcional de la decisión vigente D-02 se cumple en v1 (esta primera iteración post-MVP), no en el piloto del MVP (AL-25). |
| **F-11** | Varios itinerarios locales | El invitado puede conservar y alternar entre hasta 5 itinerarios en su dispositivo (PAR-08, RN-077). | Para el piloto basta un itinerario activo por viajero. |

### 1.3 Could have

| ID | Funcionalidad | Descripción |
|---|---|---|
| **F-12** | Compartir o exportar | Enviar el itinerario como enlace o PDF a otros miembros del grupo. |
| **F-13** | Mapa del día | Ubicar las actividades de un día en un mapa con tiempos de desplazamiento (Estimada). |
| **F-14** | Consulta sin conexión | Abrir el itinerario guardado sin internet durante el viaje. |
| **F-15** | Valoración por actividad | El viajero marca una actividad como útil o no útil, para medir la calidad de la generación en el piloto. |

### 1.4 Won't have (esta versión)

Ver sección 2: todo lo marcado como fuera de alcance tiene prioridad **W**.

---

## 2. OUT-OF-SCOPE

| # | Funcionalidad | Razón |
|---|---|---|
| 1 | **Reservas automáticas** (hoteles, vuelos, tours, restaurantes) | Requiere integraciones comerciales, acuerdos con proveedores y responsabilidad sobre la transacción; excede a una persona y a USD 0. |
| 2 | **Pagos dentro de la app** | La app es gratuita y no intermedia transacciones; implicaría PCI, pasarelas con comisión y obligaciones de protección al consumidor. |
| 3 | **Gestión avanzada de viajes** (colaboración multiusuario, edición compartida en tiempo real, historial de versiones, carpetas de viajes) | Supone cuenta obligatoria y backend con estado complejo; contradice el uso como invitado y el tamaño del equipo. |
| 4 | **Requisitos migratorios detallados** (visas, documentos, vacunas por nacionalidad) | Alto riesgo de dar información incorrecta con consecuencias graves. En v1 solo se muestra un aviso y enlaces a fuentes oficiales, sin que la IA redacte requisitos. |
| 5 | **Precios, disponibilidad y horarios en tiempo real** | No hay fuentes gratuitas y fiables; se mostrarían como Estimada o Por confirmar, nunca como dato vivo. |
| 6 | **Asistente conversacional abierto durante el viaje** (chat libre, guía en vivo, geolocalización continua) | Multiplica el consumo del modelo de IA y la superficie de alucinación; el MVP se limita a generar y ajustar itinerarios. |
| 7 | **Idiomas distintos del español** | Decisión del brief: el piloto es hispanohablante. |
| 8 | **Notificaciones push y recordatorios** | Requieren infraestructura y permisos adicionales sin aportar a la validación del itinerario. |
| 9 | **App mobile nativa y publicación en App Store y Google Play** *(D-10: post-v1)* | Las tiendas tienen costos de cuenta de desarrollador incompatibles con USD 0. La experiencia móvil del piloto se entrega como web responsive o distribución de prueba. |
| 10 | **Contenido generado por usuarios** (reseñas, fotos, itinerarios públicos) | Exige moderación y gestión de datos de terceros; no aporta a la hipótesis principal. |

---

## 3. MVP

Con solo los Must, un viajero entra a la web desde el navegador de su computadora o de su celular, sin crear cuenta, y describe su viaje: a dónde va, cuántos días, si viaja solo, en pareja, con amigos o con niños (indicando su rango de edad) y a qué ritmo quiere moverse. La IA le devuelve un itinerario organizado por días, con actividades adecuadas a su grupo y ritmo, y cada dato concreto (horarios, precios, duraciones, direcciones) aparece claramente marcado como Verificada, Estimada o Por confirmar, de modo que sabe qué debe revisar antes de salir. Si algo no le encaja, elimina la actividad, pide una alternativa o regenera el día completo. El itinerario queda guardado en su dispositivo y puede volver a abrirlo cuando tenga conexión, incluso ya en destino. No puede reservar, pagar, calcular el presupuesto, compartir el plan ni sincronizarlo entre dispositivos; eso llega después (la cuenta opcional y la sincronización son F-10, Should de v1).

---

## 4. Supuestos

| ID | Supuesto | Cómo se validará |
|---|---|---|
| SUP-01 | Los viajeros prefieren un itinerario generado y ajustable antes que armarlo desde cero. | Tasa de itinerarios generados que se conservan tras la primera sesión en el piloto. Umbral: se fija tras las pruebas internas previas al piloto (ver PREG-56). |
| SUP-02 | Los viajeros entienden y valoran los estados Verificada / Estimada / Por confirmar, en lugar de verlos como señal de baja calidad. | Entrevistas o encuesta breve a usuarios del piloto. |
| SUP-03 | La capa gratuita del proveedor de IA alcanza para 20–50 usuarios con varias generaciones y regeneraciones cada uno. | Medir solicitudes y tokens por itinerario en pruebas internas antes del piloto. Umbral: se fija tras las pruebas internas previas al piloto (ver PREG-56). |
| SUP-04 | El almacenamiento local del navegador o del dispositivo es suficiente para conservar itinerarios de invitados. | Pruebas en los navegadores y dispositivos más usados del público objetivo. Umbral: se fija tras las pruebas internas previas al piloto (ver PREG-56). |
| SUP-05 | Existen fuentes abiertas con datos suficientes de lugares para destinos de Perú y Latinoamérica como para marcar una parte relevante de datos como Verificada. | Prueba con 3–5 destinos representativos. Umbral: se fija tras las pruebas internas previas al piloto (ver PREG-56). |
| SUP-06 | Los ajustes de F-05 (eliminar, alternativa, regenerar día) cubren la mayoría de las correcciones que el viajero necesita. | Registro de qué acciones se usan y feedback del piloto. Umbral: se fija tras las pruebas internas previas al piloto (ver PREG-56). |
| SUP-07 | El segmento parejas / jóvenes solos es representativo para validar el producto aunque se soporten familias. | Comparar uso y satisfacción entre segmentos en el piloto. |
| SUP-08 | Los viajeros aceptan una app solo en español sin percibirla como limitada. | Feedback cualitativo del piloto. |

---

## 5. Restricciones

### Recursos
- **RES-01** — Una sola persona construye, prueba y opera el producto.
- **RES-02** — Presupuesto USD 0: solo capas gratuitas de IA, hosting, base de datos y mapas.
- **RES-03** — Piloto limitado a 20–50 usuarios; no se diseña para escalar más allá.
- **RES-04** — Stack decidido en el Checkpoint 2 (decisiones vigentes D-09 y D-10): web Next.js responsive, backend FastAPI, Postgres de Supabase Free; app Expo post-v1 (ADR-002 de 07).

### Técnicas
- **RES-05** — Los límites de uso de las capas gratuitas (solicitudes por minuto, tokens por día) acotan cuántas generaciones y regeneraciones se permiten por usuario.
- **RES-06** — Sin cuenta, los datos del invitado viven solo en su dispositivo: si borra el navegador, los pierde. Debe avisarse.
- **RES-07** — La IA no puede ser fuente de datos factuales: todo dato verificable se obtiene de fuentes externas o se marca Estimada / Por confirmar.

### Legales
- **RES-08** — Ley N.° 29733 de Protección de Datos Personales (Perú): las edades de menores son datos personales. Se recogen solo la cantidad y el rango de edad de cada menor (nunca nombres ni fechas de nacimiento), se guardan localmente y se envían al modelo sin datos identificables.
- **RES-09** — Las condiciones de las capas gratuitas de algunos proveedores de IA permiten usar los datos para entrenamiento. Esos proveedores no se pueden usar, aunque no se envíe información personal identificable (PRINCIPIO-005, RN-099). La viabilidad con USD 0 queda en PREG-13.
- **RES-10** — Avisos obligatorios: el itinerario es una sugerencia, los datos marcados como Estimada o Por confirmar deben verificarse, y la información migratoria se consulta solo en fuentes oficiales (el aviso migratorio se muestra en todo itinerario, cualquiera sea el destino, RN-024).

### Tensión a resolver
- **RES-11** — *(Resuelta por la decisión vigente D-10, 2026-11-07.)* La v1 y el piloto se entregan solo como web responsive (navegador móvil 360–430 px y escritorio ≥ 1024 px), sin publicar en tiendas ni distribuir una app de prueba: evita las cuentas de desarrollador (Apple con cuota anual; Google Play con pago único), incompatibles con USD 0. La app Expo y su publicación se reevalúan después de v1.

---

## 6. Dependencias

| ID | Dependencia | Para qué | Funcionalidades | Riesgo | Plan B |
|---|---|---|---|---|---|
| DEP-01 | **Proveedor del modelo de IA** (capa gratuita) | Generar itinerarios, alternativas y regeneraciones | F-03, F-05 | Alto: límites de uso, cambios en la capa gratuita, alucinaciones | Abstraer el proveedor detrás de una interfaz; tener un segundo proveedor gratuito configurado |
| DEP-02 | **Fuentes confiables de lugares** (p. ej., datos abiertos de mapas y guías colaborativas) | Validar que un lugar existe y su ubicación, para marcarlo Verificada | F-03, F-06 | Medio: cobertura irregular en ciudades pequeñas | Marcar como Por confirmar lo que no se pueda contrastar |
| DEP-03 | **Fuentes de precios y horarios** | Respaldar horarios y costos | F-06, F-07 | Alto: no hay fuente gratuita y fiable | Mostrar siempre Estimada o Por confirmar; no bloquear la generación |
| DEP-04 | **Fuentes oficiales de requisitos migratorios** (Migraciones, cancillerías) | Enlazar desde el aviso migratorio, que se muestra en todo itinerario (RN-024) | Aviso legal (RES-10) | Bajo: solo enlaces, sin procesar contenido | Mantener una lista curada de enlaces revisada manualmente cada 30 días |
| DEP-05 | **Mapas y tiempos de desplazamiento** (servicio gratuito de rutas) | Duraciones y distancias entre actividades | F-04 (duración estimada), F-08, F-13 | Medio: límites de uso | En el MVP, duraciones aproximadas marcadas como Estimada sin llamar a la API |
| DEP-06 | **Hosting web** (capa gratuita) | Servir la app y el backend ligero | Todas | Medio: suspensión por inactividad, límites de ancho de banda | Despliegue reproducible para migrar entre proveedores gratuitos |
| DEP-07 | **App Store y Google Play** | Distribución móvil nativa | Ninguna en v1 (D-10: solo web) | Alto en costo, no en técnica | Web responsive en v1; reevaluar tras v1 |

---

## 7. Trazabilidad capacidad → funcionalidad

| # | Capacidad del equipo | Funcionalidad | Prioridad |
|---|---|---|---|
| 1 | Planificación como invitado | F-01 | M |
| 2 | Perfil de viajeros y grupos familiares | F-02 | M |
| 3 | Generación de itinerarios con IA | F-03 | M |
| 4 | Adaptación por edades y ritmo | F-03 | M |
| 5 | Visualización por días | F-04 | M |
| 6 | Personalización y modificación | F-05 (eliminar, alternativa, regenerar día) / F-09 (agregar, modificar, reordenar) | M / S |
| 7 | Estimación de presupuesto | F-07 | S |
| 8 | Recomendaciones de transporte, alimentación y actividades | Actividades en F-03 / transporte y alimentación en F-08 | M / S |
| 9 | Conservación local para invitados | F-01 (itinerario activo) / F-11 (varios itinerarios) | M / S |
| — | Registro y gestión persistente | F-10 | S |
| — | Estado de confiabilidad (constitución) | F-06 | M |
