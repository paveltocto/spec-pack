# 01 — Project Brief: AI Travel Planner

| Campo | Valor |
|---|---|
| Producto | AI Travel Planner |
| Fase | Descubrimiento (BMAD — Agente Analyst) |
| Contexto | Ejercicio académico — Diploma en AI-First Software Architecture |
| Versión del documento | 1.2 · Revisión 2026-11-07 (Checkpoint 1): KPI de conversión a cuenta pasa a post-MVP (AL-25); nota sobre nacional/internacional y aviso migratorio para todo destino (AL-24); pendientes enlazados a PREG-45 a PREG-53 (antes 1.1: rangos de edad alineados con la Constitución) |
| Fecha | 2026-11-07 |
| Siguiente fase | PRD (Agente PM) |

> **Convención de evidencia.** Cada afirmación relevante está etiquetada:
> - **[S]** Supuesto o estimación del equipo, pendiente de validar con usuarios.
> - **[E]** Evidencia con fuente verificable.
> - **[PD]** Por definir.
>
> A la fecha, **ningún dato de mercado de este documento cuenta con evidencia [E]**. Todas las cifras provienen del criterio del equipo y deben validarse en el piloto o mediante entrevistas.

---

## 1. Problema y oportunidad

### 1.1 Problema

Organizar un viaje obliga a una persona, **el organizador**, a investigar en fuentes dispersas: Google, blogs, TripAdvisor, Booking, Airbnb y agencias. Además debe comparar precios, calcular distancias y tiempos, ajustar el presupuesto y coordinar con los demás integrantes, normalmente por WhatsApp.

**Dolor principal:** obtener un **itinerario realista y adaptado al grupo** cuando se parte desde cero. Un plan puede verse atractivo y resultar impracticable al considerar edades, presupuesto, horarios de apertura, tiempos de traslado y distancias. [S]

### 1.2 Situación actual (línea base estimada)

| Caso | Tiempo de planificación | Distribución | Anticipación |
|---|---|---|---|
| Familia limeña, 4 días en Cusco (feriado largo de Fiestas Patrias) | 8–12 h [S] | 1–2 semanas [S] | 2–4 semanas [S] |
| Pareja, 5 días en Ciudad de México | 5–8 h [S] | Varios días [S] | 2–4 semanas; más en internacional o temporada alta [S] |

### 1.3 Herramientas actuales y sus brechas [S]

| Herramienta | Para qué se usa | Dónde falla |
|---|---|---|
| Google y blogs | Investigar destinos y actividades | Información dispersa y posiblemente desactualizada |
| TripAdvisor, Booking, Airbnb | Comparar alojamiento, actividades y opiniones | No construyen un itinerario completo |
| WhatsApp | Compartir opciones y coordinar | Conversaciones extensas, difícil llegar a acuerdos |
| Agencias de viaje | Paquetes organizados | Rigidez, comisiones implícitas, poca personalización |
| Chats de IA generalistas (ChatGPT, Gemini) | Generar itinerarios rápidamente | Texto sin estructura temporal real, traslados omitidos, precios y horarios potencialmente inventados |

### 1.4 Qué sale mal hoy [S]

- **Viajes familiares:** actividades no aptas para la edad, jornadas demasiado largas, falta de descansos y traslados excesivos.
- **Viajes internacionales:** requisitos migratorios, documentos o permisos de viaje de menores descubiertos tarde; costos ocultos y tipo de cambio.
- **Viajes nacionales:** disponibilidad de tours en temporada alta (por ejemplo, Arequipa en Semana Santa), precios abusivos, huelgas o carreteras cerradas.
- **Viajes grupales:** diferencias de presupuesto e intereses; dificultad para llegar a consenso.
- **Información incorrecta:** lugares cerrados, precios desactualizados o actividades no disponibles.

### 1.5 Oportunidad

Los chats de IA ya demostraron que generar un itinerario en segundos es posible, pero dejaron una **brecha de confianza y verificación**. La oportunidad es un planificador que combine la rapidez de la IA con **realismo logístico y transparencia sobre el estado de confiabilidad de cada dato**, para el mercado de Perú y Latinoamérica. [S]

**Prioridad del problema:** armar el itinerario desde cero va primero. La coordinación grupal y los ajustes durante el viaje son importantes, pero quedan en segundo plano para la v1.

---

## 2. Usuarios y segmentos objetivo

### 2.1 Roles de usuario

| Rol | Descripción |
|---|---|
| **Organizador** (usuario principal) | Busca destinos, compara precios, propone actividades y coordina. Suele ser uno de los padres, un integrante de la pareja o el amigo más organizado. |
| **Acompañante** (usuario secundario) | Participa en decisiones y necesita consultar y conocer el itinerario. |

### 2.2 Segmento de validación inicial (éxito del MVP)

**Parejas y viajeros individuales jóvenes.** [S]

- **Motivo:** mayor frecuencia de viaje, ciclo de decisión corto (1–2 decisores) y alta adopción digital.
- **Perfil del organizador típico:**
  - 24–38 años.
  - NSE B/B+ y C+ aspiracional.
  - 2–4 viajes al año (escapadas cortas y un viaje largo).
  - Bancarizado: paga pasajes y hoteles con tarjeta o apps de pago, pero reserva entre 20% y 30% del presupuesto en efectivo para gastos en destino.

### 2.3 Segmentos soportados en v1, pero no usados para medir el éxito inicial

| Segmento | Situación en v1 | Necesidad diferencial |
|---|---|---|
| **Familia con niños** | Incluido: el itinerario debe adaptarse si se indican niños. No se optimiza para su complejidad total. | Ritmo, pausas, aptitud por edad, traslados cortos |
| **Amigos** | Incluido como tipo de grupo | Diferencias de presupuesto e intereses, consenso |

### 2.4 Necesidades por edad de los niños [S]

| Etapa | Necesidad dominante | Quién decide |
|---|---|---|
| 0–2 años | Logística: coche, pañales, siestas, baja tolerancia a demoras | Padres (100%) |
| 3–5 años (**la más difícil de planificar**) | Equilibrio entre estimulación y agotamiento: ritmo lento, comida predecible, pausas frecuentes, tolerancia a cambios imprevistos | Padres (100%) |
| 6–11 años | Curiosidad y energía: interactividad, historia digerible, aire libre | Padres fijan el marco; los niños eligen alternativas puntuales |
| 12–17 años (adolescentes) | Autonomía e identidad: experiencias "compartibles", rechazo a planes infantiles | Decisión negociada; si no participan, rechazan la experiencia |

> Rangos alineados con el PRINCIPIO-004 de la Constitución (0–2, 3–5, 6–11, 12–17). Ante cualquier diferencia, prevalece la Constitución.

### 2.5 Nacional vs. internacional [S]

| Dimensión | Nacional (p. ej., Arequipa, Cusco) | Internacional (p. ej., Santiago, Bogotá, Ciudad de México) |
|---|---|---|
| Proporción estimada del público inicial | ~75% | ~25% |
| Preocupaciones principales | Disponibilidad de tours, tráfico de salida en feriados, huelgas o carreteras cerradas, precios de temporada alta | Presupuesto real por tipo de cambio, costos ocultos, documentación migratoria, datos móviles, seguridad en zonas desconocidas |

> "Nacional" e "internacional" se miden aquí respecto de un viajero residente en Perú y sirven solo para el análisis de mercado. El producto no pide país de residencia ni nacionalidad: el aviso migratorio se muestra en todo itinerario, cualquiera sea el destino, incluido Perú (RN-024, AL-24).

**Momentos de uso clave en Perú:** feriados largos (Semana Santa, Fiestas Patrias, Navidad y Año Nuevo) y vacaciones escolares (enero–marzo y julio).

---

## 3. Propuesta de valor

### 3.1 Declaración

> Para **organizadores de viajes en Perú y Latinoamérica** que pierden horas armando un plan desde fuentes dispersas, **AI Travel Planner** genera en minutos un **itinerario personalizado, logísticamente realista y transparente sobre qué información está verificada, cuál es estimada y cuál debe confirmarse**. Es gratuito y no requiere registro. A diferencia de los chats de IA genéricos, **no inventa precios, horarios, disponibilidad ni requisitos**. A diferencia de las agencias, ofrece **personalización y autonomía sin sobreprecio**.

### 3.2 Diferenciación frente a alternativas [S]

| Alternativa | Por qué el usuario cambiaría |
|---|---|
| **Chat de IA generalista** | Estructura temporal real, tiempos de traslado lógicos, ausencia de datos inventados, y un plan que puede modificar y recalcular |
| **Agencia de viajes** | Personalización sin sobreprecio, autonomía de ritmo, transparencia de costos |
| **Blogs y búsqueda manual** | Ahorro de tiempo; todo consolidado en un plan coherente |

> Nota para el PM: en las entrevistas, los usuarios mencionaron expectativas como mapa, bloques editables y recálculo de presupuesto. Se registran como **expectativas percibidas**, no como funcionalidades decididas.

### 3.3 Qué significa "confiable" para el usuario [S]

1. **Consistencia geográfica y temporal:** rutas y tiempos de traslado que no obliguen a correr.
2. **Transparencia explícita:** distinción visible entre dato Verificada, Estimada y Por confirmar, con fecha de referencia.
3. **Viabilidad operativa:** horarios reales e indicaciones de cómo llegar.

**Errores que provocan abandono definitivo:**
- Recomendar como principal un lugar **cerrado permanentemente**.
- Un presupuesto que termina **costando el doble** por precios inventados.

### 3.4 Acceso sin registro [S]

- **Importancia: crítica** en el embudo inicial. El usuario debe ver el valor antes de identificarse.
  - Se estima que exigir registro antes del primer resultado genera un rebote del 60–80%. **[S — sin fuente; validar]**
- **Qué motiva a crear cuenta:**
  - Persistencia: guardar el itinerario y consultarlo después.
  - Continuidad entre web y móvil.
  - Compartir el itinerario con acompañantes (F-12, Could). La edición compartida queda fuera de v1 (D-08).
  - Exportar o sincronizar con el calendario.
- **El invitado no pierde calidad de itinerario.** Pierde la persistencia entre dispositivos, la sincronización y la asociación de sus viajes a una cuenta (D-02). La colaboración multiusuario y el historial de versiones están fuera de v1 (D-08).

---

## 4. Modelo de negocio preliminar

### 4.1 Estado de la monetización

> ⚠️ **La monetización está PENDIENTE de definición** (post-MVP, PREG-53). El producto es gratuito para el usuario y el modelo se evaluará cuando el producto madure y existan métricas de validación. Las hipótesis de esta sección **no son decisiones**.

### 4.2 Costos operativos identificados

| Costo | Driver | Estado | Preocupación |
|---|---|---|---|
| **Consultas al modelo de IA (tokens)** | Se estiman 1–3 llamadas por viaje (1 generación y 1–2 ajustes parciales) [S] | **Por estimar** el costo por itinerario | Alta |
| **APIs de mapas y lugares** | Verificación de horarios, estado del lugar y fotos | **Por estimar** | **Máxima**: escala más rápido que el hosting |
| **Infraestructura** (hosting, base de datos, autenticación) | Usuarios y almacenamiento | Tiers gratuitos durante el piloto | Baja en el piloto |
| **Soporte y moderación** | Reportes de datos incorrectos | No costeado (equipo de 1 persona) | **[PD]** (PREG-45) |
| **Auditoría manual de calidad** | Revisión de itinerarios contra fuentes oficiales | Tiempo del equipo | Media |

**Techo de gasto:** USD 0 mensuales durante el desarrollo y el piloto (tiers gratuitos y créditos promocionales).

### 4.3 Hipótesis de monetización futura

| Hipótesis | Descripción | Condición |
|---|---|---|
| H1 — Afiliación transparente | Comisión por redirección a plataformas de reservas, tours o seguros, sin sobrecargo para el usuario | Declarada explícitamente; **no debe alterar el orden de las recomendaciones** |
| H2 — Freemium | Generación y edición gratuitas; de pago para exportaciones avanzadas u offline o itinerarios ilimitados | Validar disposición a pagar |
| H3 — Alianzas institucionales B2B | Visibilidad de rutas oficiales con entidades de turismo (p. ej., PromPerú, direcciones regionales) | Exploratoria |

**Descartado de plano:** publicidad invasiva y acuerdos opacos con operadores, es decir, recomendar por pago de pauta.

**Línea roja:** la afiliación es aceptable si se declara. Que una comisión influya en lo que la IA recomienda rompe la confianza de forma irreversible.

---

## 5. Métricas de éxito (KPIs medibles)

**Contexto de medición:** piloto controlado con **20–50 usuarios de prueba** del segmento de validación (parejas e individuales). Se espera que la mayoría **no viaje** antes de la sustentación, así que la validación es principalmente **pre-viaje**.

### 5.1 Ahorro de tiempo

| KPI | Definición | Método | Meta |
|---|---|---|---|
| **Tiempo de planificación base** | Horas que el usuario invirtió en su último viaje similar | Encuesta retrospectiva al ingresar al piloto | Línea base (hipótesis: 5–8 h) |
| **Time-to-Acceptable-Itinerary (TTAI)** | Minutos desde el inicio del formulario hasta que el usuario guarda o exporta un itinerario que declara listo | Prueba de usabilidad cronometrada | **≤ 45 min** (aspiracional ≤ 30 min) |
| **Reducción de tiempo** | 1 − (TTAI ÷ línea base) | Cálculo por usuario; se reporta la mediana | **≥ 80%** |

### 5.2 Utilidad del itinerario

| KPI | Definición | Método | Meta |
|---|---|---|---|
| **Realismo percibido** | Evaluación de logística y viabilidad (escala 1–5) | Encuesta post-uso | Media **≥ 4,0** **[PD: confirmar, PREG-46]** |
| **Intención de uso** | % que responde "Usaría este itinerario tal cual o con cambios mínimos si viajara mañana" | Encuesta post-uso | **≥ 70%** |
| **Nivel de edición** | % de ítems del plan modificados por el usuario | Registro de uso | Rango saludable **15–40%** (menos sugiere poca interacción; más sugiere rechazo) |
| **Satisfacción (CSAT)** | % de respuestas 4–5 en escala 1–5 | Encuesta post-uso | **≥ 80%** |
| **Usabilidad (SUS)** | Puntaje System Usability Scale | Cuestionario SUS | **≥ 75** |

> **[PD]** Se registró la meta como "CSAT / SUS"; aquí se separan en dos KPIs independientes. Hay que confirmar si se usarán ambos instrumentos (PREG-47).

### 5.3 Confiabilidad de la información

| KPI | Definición | Método | Meta |
|---|---|---|---|
| **Tasa de error operativo crítico** | % de ítems auditados con estado de apertura erróneo o desfase severo de horario | Auditoría manual contra Google Maps y webs oficiales | **< 5%** |
| **Lugares cerrados permanentemente recomendados** | Número de recomendaciones de lugares cerrados de forma definitiva | Misma auditoría | **0** |
| **Desviación presupuestaria** | Diferencia entre el gasto diario estimado (entradas, comidas promedio, transporte local) y la referencia verificada | Auditoría de muestra | **≤ ±20%** **[PD: confirmar umbral único, PREG-48]** |
| **Cobertura de etiquetado** | % de datos factuales (precio, horario, disponibilidad, requisito) etiquetados como Verificada, Estimada o Por confirmar | Auditoría de muestra | **100%** |

**Muestra de auditoría:** el plan original es auditar entre el 15% y el 20% de los itinerarios del piloto. Con 20–50 usuarios eso puede dar solo 3–30 itinerarios, una muestra insuficiente para afirmar "< 5%" con solidez. **[PD]** Definir un mínimo fijo (p. ej., 30 itinerarios, o el 100% si hay menos), o declarar la meta como indicativa (PREG-49).

### 5.4 Acceso y conversión

| KPI | Definición | Meta |
|---|---|---|
| **Tasa de finalización como invitado** | % de sesiones que llegan a un itinerario generado sin registrarse | **[PD]** línea base en el piloto |
| **Conversión a cuenta** *(post-MVP)* | % de invitados con itinerario generado que luego crean cuenta | Se mide en v1 tras el MVP, no en el piloto: la cuenta opcional (F-10) es Should y el MVP del piloto no la incluye (AL-25). **[PD]** línea base cuando F-10 esté disponible |

### 5.5 Costo operativo

| KPI | Definición | Meta |
|---|---|---|
| **Costo por itinerario** | Costo de IA + APIs de lugares y mapas ÷ itinerarios generados | **[PD]** medir en el piloto |
| **Gasto mensual** | Gasto total fuera de tiers gratuitos | **USD 0** durante el piloto |

---

## 6. Riesgos identificados y supuestos

### 6.1 Riesgos

| # | Riesgo | Impacto | Probabilidad | Mitigación a nivel de negocio |
|---|---|---|---|---|
| R1 | **Alucinaciones de la IA**: precios, horarios, disponibilidad o requisitos inventados | Crítico (abandono definitivo) | Alta | Regla no negociable: no inventar; clasificar cada dato como Verificada, Estimada o Por confirmar; auditoría manual |
| R2 | **Información desactualizada**: lugares cerrados, cambios de horario, temporadas | Alto | Alta | Mostrar la fecha de referencia; meta de 0 lugares cerrados permanentemente |
| R3 | **Requisitos migratorios y de menores incorrectos**, con riesgo de denegación de embarque | Crítico (legal y reputacional) | Media | **Fuera de alcance resolutivo en v1**: solo un aviso que remite a fuentes oficiales (Migraciones, cancillerías, consulados), indicando que los requisitos están sujetos a cambios |
| R4 | **Costo por consulta al modelo y a APIs de lugares** que exceda los tiers gratuitos | Alto (inviabilidad del piloto con techo USD 0) | Media | Medir el costo por itinerario y limitar regeneraciones |
| R5 | **Límites de tiers gratuitos cambiantes** (mapas, lugares, LLM) | Alto | Media | **Verificar los límites vigentes antes del piloto**; no se confirmaron en esta fase (mapas y lugares: PREG-50; LLM: PREG-13) |
| R6 | **Disponibilidad o descontinuación de modelos económicos** | Medio | Media | El modelo concreto es una decisión técnica posterior |
| R7 | **Privacidad de menores** (Ley N.° 29733 de Protección de Datos Personales) | Alto | Baja (gracias a la minimización) | Recoger solo la cantidad y el rango etario; sin nombres, fechas de nacimiento, documentos ni fotos; consentimiento del padre o tutor en los términos del piloto |
| R8 | **Pérdida de confianza por monetización sesgada** | Crítico (futuro) | Baja hoy | Afiliación declarada; prohibido alterar el orden de las recomendaciones |
| R9 | **Validación pre-viaje insuficiente**: los testers no viajan, solo evalúan la percepción | Medio | Alta | Métricas sustitutas; intentar incluir testers que viajen en vacaciones escolares o feriados |
| R10 | **Capacidad del equipo**: una sola persona para producto, desarrollo, pruebas y auditoría | Alto | Alta | Alcance acotado; segmento de validación único |
| R11 | **Muestra pequeña** (20–50 usuarios) que limita la significancia estadística | Medio | Alta | Declarar los resultados como indicativos y cualitativos |

### 6.2 Supuestos críticos por validar

| # | Supuesto | Cómo validarlo |
|---|---|---|
| S1 | Existe un organizador principal que carga con la mayor parte del trabajo | Entrevistas |
| S2 | Planificar toma 5–12 h según el tipo de viaje | Encuesta retrospectiva del piloto |
| S3 | El dolor principal es armar el itinerario desde cero, más que coordinar o ajustar en destino | Entrevistas y ranking de dolores |
| S4 | Parejas e individuales jóvenes son el segmento más rápido de validar | Reclutamiento y adopción en el piloto |
| S5 | El público inicial es ~75% nacional y ~25% internacional | Registro de destinos del piloto |
| S6 | Exigir registro provoca un rebote del 60–80% | Fuente secundaria o comparación en el piloto |
| S7 | La transparencia (Verificada, Estimada, Por confirmar) aumenta la confianza frente a los chats de IA | Encuesta comparativa |
| S8 | Los usuarios aceptan la afiliación declarada | Encuesta de actitud (futuro) |
| S9 | Es posible operar el piloto con USD 0 | Medición de costo por itinerario frente a los límites de tiers |

---

## 7. Restricciones conocidas

| Tipo | Restricción |
|---|---|
| **Regla de producto (no negociable)** | La IA no inventa precios, horarios, disponibilidad, restricciones ni requisitos migratorios. Distingue información **Verificada, Estimada y Por confirmar**. |
| **Acceso** | Uso gratuito y sin registro obligatorio; la cuenta es opcional. |
| **Plataformas** | v1 y piloto: web responsive (Next.js), usable en el navegador del celular y en escritorio. App mobile (Expo) después de v1 (decisión vigente D-10, 2026-11-07). |
| **Mercado e idioma** | Perú y Latinoamérica; viajes nacionales e internacionales; **solo español** en v1. |
| **Tipos de grupo en v1** | Individual, pareja, familia con niños y amigos. El éxito se mide en pareja e individual. |
| **Alcance migratorio** | Sin validación de visas ni permisos de menores en v1; solo un aviso con remisión a fuentes oficiales. |
| **Datos de menores** | Solo la cantidad y el rango etario. |
| **Presupuesto** | USD 0 mensuales durante el desarrollo y el piloto (tiers gratuitos y créditos). |
| **Equipo** | 1 persona (producto, desarrollo y pruebas). |
| **Piloto** | 20–50 usuarios de prueba; validación principalmente pre-viaje. |
| **Metodología y entregables** | Framework BMAD y Spec-Driven Development: Spec Packs (PRD, contratos de datos u OpenAPI/JSON Schema, plan de tareas e interfaces) y demo funcional desplegada. |
| **Contexto** | Ejercicio académico (Diploma en AI-First Software Architecture), con arquitectura desacoplada para un posible escalado posterior. |
| **Stack técnico** | Decidido en el Checkpoint 2 (decisión vigente D-09, ajustada por D-10): web Next.js, backend FastAPI, Postgres de Supabase Free solo como base; Expo post-v1. Detalle en ADR-002 de 07-architecture.md. |
| **Fecha de sustentación** | **[PD]** (PREG-51) |

---

## Anexo — Pendientes abiertos para la siguiente fase

1. ~~Fijar el stack~~ Resuelto (D-07): el stack se decide en el Checkpoint 2 de la Fase 1.
2. Definir la fecha de sustentación y ubicar el piloto en el calendario (¿vacaciones escolares de enero a marzo?). → PREG-51
3. Confirmar los instrumentos de satisfacción (CSAT y/o SUS) y el umbral presupuestario único. → PREG-47 y PREG-48
4. Definir el tamaño mínimo de la muestra de auditoría. → PREG-49
5. Verificar los límites vigentes de los tiers gratuitos de mapas, lugares y LLM. → PREG-50 (mapas y lugares) y PREG-13 (LLM)
6. Buscar una fuente o validar el supuesto de rebote por registro obligatorio. → PREG-52
