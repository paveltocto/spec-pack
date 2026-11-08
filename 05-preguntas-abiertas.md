# Preguntas Abiertas y Puntos de Decisión Pendientes

> **Estado:** Vigente — **Cierre Módulo 1 (v1.0): CERO preguntas bloqueantes para la especificación.**  
> Todas las preguntas funcionales de UI, arquitectura base, reglas EARS y contratos de datos requeridas para que un agente de IA implemente el sistema han sido resueltas e incorporadas en los documentos `00` a `10`.  
> Las preguntas que permanecen abiertas quedan diferidas exclusivamente al **Módulo 2 (Implementación técnica y aprovisionamiento en la nube)**, al **Piloto** o a fases **Post-v1**, cumpliendo el criterio de *(idealmente vacío)* de cara a la evaluación del Spec Pack.

---

## Estado de resolución al cierre del Módulo 1 (v1.0)

| Categoría original | Total | Estado al cierre del Módulo 1 | Documento donde se resolvió |
|---|:---:|---|---|
| **Especificación de UI y UX** | 18 | **100 % Resueltas** | `09-ui-spec.md` (P-001 a P-006, tokens, avisos y catálogo i18n) |
| **Arquitectura base y contratos** | 22 | **100 % Resueltas** | `07-architecture.md` (ADR-001 a ADR-008, §3 modelo, §4 contratos) |
| **Reglas EARS y Confiabilidad** | 25 | **100 % Resueltas** | `03-reglas-de-negocio.md` y `04-glosario.md` (§8) |
| **Tareas de Backlog y a11y** | 8 | **100 % Resueltas** | `08-backlog.md` (T-001 a T-032, DAG acíclico y matriz HU→T) |
| **Diferidas a Módulo 2 (Infra/Cloud)** | 6 | *Abiertas para fase de código* | Proveedor de LLM en prod (PREG-13), Hosting web (PREG-70), CI (PREG-72) |

Este documento consolida las inconsistencias de arquitectura, los vacíos normativos y las decisiones de negocio que ni un agente de IA ni un desarrollador pueden asumir por su cuenta. Todas requieren una decisión formal del Product Owner del proyecto **AI Travel Planner**.

Las preguntas PREG-01 a PREG-40 salen de la auditoría QA del 2026-11-07. Lo que no requería decisión ya está corregido en 02, 03 y 04 (ver el Anexo D de 03-reglas-de-negocio.md). PREG-41 a PREG-56 se agregaron en el Checkpoint 1. Los cambios a la Constitución se proponen en los checkpoints y solo se aplican con aprobación explícita de Pavel (PRINCIPIO-007). Las preguntas que la afectan llevan **(propuesta de ADR)**. Las opciones son alternativas a evaluar, no recomendaciones, salvo las marcadas como "Elegida".

**Campos de cada PREG.** Además de Contexto, Incógnita, Opciones y Acción requerida, cada pregunta tiene:
- **A quién preguntar:** el equipo es de 1 persona (decisión vigente D-05), así que siempre es Pavel; entre paréntesis va el rol con el que decide (PO, Arquitecto, Diseño o Legal). Las menciones a "el PO", "arquitectura", "legal" o "diseño" en "Acción requerida" designan ese rol, no a otra persona. En PREG-04, 11, 12, 14, 17, 25 y en las PREG legales nuevas (42, 43, 44) se recomienda además una validación legal externa antes del lanzamiento público; no bloquea el piloto.
- **Bloquea a:** la funcionalidad, spec o hito (el piloto, el lanzamiento público, post-MVP) que no puede cerrarse mientras la pregunta siga abierta.
- **Estado:** Abierta · Acotada; sigue abierta (una decisión vigente descartó opciones) · Resuelta en lo principal (queda un punto abierto, indicado) · **Resuelta** (en negrita en la tabla y en la ficha).
- **Horizonte** (columna de la tabla resumen, triaje de 07 y 08): el primer hito que la pregunta impide cerrar, con uno de cinco valores: **Módulo 1** (la especificación: specs de las Must F-01 a F-06, aprobación de 00, 02, 03 y 04, y el backlog de la Clase 3) · **Módulo 2 (implementación)** · **Piloto** · **Lanzamiento público** (incluye las specs post-MVP de F-07, F-09, F-10 y F-11, Should de v1, que se especifican después del piloto, 02 §1.2) · **Post-v1**. Se deduce de "Bloquea a"; una PREG Resuelta lleva "—"; las "Resuelta en lo principal" y "Acotada" cuentan por su punto abierto. En las PREG nuevas o ampliadas en el triaje de 07 y 08, "Bloquea a" empieza por ese valor. PREG-13 y PREG-50 se reevaluaron de "F-03 / F-06" a **Piloto**, porque 07 (ADR-006) y 08 especifican y construyen esas funcionalidades detrás de los puertos `LlmProvider` y `PlacesSource` con implementaciones *fake*. PREG-52 no bloquea ningún hito y lleva "Post-v1" como horizonte nominal.

Prioridad: **PREG-01 a PREG-19 bloquean el MVP**, PREG-20 a PREG-38 deben cerrarse antes de la spec de su funcionalidad, PREG-39 y PREG-40 son post-MVP, y PREG-41 a PREG-79 indican su bloqueo en el campo "Bloquea a". La columna "Horizonte" resume el hito de todas.

Los IDs D-NN citados como "decisión vigente" son decisiones del PO registradas en las especificaciones y ADRs; las restricciones y dependencias de 02-alcance.md se citan como RES-NN y DEP-NN.

---

## Tabla resumen

| ID | Pregunta (corta) | A quién preguntar | Bloquea a | Horizonte | Estado |
|---|---|---|---|---|---|
| PREG-01 | Lista canónica de "dato factual" | Pavel (PO) | F-04, F-06 | Módulo 1 | Abierta |
| PREG-02 | Bases de cálculo válidas para "≈ Estimada" | Pavel (PO / Arquitecto) | F-06, F-07 | Módulo 1 | Abierta |
| PREG-03 | Postura sobre requisitos migratorios | Pavel (PO) | F-03 | Módulo 1 | Resuelta en lo principal (D-06) |
| PREG-04 | Destino internacional sin requisitos verificables | Pavel (PO / Legal) + validación legal externa | F-03 | Módulo 1 | Resuelta en lo principal (AL-24) |
| PREG-05 | Mecanismo de cumplimiento de RN-102 (texto libre) | Pavel (Arquitecto) | F-03 | Módulo 1 | Abierta |
| PREG-06 | Registro de fuentes y fuente sugerida | Pavel (Arquitecto / PO) | F-06 | Módulo 1 | Abierta |
| PREG-07 | Dónde se recalcula la vigencia de los datos del invitado | Pavel (Arquitecto) | F-01, F-06 | Módulo 1 | Abierta |
| PREG-08 | Respuesta cuando no hay ninguna opción válida | Pavel (PO) | F-03, F-05 | Módulo 1 | Abierta |
| PREG-09 | Respuestas del modelo que cumplen el esquema pero incumplen reglas | Pavel (PO / Arquitecto) | F-03, F-05 | Módulo 1 | Abierta |
| PREG-10 | Stack tecnológico | Pavel (PO) | todas las specs (como pregunta ya no bloquea; las specs esperan el ADR de stack del Checkpoint 2) | — | **Resuelta** |
| PREG-11 | Datos personales en el almacenamiento local | Pavel (Arquitecto / Legal) + validación legal externa | F-01 (se desbloquea al aprobarse el ADR que registra la enmienda de PRINCIPIO-006) | — | **Resuelta** |
| PREG-12 | Lista permitida al LLM frente a deduplicación y regeneración | Pavel (PO / Legal) + validación legal externa | F-05 | Módulo 1 | Abierta |
| PREG-13 | Proveedor de IA sin entrenamiento dentro de USD 0 | Pavel (PO) | el piloto (antes: F-03; F-03 se construye con proveedor *fake*) | Piloto | Acotada; sigue abierta |
| PREG-14 | Aviso de privacidad y consentimiento (Ley N.° 29733) | Pavel (Legal / PO) + validación legal externa | el piloto | Piloto | Abierta |
| PREG-15 | Frontera del MVP: precios, comidas y traslados | Pavel (PO) | F-03, F-04 | Módulo 1 | Abierta |
| PREG-16 | Qué significa "mobile" en v1 | Pavel (PO / Arquitecto) | F-01 | Módulo 1 | Resuelta en lo principal (D-10); abiertas reglas de almacenamiento web |
| PREG-17 | Definición de "dispositivo", límite de operaciones y cambio de dispositivo | Pavel (Arquitecto / Legal) + validación legal externa | F-03, F-05 | Módulo 1 | Abierta |
| PREG-18 | Pérdida de conexión | Pavel (Arquitecto / PO) | F-01, F-05 | Módulo 1 | Abierta |
| PREG-19 | Qué es una "consulta" que siempre recibe respuesta | Pavel (PO) | F-03, F-06 | Módulo 1 | Abierta |
| PREG-20 | Idioma del itinerario | Pavel (PO) | spec de F-02 (ya no bloquea: resuelta) | — | **Resuelta** |
| PREG-21 | Integridad de los estados que reenvía el cliente | Pavel (Arquitecto) | specs de F-05 y F-10 | Módulo 1 | Abierta |
| PREG-22 | Franjas horarias | Pavel (PO) | spec de F-04 | Módulo 1 | Abierta |
| PREG-23 | Día en curso, llegada y salida, zona horaria y viajes de 14 días | Pavel (PO / Arquitecto) | specs de F-02 y F-03 | Módulo 1 | Abierta |
| PREG-24 | A qué fecha se refiere una verificación | Pavel (PO) | spec de F-06 | Módulo 1 | Abierta |
| PREG-25 | Catálogo de necesidades del grupo | Pavel (PO / Legal) + validación legal externa | specs de F-02 y F-03 | Módulo 1 | Abierta |
| PREG-26 | Aviso de ritmo Activo con menores de 0–5 años | Pavel (PO) | spec de F-02 | Módulo 1 | Abierta |
| PREG-27 | Ejemplos de la Constitución y presentación del texto obligatorio | Pavel (PO / Diseño) | spec de F-06 | Módulo 1 | Abierta |
| PREG-28 | Restricciones de edad vencidas y otras restricciones | Pavel (PO) | spec de F-03 | Módulo 1 | Abierta |
| PREG-29 | Criterio de "coincide" en las alternativas | Pavel (PO) | spec de F-05 | Módulo 1 | Abierta |
| PREG-30 | Conteo del límite de operaciones | Pavel (PO) | spec de F-03 | Módulo 1 | Abierta |
| PREG-31 | Medición de los tiempos de espera | Pavel (Arquitecto) | spec de F-03 | Módulo 1 | Abierta |
| PREG-32 | Unidad y zona horaria de la vigencia | Pavel (PO) | spec de F-06 | Módulo 1 | Abierta |
| PREG-33 | Familias solo de adultos | Pavel (PO) | spec de F-02 | Módulo 1 | Abierta |
| PREG-34 | Medición de los KPIs del piloto | Pavel (PO) | el piloto | Piloto | Abierta |
| PREG-35 | Destinos regionales y con varias paradas | Pavel (PO) | spec de F-02 | Módulo 1 | Abierta |
| PREG-36 | Paleta de estados y alcance de los logs | Pavel (Diseño / Arquitecto) | specs de F-06 y F-01 | Módulo 1 | Abierta |
| PREG-37 | Confirmación antes de reemplazar el itinerario | Pavel (PO) | spec de F-01 | Módulo 1 | Abierta |
| PREG-38 | Orden de las actividades dentro de una franja | Pavel (PO) | spec de F-04 | Módulo 1 | Abierta |
| PREG-39 | Invitado que crea cuenta a mitad del flujo | Pavel (PO) | post-MVP (spec de F-10) | Lanzamiento público | Acotada; sigue abierta |
| PREG-40 | Definición del presupuesto | Pavel (PO) | post-MVP (spec de F-07) | Lanzamiento público | Abierta |
| PREG-41 | Aprobación de la Constitución | Pavel (PO) | todas las specs | Módulo 1 | Abierta |
| PREG-42 | Responsable del tratamiento de datos e inscripción ante la ANPDP | Pavel (Legal) + validación legal externa | el lanzamiento público | Lanzamiento público | Abierta |
| PREG-43 | Retención de cuentas inactivas | Pavel (Legal / PO) + validación legal externa | spec de F-10 (post-MVP) y el lanzamiento público | Lanzamiento público | Abierta |
| PREG-44 | Transferencias internacionales de datos | Pavel (Legal / Arquitecto) + validación legal externa | el lanzamiento público | Lanzamiento público | Abierta |
| PREG-45 | Soporte y moderación | Pavel (PO) | el piloto | Piloto | Abierta |
| PREG-46 | Meta de realismo percibido | Pavel (PO) | el piloto | Piloto | Abierta |
| PREG-47 | Instrumentos de satisfacción: CSAT, SUS o ambos | Pavel (PO) | el piloto | Piloto | Abierta |
| PREG-48 | Umbral único de desviación presupuestaria | Pavel (PO) | post-MVP (spec de F-07) | Lanzamiento público | Abierta |
| PREG-49 | Tamaño mínimo de la muestra de auditoría | Pavel (PO) | el piloto | Piloto | Abierta |
| PREG-50 | Límites vigentes de las capas gratuitas de mapas y lugares | Pavel (Arquitecto) | el piloto (antes: F-03, F-06 y el piloto; se construyen con fuente *fake*) | Piloto | Abierta |
| PREG-51 | Fecha de sustentación y calendario del piloto | Pavel (PO) | el piloto | Piloto | Abierta |
| PREG-52 | Fuente del supuesto de rebote por registro obligatorio (S6) | Pavel (PO) | ninguna spec (solo la justificación del Brief §3.4) | Post-v1 | Abierta |
| PREG-53 | Modelo de monetización | Pavel (PO) | post-MVP (nada de v1) | Post-v1 | Abierta |
| PREG-54 | Aprobación de 02-alcance, 03-reglas-de-negocio y 04-glosario | Pavel (PO) | todas las specs | Módulo 1 | Abierta |
| PREG-55 | Aprobación de PAR-01 a PAR-11 y de las reglas "BA (propuesta)" | Pavel (PO) | specs de F-01, F-02, F-03, F-04, F-05, F-07, F-09 y F-11 | Módulo 1 | Abierta |
| PREG-56 | Umbrales de validación de supuestos SUP-01..SUP-06 | Pavel (PO) | el piloto | Piloto | Abierta |
| PREG-57 | Textos de rechazo y código HTTP de la validación de la solicitud | Pavel (PO / Arquitecto) | spec de F-02 | Módulo 1 | Abierta |
| PREG-58 | Textos de progreso, error y acciones deshabilitadas en las operaciones de IA | Pavel (PO / Diseño) | specs de F-03 y F-05 | Módulo 1 | Abierta |
| PREG-59 | Confirmación o deshacer al eliminar una actividad | Pavel (PO / Diseño) | spec de F-05 | Módulo 1 | Abierta |
| PREG-60 | Alcance de F-10 en v1: registro, eliminación de cuenta, sincronización y orden frente a F-11 | Pavel (PO / Arquitecto) | post-MVP (spec de F-10) | Lanzamiento público | Abierta |
| PREG-61 | Casos límite de la asociación de viajes y de la sesión en F-10 | Pavel (PO / Arquitecto) | post-MVP (spec de F-10) | Lanzamiento público | Abierta |
| PREG-62 | Itinerario guardado cuando falla la generación que lo reemplaza | Pavel (PO) | spec de F-01 | Módulo 1 | Abierta |
| PREG-63 | Almacenamiento local no disponible o lleno | Pavel (PO / Arquitecto) | spec de F-01 | Módulo 1 | Abierta |
| PREG-64 | Falla o demora de la fuente de lugares al resolver el destino | Pavel (PO / Arquitecto) | spec de F-02 | Módulo 1 | Abierta |
| PREG-65 | Franja sin actividades y día con 0 actividades | Pavel (PO / Diseño) | specs de F-04 y F-05 | Módulo 1 | Abierta |
| PREG-66 | Registro de proveedor y versión del modelo frente a los logs mínimos | Pavel (Arquitecto / PO) | specs de F-01 y F-03 | Módulo 1 | Abierta |
| PREG-67 | Momento en que se evalúa la "fecha actual" | Pavel (PO / Arquitecto) | spec de F-02 | Módulo 1 | Abierta |
| PREG-68 | Estado de confiabilidad de los datos de una actividad propia | Pavel (PO) | post-MVP (spec de F-09) | Lanzamiento público | Abierta |
| PREG-69 | Límite de 5 itinerarios locales alcanzado | Pavel (PO) | post-MVP (spec de F-11) | Lanzamiento público | Abierta |
| PREG-70 | Hosting de la web Next.js y cookie de sesión en el mismo sitio | Pavel (Arquitecto) | despliegue del piloto (C-01); la cookie afecta a HU-012 | Piloto | Abierta |
| PREG-71 | Servicio de ping y condiciones verificadas de Render Free y Supabase Free | Pavel (Arquitecto) | T-003, C-07, C-20 | Módulo 2 (implementación) | Abierta |
| PREG-72 | Servicio de CI, repositorio y presupuesto del golden set | Pavel (Arquitecto) | T-001, RT-11 | Módulo 2 (implementación) | Abierta |
| PREG-73 | Tareas del backlog que esperan decisiones abiertas | Pavel (PO / Arquitecto) | tareas de despliegue, adaptadores reales y HU-012 | Módulo 2 (implementación) | Abierta |
| PREG-74 | T-024 por encima del tope de 2 h | Pavel (PO / Arquitecto) | T-024 | Módulo 2 (implementación) | Abierta |
| PREG-75 | Valores abiertos que el código deja configurables | Pavel (Arquitecto) | T-012, T-013, T-014 | Módulo 2 (implementación) | Abierta |
| PREG-76 | Aprobación de las dependencias antes de T-001 | Pavel (Arquitecto) | T-001 (y T-006, T-012, T-016, T-022) | Módulo 2 (implementación) | Abierta |
| PREG-77 | Reglas Must de presentación en las tareas de UI (T-027+) | Pavel (PO / Diseño) | backlog de la Clase 3 (T-027+) y T-019 | Módulo 1 | Abierta |
| PREG-78 | Evidencia del test de 0 filas para el piloto | Pavel (Arquitecto / PO) | evidencia de RT-06 para el piloto | Piloto | Abierta |
| PREG-79 | Presupuesto de rendimiento y equipos de referencia de la web móvil | Pavel (Arquitecto) | plan de pruebas del piloto, SUP-04 | Piloto | Abierta |

---

### PREG-01: Lista canónica de "dato factual"
- **Contexto:** (Hallazgo QA C-02 y M-02 · CRÍTICA · bloquea F-04 y F-06) La Constitución (PRINCIPIO-002) y el glosario definen como dato factual el precio, el horario, la disponibilidad, las restricciones, los requisitos migratorios o sanitarios, el clima y el tiempo de traslado. F-06 agrega dirección, duración y distancia; RN-020 exige estado para la duración, y RN-105 trata la existencia del lugar como un dato con estado.
- **Incógnita:** ¿La dirección, la duración de una actividad, la distancia, la existencia del lugar y la duración del bloque de descanso son datos factuales, con estado y vigencia propios?
- **Opciones:**
  1. Ampliar la lista del glosario y fijar la vigencia de cada dato nuevo.
  2. Mantener la lista de la Constitución y quitar el estado de RN-020 y de F-06.
  3. Una lista intermedia (por ejemplo, solo dirección y existencia del lugar).
- **Acción requerida:** El PO fija la lista canónica en 04-glosario.md y se actualizan F-06, `RN-020`, `RN-061` y `RN-105`.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** F-04, F-06
- **Estado:** Abierta

---

### PREG-02: Bases de cálculo válidas para "≈ Estimada"
- **Contexto:** (C-05 · CRÍTICA · bloquea F-06 y F-07) Ninguna regla dice quién asigna el estado Estimada ni qué base de cálculo es válida. Una base como "precios promedio de la zona" puede encubrir una cifra inventada por el LLM. Esto incluye el tipo de cambio (`RN-054`) y la distancia que usa `RN-060`.
- **Incógnita:** ¿Qué bases de cálculo se aceptan, quién calcula una estimación y cómo se muestra una estimación que solo proviene del LLM?
- **Opciones:**
  1. Un catálogo cerrado de bases, siempre calculadas por el backend.
  2. Se aceptan estimaciones del LLM, pero siempre como Por confirmar.
  3. Se aceptan estimaciones del LLM como Estimada, declarando la base "estimación del modelo".
- **Acción requerida:** El PO y arquitectura definen una regla nueva en 03 §2.7 y ajustan `RN-020`, `RN-054`, `RN-060` y `RN-066`.
- **A quién preguntar:** Pavel (PO / Arquitecto)
- **Bloquea a:** F-06, F-07
- **Estado:** Abierta

---

### PREG-03: Postura sobre requisitos migratorios (propuesta de ADR)
- **Contexto:** (C-06 · CRÍTICA · bloquea F-03) El ejemplo de PRINCIPIO-008 muestra a la IA redactando "generalmente requieren visa… ! Por confirmar". El OUT-OF-SCOPE #4 y `RN-025` prohíben cualquier requisito redactado por la IA. `RN-069` regula un requisito Verificado que en v1 no puede existir, y no está definido si el aviso migratorio lleva estado.
- **Incógnita:** ¿La IA puede dar información general sobre requisitos o no redacta ninguno? ¿El aviso migratorio lleva estado de confiabilidad? ¿`RN-069` tiene uso en v1?
- **Opciones:**
  1. Alinear el ejemplo de PRINCIPIO-008 con el Alcance (ADR, cambio PATCH). — Elegida por la decisión vigente D-06.
  2. Alinear el Alcance y `RN-025` con PRINCIPIO-008. — Descartada por D-06.
  3. Mantener ambos y delimitar en qué casos aplica cada uno.
- **Acción requerida:** El PO aprueba un ADR y se actualizan PRINCIPIO-008 (si corresponde), `RN-024`, `RN-025`, `RN-059` y `RN-069`.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** F-03
- **Estado:** Resuelta en lo principal (D-06); abierto: si el aviso migratorio lleva estado de confiabilidad

---

### PREG-04: Destino internacional sin requisitos verificables
- **Contexto:** (C-07 · CRÍTICA · bloquea F-03) `RN-024` exige un enlace de la lista curada, pero no cubre un país sin enlace ni un enlace roto. No se pide la nacionalidad aunque el mercado es Latinoamérica. El Brief §1.4 menciona los permisos de viaje de menores, y no existe un aviso sanitario, tampoco para destinos nacionales.
- **Incógnita:** ¿Qué responde el sistema si no hay enlace curado? ¿Se pide la nacionalidad o se asume peruana? ¿Hay aviso para menores y aviso sanitario? ¿Cada cuánto se revisa la lista curada?
- **Opciones:**
  1. Asumir nacionalidad peruana y mostrar un aviso genérico que remite al consulado del país cuando no hay enlace. — Descartada en el Checkpoint 1 (AL-24): choca con D-01.
  2. Pedir la nacionalidad (agregarla a `RN-096`) y mantener enlaces por par origen–destino. — Descartada en el Checkpoint 1 (AL-24): agrega datos personales.
  3. No generar para destinos sin enlace curado, con un mensaje que lo explique, hasta incorporarlos. — Descartada por D-01 y D-04.
  4. Mostrar el aviso migratorio genérico en todo itinerario (cualquier destino, incluido Perú), con enlaces de la lista curada y sin pedir residencia ni nacionalidad. — **Elegida en el Checkpoint 1 (AL-24)**; `RN-024` y el glosario ya están actualizados. La lista curada se revisa cada 30 días (AL-30).
- **Acción requerida:** Pavel (PO y legal) define una regla Unwanted para el caso "país sin enlace curado o enlace roto", decide por separado los avisos para menores y sanitario, y actualiza `RN-024` y el Alcance DEP-04. `RN-096` no cambia: no se pide nacionalidad.
- **A quién preguntar:** Pavel (PO / Legal) · validación legal externa recomendada antes del lanzamiento público
- **Bloquea a:** F-03
- **Estado:** Resuelta en lo principal por AL-24 (aviso genérico en todo itinerario, sin pedir residencia ni nacionalidad; lista curada revisada cada 30 días por AL-30); abierto: qué muestra el aviso si el país de destino no tiene enlace curado o el enlace está roto, aviso para menores y aviso sanitario

---

### PREG-05: Mecanismo de cumplimiento de RN-102 (texto libre)
- **Contexto:** (C-01 · CRÍTICA · bloquea F-03) `RN-102` prohíbe datos factuales en el texto libre (nombre, descripción, nota, orientación). Sin embargo, el esquema y el golden set solo validan los campos estructurados.
- **Incógnita:** ¿Con qué mecanismo se garantiza que el texto libre no contenga precios, horarios, clima ni afirmaciones de seguridad?
- **Opciones:**
  1. Un esquema sin campos de texto libre (descripciones desde catálogo o plantillas).
  2. Un detector en la capa de verificación que rechace la respuesta.
  3. Solo casos del golden set.
  4. Una combinación de las anteriores.
- **Acción requerida:** Arquitectura registra la decisión en un ADR y se referencia desde `RN-102`.
- **A quién preguntar:** Pavel (Arquitecto)
- **Bloquea a:** F-03
- **Estado:** Abierta

---

### PREG-06: Registro de fuentes y fuente sugerida
- **Contexto:** (C-04 · CRÍTICA · bloquea F-06) `RN-106` exige que la `source` pertenezca al registro de fuentes, pero ese registro todavía no existe. Tampoco se sabe quién genera la `fuente_sugerida`: el LLM podría nombrar una entidad que no existe.
- **Incógnita:** ¿Dónde vive el registro, quién da de alta y de baja una fuente, cómo se vincula un dato con su fuente y quién genera la fuente sugerida?
  - (desde ARQ-Q-05) ¿Cuál es el contenido inicial del registro de fuentes (`source_registry.yaml`, C-16 de 07) y de los catálogos `estimation_basis` (qué bases se aceptan sigue en PREG-02) y `suggested_source`?
  - (desde BB-Q2) El contenido real de `source_registry.yaml` y `official_links.yaml` está bajo CODEOWNERS y lo escribe una persona: ¿Pavel lo carga antes de T-013, o el piloto arranca sin fuentes (todo dato queda `estimated` o `to_confirm`)? Para los enlaces de `official_links.yaml`, ver también PREG-04.
- **Opciones:**
  1. Archivo versionado en el repositorio, con CODEOWNERS humano; fuente sugerida desde un catálogo por tipo de dato.
  2. Tabla en la base de datos administrada por el PO; fuente sugerida desde un catálogo por tipo de dato.
  3. Cualquiera de los dos registros; la fuente sugerida la propone el LLM y el backend la valida contra el catálogo.
- **Acción requerida:** Arquitectura y el PO lo documentan en un ADR y actualizan el glosario ("Registro de fuentes"), `RN-067` y `RN-106`.
- **A quién preguntar:** Pavel (Arquitecto / PO)
- **Bloquea a:** Módulo 1 — F-06; la carga del contenido real (BB-Q2) hace falta además antes de T-013 y del piloto
- **Estado:** Abierta

---

### PREG-07: Dónde se recalcula la vigencia de los datos del invitado
- **Contexto:** (C-08 · CRÍTICA · bloquea F-01 y F-06) `RN-107` obliga a recalcular el estado al abrir un itinerario guardado. El backend no guarda datos del invitado (PRINCIPIO-005) y los clientes no deben duplicar reglas (PRINCIPIO-009). Ver el Anexo B #5 de 03.
- **Incógnita:** ¿Dónde se calcula el estado vigente de un itinerario de invitado?
  - (desde ARQ-Q-06) ¿Se acepta ADR-005 de 07 (endpoint sin estado `POST /v1/itineraries/reliability/refresh`, con atestación HMAC y respaldo con `valid_until`) como respuesta? Corresponde a la opción 1 combinada con la firma de PREG-21.
- **Opciones:**
  1. Un endpoint sin estado que recibe las fechas de consulta y devuelve los estados.
  2. Una función en `core` generada desde el backend.
  3. Volver a verificar los datos en cada apertura (consume cuota de APIs).
- **Acción requerida:** ADR de arquitectura; se actualizan `RN-107` y el Anexo B #5.
- **A quién preguntar:** Pavel (Arquitecto)
- **Bloquea a:** Módulo 1 — F-01, F-06
- **Estado:** Abierta

---

### PREG-08: Respuesta cuando no hay ninguna opción válida
- **Contexto:** (C-09 · CRÍTICA · bloquea F-03 y F-05) `RN-108` obliga a informar y a no modificar el itinerario, pero no dice qué más se ofrece. Tampoco está definido qué entrega la generación completa si el destino no tiene actividades compatibles con el grupo.
- **Incógnita:** ¿Qué recibe el viajero cuando ninguna actividad cumple su perfil?
- **Opciones:**
  1. Solo el mensaje informativo.
  2. El mensaje y una sugerencia de cambio (ritmo, fechas o destino).
  3. Una franja o un día "libre", marcado como tal.
- **Acción requerida:** El PO completa `RN-108` y se agrega una regla para la generación completa.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** F-03, F-05
- **Estado:** Abierta

---

### PREG-09: Respuestas del modelo que cumplen el esquema pero incumplen reglas
- **Contexto:** (C-10 · CRÍTICA · bloquea F-03 y F-05) `RN-109` y `RN-110` cubren el estado faltante y los días incompletos. No cubren las respuestas que cumplen el esquema pero violan `RN-021`, `RN-027` a `RN-030` o `RN-111`, ni las salidas truncadas ni el salvado parcial. Tampoco se dice si el reintento entra dentro de los 60 s.
- **Incógnita:** ¿Qué hace el backend con esas respuestas y cómo se presupuesta el reintento?
  - (desde ARQ-Q-10 y BB-Q4) ¿Qué margen tiene el plazo del servidor respecto al del cliente, y qué valores usa el piloto para `GENERATE_DEADLINE_S` (< 60 s) y `ADJUST_DEADLINE_S` (< 30 s)? Requiere medir latencias del proveedor elegido (PREG-13). Cómo se comporta el código mientras falten los valores está en PREG-75.
- **Opciones:**
  1. Rechazar la respuesta y reintentar según `RN-085`, dentro de los 60 s de `RN-083`.
  2. Rechazar y reintentar, con un presupuesto de tiempo propio para el reintento.
  3. Recortar las actividades que incumplen y entregar el resto, sin reintentar.
  4. Entregar con un aviso de las reglas incumplidas.
- **Acción requerida:** El PO y arquitectura definen una regla nueva en 03 §2.9 y ajustan `RN-083` y `RN-085`.
- **A quién preguntar:** Pavel (PO / Arquitecto)
- **Bloquea a:** Módulo 1 — F-03, F-05
- **Estado:** Abierta

---

### PREG-10: Stack tecnológico (propuesta de ADR)
- **Contexto:** (A-01 · ALTA · bloquea todas las specs) La Constitución (PRINCIPIO-006, PRINCIPIO-009, PRINCIPIO-012) fija FastAPI, React + Vite, React Native + Expo, Playwright y Maestro. El Brief §7 y el Alcance RES-04 lo dejan abierto (Next.js, Supabase, Node).
- **Incógnita:** ¿El stack está fijado por la Constitución o sigue abierto?
- **Opciones:**
  1. Fijado: se actualizan el Brief §7 y RES-04. — Descartada por la decisión vigente D-07.
  2. Abierto: un ADR modifica PRINCIPIO-006, PRINCIPIO-009 y PRINCIPIO-012 (cambio MAJOR si relaja algún principio).
- **Acción requerida:** Resuelta por la decisión vigente D-07 (opción 2). Acciones derivadas: adecuar los principios de 00 que nombraban herramientas (AL-22) y fijar el stack en el ADR del Checkpoint 2 de la Fase 1.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** todas las specs (como pregunta ya no bloquea; las specs esperan el ADR de stack del Checkpoint 2)
- **Estado:** **Resuelta** (decisión vigente D-07; el stack quedó fijado en el Checkpoint 2 por D-09 y D-10)

---

### PREG-11: Datos personales en el almacenamiento local (propuesta de ADR)
- **Contexto:** (A-04 · ALTA · bloquea F-01) PRINCIPIO-005 manda guardar los itinerarios de invitados en IndexedDB; PRINCIPIO-006 prohíbe guardar datos personales en IndexedDB; el Alcance RES-08 considera dato personal la edad de un menor. Además, para regenerar un día hay que conservar la solicitud completa (Anexo B #4).
- **Incógnita:** ¿Se pueden guardar localmente los rangos de edad? Si se puede, ¿cómo?
- **Opciones:**
  1. Declarar que el rango de edad sin identificadores no es dato personal a efectos de PRINCIPIO-006 (ADR con validación legal). — **Elegida en el Checkpoint 1 (AL-29)**.
  2. Cifrar el almacenamiento local. — Descartada (AL-29).
  3. No guardar los rangos y volver a pedirlos al regenerar. — Descartada (AL-29): rompe F-05.
- **Acción requerida:** ADR de arquitectura y legal; propuesta de ajuste de PRINCIPIO-005 o PRINCIPIO-006; se actualizan `RN-073` y el Anexo B #4.
- **A quién preguntar:** Pavel (Arquitecto / Legal) · validación legal externa recomendada antes del lanzamiento público
- **Bloquea a:** F-01 (se desbloquea al aprobarse el ADR que registra la enmienda de PRINCIPIO-006)
- **Estado:** **Resuelta** (AL-29: enmienda de PRINCIPIO-006; validación legal recomendada)

---

### PREG-12: Lista permitida al LLM frente a deduplicación y regeneración
- **Contexto:** (A-05 · ALTA · bloquea F-05) `RN-040` a `RN-042` necesitan que el LLM conozca el itinerario actual, pero `RN-096` no permite enviarlo.
- **Incógnita:** ¿Cómo se evitan los duplicados y se respeta el resto del plan al regenerar?
- **Opciones:**
  1. Ampliar la lista permitida con las actividades existentes, sin datos de menores.
  2. Deduplicar y validar en el backend sin enviar el itinerario al LLM.
- **Acción requerida:** El PO y legal deciden; se actualiza `RN-096` o `RN-040`.
- **A quién preguntar:** Pavel (PO / Legal) · validación legal externa recomendada antes del lanzamiento público
- **Bloquea a:** F-05
- **Estado:** Abierta

---

### PREG-13: Proveedor de IA sin entrenamiento dentro de USD 0
- **Contexto:** (A-07 · ALTA · bloquea F-03) El Alcance RES-09 ya está alineado con PRINCIPIO-005: no se usan proveedores que entrenan con los datos. Muchas capas gratuitas lo permiten, y el techo de gasto es USD 0.
- **Incógnita:** ¿Existe un proveedor gratuito que cumpla PRINCIPIO-005? Si no existe, ¿qué se hace?
  - (desde ARQ-Q-03) Además del no entrenamiento, ¿el proveedor garantiza la no retención de los datos y en qué país los procesa (decisión pendiente n.° 3 de 00)? Las garantías de la transferencia internacional siguen en PREG-44.
- **Opciones:**
  1. Proveedor gratuito con la opción de no entrenamiento verificada.
  2. Créditos promocionales de un proveedor de pago.
  3. Modelo abierto autoalojado.
  4. Aceptar un costo y revisar el techo de USD 0. — Descartada por la decisión vigente D-05.
- **Acción requerida:** El PO cierra la decisión pendiente #3 de la Constitución en un ADR; se actualizan las entradas del Alcance DEP-01 y RES-02.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** Piloto — reevaluado en el triaje de 07 y 08 (antes: F-03): F-03 se especifica y se construye detrás del puerto `LlmProvider` con un proveedor *fake* (07 ADR-006; 08, convenciones); el proveedor real hace falta para el piloto, y su selección, además, para el lanzamiento público (00, decisión pendiente n.° 3)
- **Estado:** Acotada; sigue abierta — la opción 4 (aceptar un costo) queda descartada por D-05; falta elegir entre proveedor gratuito sin entrenamiento, créditos promocionales o modelo autoalojado

---

### PREG-14: Aviso de privacidad y consentimiento (Ley N.° 29733)
- **Contexto:** (A-19 · ALTA · bloquea el piloto) No hay ninguna regla de aviso de privacidad ni de consentimiento antes de procesar la solicitud (backend, LLM, transferencia internacional). El Brief R7 menciona el consentimiento del padre o tutor, y no se trata el caso de un usuario menor de edad.
- **Incógnita:** ¿Qué aviso y qué consentimiento se piden, en qué momento y cómo se trata a un usuario menor de edad?
- **Opciones:**
  1. Aviso de privacidad informativo antes de la primera generación.
  2. Consentimiento explícito (casilla) antes de generar.
  3. Consentimiento solo en los términos del piloto, fuera de la app.
- **Acción requerida:** Legal y el PO deciden; se agregan reglas en 03 §2.11, incluida la edad mínima de uso si corresponde.
- **A quién preguntar:** Pavel (Legal / PO) · validación legal externa recomendada antes del lanzamiento público
- **Bloquea a:** el piloto
- **Estado:** Abierta

---

### PREG-15: Frontera del MVP: precios, comidas y traslados
- **Contexto:** (A-18 · ALTA · bloquea F-03 y F-04) La descripción del MVP (02 §3) muestra precios; `RN-019` y `RN-021` programan comidas, y la carga diaria incluye traslados. Pero F-07 y F-08 son Should.
- **Incógnita:** ¿El MVP muestra precios por actividad, comidas y traslados?
- **Opciones:**
  1. Sí, como parte de F-03 y F-04 (se amplía el alcance del MVP).
  2. No: se quitan precios y comidas del MVP y de los ejemplos.
  3. Solo traslados, porque los exige la carga diaria.
- **Acción requerida:** El PO decide; se actualizan 02 §3, F-04, F-08 y el Anexo C de 03.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** F-03, F-04
- **Estado:** Abierta

---

### PREG-16: Qué significa "mobile" en v1
- **Contexto:** (A-03 · ALTA · bloquea F-01) PRINCIPIO-005 y `RN-073` nombran el almacenamiento de Expo, y el Alcance RES-11 propone una PWA, sin cerrar la decisión. Faltan, además, reglas para el borrado automático del almacenamiento web (por ejemplo, Safari en iOS), para varias pestañas editando el mismo itinerario y para la app en segundo plano durante una operación de IA.
- **Incógnita:** ¿Qué es "mobile" en v1 y cómo se comporta en esos casos?
- **Opciones:**
  1. Solo PWA, pidiendo instalarla para reducir el borrado automático.
  2. App Expo con distribución de prueba.
  3. Ambas.
- **Acción requerida:** El PO y arquitectura cierran RES-11, agregan reglas para varias pestañas y segundo plano, y ajustan `RN-073` y `RN-075`.
- **A quién preguntar:** Pavel (PO / Arquitecto)
- **Bloquea a:** F-01
- **Estado:** Resuelta en lo principal (decisión vigente D-10, 2026-11-07): "mobile" en v1 = la web responsive en el navegador del celular; la app Expo es post-v1 (opciones 2 y 3 descartadas para v1). Siguen abiertas las reglas de borrado automático del almacenamiento web (p. ej., Safari en iOS), varias pestañas editando el mismo itinerario y pestaña en segundo plano durante una operación de IA.

---

### PREG-17: Definición de "dispositivo", límite de operaciones y cambio de dispositivo
- **Contexto:** (A-16 y M-10 · ALTA · bloquea F-03 y F-05) `RN-043` limita las operaciones por dispositivo, pero "dispositivo" no está definido. Un identificador persistido puede ser dato personal, el modo incógnito evade el límite, no hay aviso de que el itinerario no pasa a otro dispositivo, y "datos de viaje" tampoco está definido.
- **Incógnita:** ¿Qué identifica a un dispositivo, cómo se evita la evasión del límite, se avisa de la no portabilidad y qué incluye "datos de viaje"?
  - (desde ARQ-Q-07) ¿Un `device_id` UUID v4 anónimo en IndexedDB es compatible con PRINCIPIO-006 (sin datos identificativos en el almacenamiento local)? ¿Se acepta el contador en memoria del proceso de ADR-008 de 07, que se pone en cero con cada reinicio o despliegue, o se prefiere persistirlo, lo que choca con "0 filas" de PRINCIPIO-005?
- **Opciones:**
  1. Identificador aleatorio generado en el cliente (no es personal, pero es fácil de evadir).
  2. Límite por IP con hash y rotación (más robusto, pero más sensible).
  3. Un límite global del servicio además del límite por dispositivo.
- **Acción requerida:** ADR de arquitectura y legal. Se definen "dispositivo" y "datos de viaje" en el glosario, se actualizan `RN-043`, `RN-074` y `RN-089`, y se agrega una regla de aviso de no portabilidad si se aprueba.
- **A quién preguntar:** Pavel (Arquitecto / Legal) · validación legal externa recomendada antes del lanzamiento público
- **Bloquea a:** Módulo 1 — F-03, F-05
- **Estado:** Abierta

---

### PREG-18: Pérdida de conexión
- **Contexto:** (A-17 · ALTA · bloquea F-01 y F-05) Si el backend completó la operación y se perdió la respuesta, la operación cuenta como exitosa. No está definido qué es "sin conexión". `RN-090` y `RN-091` suponen que la app carga sin red, pero F-14 es Could. Tampoco se dice si un corte de conexión es una "falla" para `RN-088`.
- **Incógnita:** ¿Cómo se manejan las operaciones interrumpidas y qué debe funcionar sin red en el MVP?
  - (desde ARQ-Q-09) ¿Se aprueba la propuesta de 07 §5.2 para la opción 1: cabecera `Idempotency-Key` por operación, con la respuesta guardada 10 min en la memoria del proceso [ARQ-propuesta], para no descontar cupo cuando la respuesta se pierde?
- **Opciones:**
  1. Idempotencia por `request_id`: el reintento devuelve el resultado ya generado sin consumir el límite.
  2. Sin idempotencia: la operación perdida se descuenta.
  3. Un service worker mínimo en el MVP, para que el indicador de "Sin conexión" de `RN-091` pueda mostrarse.
- **Acción requerida:** Arquitectura y el PO definen "sin conexión" en el glosario y ajustan `RN-088` y `RN-090` a `RN-093`.
- **A quién preguntar:** Pavel (Arquitecto / PO)
- **Bloquea a:** Módulo 1 — F-01, F-05
- **Estado:** Abierta

---

### PREG-19: Qué es una "consulta" que siempre recibe respuesta
- **Contexto:** (A-22 · ALTA · bloquea F-03 y F-06) PRINCIPIO-002 y PRINCIPIO-008 dicen que "toda consulta recibe respuesta", pero el glosario prohíbe la palabra "consulta" y no la define.
- **Incógnita:** ¿A qué interacciones aplica esa garantía?
- **Opciones:**
  1. A toda solicitud de viaje.
  2. A toda operación de IA.
  3. A todo dato factual (siempre se muestra, aunque sea Por confirmar).
  4. A todas las anteriores.
- **Acción requerida:** El PO lo define en el glosario; si cambia la redacción de la Constitución, mediante un ADR PATCH.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** F-03, F-06
- **Estado:** Abierta

---

### PREG-20: Idioma del itinerario
- **Contexto:** (A-02 · ALTA · antes de la spec de F-02) PRINCIPIO-011 exige que el idioma del itinerario sea elegible, independiente del de la interfaz. En v1 solo existe es-419 y `RN-096` no incluye el idioma.
- **Incógnita:** ¿Se muestra un selector de idioma en v1?
- **Opciones:**
  1. Omitir el selector en v1 y registrarlo como excepción en un ADR. — **Elegida en el Checkpoint 1 (AL-26)**: v1 solo es-419, sin selector; enmienda constitucional 2.0.0.
  2. Mostrar el selector solo con es-419 y agregar el idioma a `RN-096`. — Descartada (AL-26).
- **Acción requerida:** El PO decide; se actualizan 02 §0 y `RN-096`.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** spec de F-02 (ya no bloquea: resuelta)
- **Estado:** **Resuelta** (AL-26: v1 solo es-419, sin selector; enmienda constitucional 2.0.0)

---

### PREG-21: Integridad de los estados que reenvía el cliente
- **Contexto:** (A-06 · ALTA · antes de las specs de F-05 y F-10) Al regenerar un día o al migrar un viaje (`RN-082`), el backend recibe del cliente estados y fechas de consulta que no puede comprobar. Así, un "Verificada" puede llegar a la cuenta sin pasar por la capa de verificación.
- **Incógnita:** ¿Cómo se protege la integridad de esos datos?
  - (desde ARQ-Q-27) Si se elige la firma HMAC (opción 1; ADR-005 de 07): ¿qué período de aceptación tiene el `kid` anterior tras rotar `ATTESTATION_KEY`, frente a itinerarios guardados durante mucho tiempo? ¿Un dato firmado con un `kid` retirado se rechaza con 422 (caso c de 07 §3.2) o se degrada a `to_confirm` como vigencia vencida (caso d)?
- **Opciones:**
  1. El backend firma los estados (HMAC) y los valida al recibirlos.
  2. Se vuelve a verificar todo dato Verificada recibido del cliente.
  3. Todo Verificada que llega del cliente se degrada a Por confirmar.
- **Acción requerida:** ADR de arquitectura; se actualizan `RN-041` y `RN-082`.
- **A quién preguntar:** Pavel (Arquitecto)
- **Bloquea a:** Módulo 1 — specs de F-05 y F-10
- **Estado:** Abierta

---

### PREG-22: Franjas horarias
- **Contexto:** (A-10 · ALTA · antes de la spec de F-04) `RN-018` asigna cada actividad a una sola franja. Sin embargo, hay excursiones de 540 min (el ejemplo de `RN-029`) y tours que salen antes de las 06:00, y los límites de las 12:00 y las 18:00 son ambiguos.
- **Incógnita:** ¿Cómo se ubican las actividades largas o de madrugada y a qué franja pertenecen los límites?
- **Opciones:**
  1. Actividades que ocupan varias franjas, con inicio y fin.
  2. Agregar una franja "Madrugada" (00:00–06:00).
  3. Intervalos semiabiertos: Mañana [06:00, 12:00), Tarde [12:00, 18:00), Noche [18:00, 23:00).
- **Acción requerida:** El PO decide; se actualizan PAR-04, `RN-018`, `RN-019` y el glosario.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** spec de F-04
- **Estado:** Abierta

---

### PREG-23: Día en curso, llegada y salida, zona horaria y viajes de 14 días
- **Contexto:** (A-11 · ALTA · antes de las specs de F-02 y F-03) Un viaje que empieza hoy, generado a las 21:00, incluye franjas que ya pasaron. No se piden el origen ni las horas de llegada y salida. La "fecha actual" se toma en Lima aunque el destino tenga otra zona horaria. Generar 14 días en 60 s puede no ser viable.
- **Incógnita:** ¿Cómo se tratan el día en curso, el primer y el último día, la zona horaria y los viajes largos?
- **Opciones:**
  1. Excluir las franjas ya pasadas del día en curso.
  2. Pedir horas de llegada y salida (opcionales) para el primer y el último día.
  3. Tomar la fecha actual en la zona horaria del destino.
  4. Hacer PAR-09 proporcional a la duración, o generar por partes.
- **Acción requerida:** El PO y arquitectura deciden; se actualizan `RN-004`, `RN-017` y PAR-09.
- **A quién preguntar:** Pavel (PO / Arquitecto)
- **Bloquea a:** specs de F-02 y F-03
- **Estado:** Abierta

---

### PREG-24: A qué fecha se refiere una verificación
- **Contexto:** (A-12 · ALTA · antes de la spec de F-06) Que un dato esté Verificada hoy no garantiza que siga así en la fecha del viaje (temporada, disponibilidad a meses vista). `RN-111` depende de esta respuesta.
- **Incógnita:** ¿La verificación vale para la fecha de consulta o para la fecha del viaje?
- **Opciones:**
  1. Vale para la fecha de consulta, y la etiqueta lo indica.
  2. Los datos que dependen de una fecha futura (disponibilidad, horario estacional) nunca son Verificada si el viaje cae fuera de la vigencia.
  3. Solo es Verificada si la fuente declara validez para la fecha del viaje.
- **Acción requerida:** El PO decide; propuesta de ajuste de PRINCIPIO-003; se actualizan `RN-068` y `RN-111`.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** spec de F-06
- **Estado:** Abierta

---

### PREG-25: Catálogo de necesidades del grupo
- **Contexto:** (A-13 · ALTA · antes de las specs de F-02 y F-03) El catálogo cerrado no tiene valores definidos (PRINCIPIO-004 menciona cochecito, movilidad reducida, restricciones alimentarias y menor exigencia física). No se define cómo afecta cada necesidad a la generación, y "restricciones alimentarias" podría ser un dato sensible.
- **Incógnita:** ¿Qué valores tiene el catálogo y qué efecto tiene cada uno?
- **Opciones:**
  1. Adoptar los cuatro valores de PRINCIPIO-004; cada necesidad excluye las actividades incompatibles.
  2. Adoptar los cuatro valores de PRINCIPIO-004; cada necesidad solo agrega un aviso orientativo.
  3. Un catálogo reducido, sin restricciones alimentarias.
- **Acción requerida:** El PO y legal deciden; el catálogo va al glosario y la regla de efecto a 03 §2.4.
- **A quién preguntar:** Pavel (PO / Legal) · validación legal externa recomendada antes del lanzamiento público
- **Bloquea a:** specs de F-02 y F-03
- **Estado:** Abierta

---

### PREG-26: Aviso de ritmo Activo con menores de 0–5 años
- **Contexto:** (A-14 · ALTA · antes de la spec de F-02) `RN-036` ya se dispara por estado y no por evento. No está definido si el aviso bloquea, cuál es su texto fijo, ni si se limitan los traslados para menores de 0–5 años (Brief §2.4).
- **Incógnita:** ¿Cómo se comporta el aviso y se limitan los traslados?
- **Opciones:**
  1. Aviso informativo, no bloqueante, sin límite de traslados.
  2. Confirmación obligatoria antes de generar.
  3. Aviso informativo y un límite por traslado para grupos con menores de 0–5 años.
- **Acción requerida:** El PO decide; el texto se fija como clave de traducción y se actualiza `RN-036`.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** spec de F-02
- **Estado:** Abierta

---

### PREG-27: Ejemplos de la Constitución y presentación del texto obligatorio (propuesta de ADR)
- **Contexto:** (A-21 · ALTA · antes de la spec de F-06) Los ejemplos de PRINCIPIO-002 (agrega "directamente") y de PRINCIPIO-008 (le falta "No pudimos verificar este dato") no usan los textos obligatorios de PRINCIPIO-003. Tampoco se define si el texto obligatorio se muestra en línea o en una vista expandible.
- **Incógnita:** ¿Se corrigen esos ejemplos y cómo se presenta el texto obligatorio en pantalla?
- **Opciones:**
  1. ADR PATCH que corrige los ejemplos y texto completo siempre en línea.
  2. ADR PATCH que corrige los ejemplos y etiqueta en línea con el texto completo en una vista expandible y accesible.
- **Acción requerida:** El PO y diseño deciden; ADR PATCH y regla de presentación en 03 §2.12.
- **A quién preguntar:** Pavel (PO / Diseño)
- **Bloquea a:** spec de F-06
- **Estado:** Abierta

---

### PREG-28: Restricciones de edad vencidas y otras restricciones
- **Contexto:** (M-01 · MEDIA · antes de la spec de F-03) No está definido si una edad mínima Verificada que venció (y pasó a Por confirmar) sigue excluyendo la actividad, si la altura, la edad máxima o la salud excluyen, ni si una edad "Por confirmar" con un menor de 0–2 años requiere un aviso adicional.
- **Incógnita:** ¿Qué restricciones excluyen una actividad y en qué estado?
- **Opciones:**
  1. Una restricción vencida sigue excluyendo, por prudencia.
  2. Una restricción vencida solo informa.
  3. Altura, edad máxima y salud excluyen igual que la edad mínima.
- **Acción requerida:** El PO decide; se actualizan `RN-030` a `RN-032`.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** spec de F-03
- **Estado:** Abierta

---

### PREG-29: Criterio de "coincide" en las alternativas
- **Contexto:** (M-03 · MEDIA · antes de la spec de F-05) `RN-040` no define qué significa que una alternativa "coincide" con una actividad existente. Tampoco se evitan duplicados al regenerar un día ni se excluyen las alternativas ya rechazadas.
- **Incógnita:** ¿Cuándo dos actividades son la misma?
- **Opciones:**
  1. Mismo lugar (identificador de la fuente de lugares).
  2. Mismo nombre normalizado.
  3. Mismo tipo de actividad.
- **Acción requerida:** El PO decide, también si se excluyen las alternativas rechazadas; se actualizan `RN-040` y `RN-041`.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** spec de F-05
- **Estado:** Abierta

---

### PREG-30: Conteo del límite de operaciones
- **Contexto:** (M-04 · MEDIA · antes de la spec de F-03) `RN-043` no define en qué zona horaria se muestra la hora de desbloqueo, si cuentan el reintento (`RN-085`) y el failover (`RN-086`), ni qué límite tiene un usuario registrado.
- **Incógnita:** ¿Cómo se cuenta y se comunica el límite?
  - (desde ARQ-Q-08 y BB-Q6) ¿Una respuesta `no_valid_option` (alternativa o regenerar día, RN-108) cuenta como operación exitosa para PAR-07? T-012 lo deja como flag de configuración y sus tests cubren ambos valores.
- **Opciones:**
  1. Hora en America/Lima; reintento y failover cuentan como una sola operación; el mismo límite para registrados.
  2. Hora en la zona del dispositivo; reintento y failover no cuentan; límite por cuenta para registrados.
- **Acción requerida:** El PO decide; se actualizan `RN-043` y PAR-07.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** Módulo 1 — spec de F-03
- **Estado:** Abierta

---

### PREG-31: Medición de los tiempos de espera
- **Contexto:** (M-05 · MEDIA · antes de la spec de F-03) No se define dónde se miden los 60 s y los 30 s, qué significa "no responda" en `RN-086`, ni se confirma que el proveedor secundario cumpla `RN-099`.
- **Incógnita:** ¿Desde dónde se mide el tiempo y qué dispara el failover?
- **Opciones:**
  1. Medición en el cliente, desde que se pulsa el botón; failover solo por timeout.
  2. Medición en el backend, desde la llamada al proveedor; failover por timeout o error 5xx.
  3. Medición en el backend; failover por timeout, error 5xx o 429.
- **Acción requerida:** Arquitectura decide y lo registra en el ADR de proveedor; se actualizan `RN-083`, `RN-084` y `RN-086`.
- **A quién preguntar:** Pavel (Arquitecto)
- **Bloquea a:** spec de F-03
- **Estado:** Abierta

---

### PREG-32: Unidad y zona horaria de la vigencia
- **Contexto:** (M-06 · MEDIA · antes de la spec de F-06) No está definido si "7 días" son 168 h o días de calendario, ni en qué zona horaria se muestra "a las {HH:mm}".
- **Incógnita:** ¿Cómo se mide y se muestra la vigencia?
- **Opciones:**
  1. 168 h exactas; hora en America/Lima.
  2. Días de calendario hasta las 23:59; hora en la zona del destino.
  3. 168 h exactas; hora en la zona del dispositivo.
- **Acción requerida:** El PO decide; se actualizan `RN-068`, `RN-069` y 03 §0.4.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** spec de F-06
- **Estado:** Abierta

---

### PREG-33: Familias solo de adultos
- **Contexto:** (M-07 · MEDIA · antes de la spec de F-02) Una familia solo de adultos (abuelos con hijos adultos) queda obligada a elegir "Amigos". Con dos tipos posibles, `RN-011` no sabe cuál sugerir.
- **Incógnita:** ¿Se ajusta el catálogo de tipos de grupo?
- **Opciones:**
  1. Ampliar "Familia" a "≥ 2 viajeros con o sin menores".
  2. Renombrar "Amigos" a "Grupo de adultos".
  3. Mantener el catálogo actual y que `RN-011` sugiera todos los tipos válidos.
- **Acción requerida:** El PO decide; se actualizan el glosario ("Tipo de grupo") y `RN-011`.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** spec de F-02
- **Estado:** Abierta

---

### PREG-34: Medición de los KPIs del piloto
- **Contexto:** (M-12 · MEDIA · antes del piloto) El TTAI y el nivel de edición requieren telemetría (la conversión a cuenta pasa a post-MVP por AL-25), pero PRINCIPIO-005 impide persistir datos del invitado y la analítica es la decisión pendiente #5 de la Constitución.
- **Incógnita:** ¿Cómo se miden los KPIs sin violar PRINCIPIO-005?
- **Opciones:**
  1. Eventos anónimos agregados, con consentimiento.
  2. Medición solo en pruebas moderadas.
  3. Telemetría local que el tester exporta voluntariamente.
- **Acción requerida:** El PO cierra la decisión pendiente #5 en un ADR y se ajusta el Brief §5.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** el piloto
- **Estado:** Abierta

---

### PREG-35: Destinos regionales y con varias paradas
- **Contexto:** (M-13 · MEDIA · antes de la spec de F-02) Un destino se resuelve a una sola entrada; regiones (Valle Sagrado) y rutas (Lima–Paracas–Ica) no encajan.
- **Incógnita:** ¿Qué tipos de destino admite v1?
- **Opciones:**
  1. Solo un destino puntual.
  2. Destinos regionales como entrada única.
  3. Varias paradas, con días asignados a cada una.
- **Acción requerida:** El PO decide; se actualizan el glosario ("Destino") y `RN-007`.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** spec de F-02
- **Estado:** Abierta

---

### PREG-36: Paleta de estados y alcance de los logs
- **Contexto:** (M-14 · MEDIA · antes de las specs de F-06 y F-01) `RN-062` exige color, pero no hay paleta definida. `RN-097` no aclara si "logs del sistema" incluye el reporte de errores del cliente o la analítica.
- **Incógnita:** ¿Qué colores usan los estados y qué cubren los logs?
- **Opciones:**
  1. Paleta en design tokens con contraste verificado; logs solo del backend.
  2. Paleta en design tokens con contraste verificado; logs del backend y crash reporting del cliente, con el payload filtrado.
- **Acción requerida:** Diseño y arquitectura deciden; se actualizan `RN-062` y `RN-097`.
- **A quién preguntar:** Pavel (Diseño / Arquitecto)
- **Bloquea a:** specs de F-06 y F-01
- **Estado:** Abierta

---

### PREG-37: Confirmación antes de reemplazar el itinerario
- **Contexto:** (M-15 · MEDIA · antes de la spec de F-01) `RN-076` no define en qué momento se pide la confirmación ni qué pasa si el viaje guardado está en curso.
- **Incógnita:** ¿Cuándo se pide la confirmación y cómo se trata un viaje en curso?
- **Opciones:**
  1. Al abrir el formulario de una nueva solicitud.
  2. Al pulsar "Generar".
  3. Al pulsar "Generar", con un aviso adicional si el viaje guardado está en curso.
- **Acción requerida:** El PO decide; se actualiza `RN-076`.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** spec de F-01
- **Estado:** Abierta

---

### PREG-38: Orden de las actividades dentro de una franja
- **Contexto:** (B-09 · BAJA · antes de la spec de F-04) `RN-019` ordena las franjas, pero no las actividades dentro de una misma franja.
- **Incógnita:** ¿Con qué criterio se ordenan?
- **Opciones:**
  1. Por hora de inicio sugerida.
  2. Por proximidad geográfica.
  3. Según el orden que devuelve el LLM.
- **Acción requerida:** El PO decide; se actualiza `RN-019`.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** spec de F-04
- **Estado:** Abierta

---

### PREG-39: Invitado que crea cuenta a mitad del flujo
- **Contexto:** (A-15 · ALTA · post-MVP, F-10) No se define qué pasa con el formulario o con una operación en curso al crear cuenta, dónde se guardan las ediciones si el viajero rechaza la migración, si el límite pasa a ser por cuenta, ni qué ocurre con los viajes locales al cerrar sesión en un dispositivo compartido.
- **Incógnita:** ¿Cómo es la transición de invitado a usuario registrado?
- **Opciones:**
  1. Se puede crear la cuenta en cualquier momento: se conserva el formulario y se espera a que termine la operación en curso.
  2. Solo se puede crear la cuenta fuera del flujo de generación.
  3. Al cerrar sesión, los viajes ya asociados se borran del dispositivo; los no asociados se conservan.
- **Acción requerida:** El PO decide al especificar F-10; se agregan reglas en 03 §2.8.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** post-MVP (spec de F-10)
- **Estado:** Acotada; sigue abierta — la cuenta opcional existe y asocia los viajes de invitado (D-02) y F-10 sigue en Should de v1, post-MVP (AL-25); falta definir el flujo de transición de invitado a usuario registrado

---

### PREG-40: Definición del presupuesto
- **Contexto:** (M-08 · MEDIA · post-MVP, F-07) `RN-046` rechaza un presupuesto menor o igual que 0, pero no se define si un campo vacío equivale a 0, si el presupuesto es por persona, por grupo o por día, ni en qué moneda se ingresa frente a la del destino.
- **Incógnita:** ¿Qué representa el presupuesto y en qué moneda?
- **Opciones:**
  1. Vacío significa "sin presupuesto"; total del viaje para todo el grupo, en la moneda del viajero.
  2. Por persona y por día, en la moneda del destino.
  3. Total del viaje para el grupo, con moneda elegible.
- **Acción requerida:** El PO decide al especificar F-07; se actualizan `RN-046` a `RN-049`.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** post-MVP (spec de F-07)
- **Estado:** Abierta

---

## Preguntas agregadas en el Checkpoint 1 (2026-11-07)

PREG-41 a PREG-55 recogen los pendientes de 00 a 03 que no estaban en este documento (auditoría de alineación §5.3). PREG-56 sale de AL-30. Las líneas de origen corresponden a los archivos tras la revisión del Checkpoint 1 (00-constitucion.md la edita otra persona en paralelo y sus líneas pueden variar).

### PREG-41: Aprobación de la Constitución
- **Contexto:** (Auditoría de alineación §5.3 #1 · origen 00-constitucion.md:7 · bloquea todas las specs) La Constitución está en estado "Draft": las enmiendas 2.0.0 se aprobaron en el Checkpoint 1, pero la aprobación final del documento sigue pendiente. Sin Constitución aprobada, ninguna spec puede pasar a `approved` sin contradecir el orden de precedencia.)
- **Incógnita:** ¿Cuándo y en qué versión se aprueba 00-constitucion.md?
- **Opciones:**
  1. Aprobarla en su versión 2.0.0, con las enmiendas del Checkpoint 1 ya aplicadas.
  2. Aprobarla en el Checkpoint 2, junto con el ADR de stack (AL-22).
- **Acción requerida:** Pavel aprueba el documento y cambia su estado de "Draft" a "Aprobada" con fecha y versión.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** todas las specs
- **Estado:** Abierta

---

### PREG-42: Responsable del tratamiento de datos e inscripción ante la ANPDP
- **Contexto:** (Auditoría §5.3 #2 · origen 00-constitucion.md:182 (decisión pendiente #1) · bloquea el lanzamiento público) La Ley N.° 29733 exige identificar al responsable del tratamiento e inscribir el banco de datos ante la Autoridad Nacional de Protección de Datos Personales.)
- **Incógnita:** ¿Quién es el responsable del tratamiento y cuándo se inscribe el banco de datos?
- **Opciones:**
  1. Pavel como persona natural responsable; inscripción antes del lanzamiento público.
  2. Una institución (p. ej., la del diploma) como responsable durante el piloto; inscripción antes del lanzamiento público.
- **Acción requerida:** Pavel lo decide en un ADR de privacidad y lo refleja en el aviso de privacidad (PREG-14).
- **A quién preguntar:** Pavel (Legal) · validación legal externa recomendada antes del lanzamiento público
- **Bloquea a:** el lanzamiento público
- **Estado:** Abierta

---

### PREG-43: Retención de cuentas inactivas
- **Contexto:** (Auditoría §5.3 #3 · origen 00-constitucion.md:183 (decisión pendiente #2) · bloquea la spec de F-10 y el lanzamiento público) No hay plazo ni procedimiento para borrar cuentas inactivas; se vuelve relevante con F-10 (Should de v1, AL-25).)
- **Incógnita:** ¿Tras cuánto tiempo de inactividad se borra una cuenta y con qué aviso previo?
- **Opciones:**
  1. 12 meses de inactividad, con aviso por correo 30 días antes del borrado.
  2. 24 meses de inactividad, con aviso por correo 30 días antes del borrado.
  3. Sin borrado automático; solo borrado a pedido del usuario.
- **Acción requerida:** Pavel lo fija en un ADR de privacidad y se agrega una regla en 03 §2.8 al especificar F-10.
- **A quién preguntar:** Pavel (Legal / PO) · validación legal externa recomendada antes del lanzamiento público
- **Bloquea a:** spec de F-10 (post-MVP) y el lanzamiento público
- **Estado:** Abierta

---

### PREG-44: Transferencias internacionales de datos
- **Contexto:** (Auditoría §5.3 #4 · origen 00-constitucion.md:185 (decisión pendiente #4) · bloquea el lanzamiento público) El hosting en la nube y el proveedor de LLM pueden procesar datos fuera del Perú. PREG-14 lo menciona solo en su contexto.)
- **Incógnita:** ¿Qué garantías se exigen a los proveedores que procesan datos fuera del Perú y cómo se informa al viajero?
  - (desde ARQ-Q-32) ¿En qué región se despliegan Render (C-04) y Supabase (C-19), que procesan los datos de las cuentas?
- **Opciones:**
  1. Informarlo en el aviso de privacidad y elegir proveedores con cláusulas de protección de datos publicadas.
  2. Restringir el procesamiento a proveedores con región de datos declarada.
- **Acción requerida:** Pavel lo documenta en el ADR de proveedor de LLM (PREG-13) y en el ADR de stack, y lo refleja en el aviso de privacidad (PREG-14).
- **A quién preguntar:** Pavel (Legal / Arquitecto) · validación legal externa recomendada antes del lanzamiento público
- **Bloquea a:** Lanzamiento público
- **Estado:** Abierta

---

### PREG-45: Soporte y moderación
- **Contexto:** (Auditoría §5.3 #5 · origen 01-project-brief.md:165 · bloquea el piloto) El Brief marca como [PD] cómo se atienden los reportes de datos incorrectos, con un equipo de 1 persona.)
- **Incógnita:** ¿Por qué canal reportan los testers un dato incorrecto y en qué plazo se atiende?
- **Opciones:**
  1. Un formulario o correo único atendido por Pavel, con respuesta en ≤ 72 h durante el piloto.
  2. Sin canal en la app: los reportes se recogen en la encuesta post-uso.
- **Acción requerida:** Pavel decide y lo registra en el Brief §4.2 y en el plan del piloto.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** el piloto
- **Estado:** Abierta

---

### PREG-46: Meta de realismo percibido
- **Contexto:** (Auditoría §5.3 #6 · origen 01-project-brief.md:200 · bloquea el piloto) La meta "media ≥ 4,0 (escala 1–5)" está marcada [PD: confirmar].)
- **Incógnita:** ¿Se confirma la meta de realismo percibido?
- **Opciones:**
  1. Confirmar media ≥ 4,0.
  2. Bajar a media ≥ 3,5 y declararla indicativa (Brief R11).
- **Acción requerida:** Pavel decide y se actualiza el Brief §5.2.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** el piloto
- **Estado:** Abierta

---

### PREG-47: Instrumentos de satisfacción: CSAT, SUS o ambos
- **Contexto:** (Auditoría §5.3 #7 · origen 01-project-brief.md:206 y 294 · bloquea el piloto) El Brief separó "CSAT / SUS" en dos KPIs, pero no confirma si se aplican ambos.)
- **Incógnita:** ¿Qué instrumentos se aplican en la encuesta post-uso?
- **Opciones:**
  1. Ambos: CSAT ≥ 80 % y SUS ≥ 75.
  2. Solo SUS ≥ 75.
  3. Solo CSAT ≥ 80 %.
- **Acción requerida:** Pavel decide y se actualizan el Brief §5.2 y el Anexo #3.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** el piloto
- **Estado:** Abierta

---

### PREG-48: Umbral único de desviación presupuestaria
- **Contexto:** (Auditoría §5.3 #8 · origen 01-project-brief.md:214 y 294 · post-MVP, F-07) El KPI dice "≤ ±20 % [PD: confirmar umbral único]". PREG-40 trata la definición del presupuesto, no este KPI.)
- **Incógnita:** ¿Cuál es el umbral de desviación y sobre qué se mide?
- **Opciones:**
  1. ≤ ±20 % sobre el gasto diario estimado.
  2. ≤ ±20 % sobre el total del viaje.
  3. ≤ ±15 % sobre el gasto diario estimado.
- **Acción requerida:** Pavel decide al especificar F-07 y se actualiza el Brief §5.3.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** post-MVP (spec de F-07)
- **Estado:** Abierta

---

### PREG-49: Tamaño mínimo de la muestra de auditoría
- **Contexto:** (Auditoría §5.3 #9 · origen 01-project-brief.md:217 y 295 · bloquea el piloto) Auditar el 15–20 % de los itinerarios del piloto da solo 3–30 itinerarios, insuficiente para afirmar "< 5 %" de error crítico.)
- **Incógnita:** ¿Cuántos itinerarios se auditan como mínimo?
- **Opciones:**
  1. Un mínimo fijo de 30 itinerarios, o el 100 % si hay menos.
  2. El 15–20 % de los itinerarios, declarando la meta como indicativa.
- **Acción requerida:** Pavel decide y se actualiza el Brief §5.3.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** el piloto
- **Estado:** Abierta

---

### PREG-50: Límites vigentes de las capas gratuitas de mapas y lugares
- **Contexto:** (Auditoría §5.3 #10 · origen 01-project-brief.md:245 y 296 · bloquea F-03, F-06 y el piloto) El Brief R5 pide verificar los límites antes del piloto. PREG-13 cubre solo el LLM; los límites de mapas y lugares (Alcance DEP-02 y DEP-05) no están verificados.)
- **Incógnita:** ¿Qué proveedor gratuito de mapas y lugares se usa y alcanzan sus límites para 50 usuarios con PAR-07 (15 operaciones por dispositivo cada 24 h)?
  - (desde ARQ-Q-04) La fuente debe cubrir la resolución del destino, la existencia del lugar, el cierre permanente y los horarios (C-15 de 07; `RN-007`, `RN-021`, `RN-105`): ¿el proveedor elegido cubre las cuatro y con qué límites gratuitos?
- **Acción requerida:** Pavel (arquitectura) verifica los límites vigentes, los registra con fecha en el ADR de stack y ajusta DEP-02, DEP-05 y PAR-07 si no alcanzan.
- **A quién preguntar:** Pavel (Arquitecto)
- **Bloquea a:** Piloto — reevaluado en el triaje de 07 y 08 (antes: F-03, F-06 y el piloto): las specs y el backlog trabajan detrás del puerto `PlacesSource` con una fuente *fake* (08, convenciones); el proveedor real y sus límites hacen falta para el piloto
- **Estado:** Abierta

---

### PREG-51: Fecha de sustentación y calendario del piloto
- **Contexto:** (Auditoría §5.3 #11 · origen 01-project-brief.md:286 y 293 · bloquea el piloto) La fecha de sustentación es [PD] y el piloto no está ubicado en el calendario.)
- **Incógnita:** ¿Cuál es la fecha de sustentación y en qué semanas corre el piloto?
- **Opciones:**
  1. Piloto durante las vacaciones escolares de enero a marzo.
  2. Piloto en las 4 semanas previas a la sustentación, sea cual sea su fecha.
- **Acción requerida:** Pavel fija ambas fechas y se actualizan el Brief §7 y el Anexo #2.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** el piloto
- **Estado:** Abierta

---

### PREG-52: Fuente del supuesto de rebote por registro obligatorio (S6)
- **Contexto:** (Auditoría §5.3 #12 · origen 01-project-brief.md:142 y 297 · no bloquea specs) El Brief estima un rebote del 60–80 % si se exige registro, sin fuente.)
- **Incógnita:** ¿Se busca una fuente secundaria o se valida en el piloto?
- **Opciones:**
  1. Buscar una fuente secundaria y citarla como [E].
  2. Mantenerlo como [S] y no validarlo en el piloto (no hay registro obligatorio que comparar).
- **Acción requerida:** Pavel decide y se actualiza el Brief §3.4 y §6.2.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** ninguna spec (solo la justificación del Brief §3.4)
- **Estado:** Abierta

---

### PREG-53: Modelo de monetización
- **Contexto:** (Auditoría §5.3 #13 · origen 01-project-brief.md:156 · post-MVP) La monetización está "PENDIENTE de definición"; v1 es gratuita (D-01). Las hipótesis H1–H3 del Brief §4.3 no son decisiones.)
- **Incógnita:** ¿Qué modelo de monetización se adopta después de v1?
- **Opciones:**
  1. H1 — Afiliación transparente.
  2. H2 — Freemium.
  3. H3 — Alianzas institucionales B2B.
- **Acción requerida:** Pavel decide después del piloto, con métricas de validación, en un ADR; ninguna decisión puede alterar el orden de las recomendaciones (Brief §4.3, línea roja).
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** post-MVP (nada de v1)
- **Estado:** Abierta

---

### PREG-54: Aprobación de 02-alcance, 03-reglas-de-negocio y 04-glosario
- **Contexto:** (Auditoría §5.3 #14 · origen 02-alcance.md:5 y 03-reglas-de-negocio.md:6 (también 04-glosario.md:6) · bloquea todas las specs) Los tres documentos están en estado "Borrador para revisión" del PO.)
- **Incógnita:** ¿Se aprueban 02, 03 y 04 tal como quedaron tras el Checkpoint 1?
- **Opciones:**
  1. Aprobarlos tras el Checkpoint 1.
  2. Aprobarlos cuando se cierren PREG-01 a PREG-19.
- **Acción requerida:** Pavel revisa y cambia el estado de cada documento a "Aprobado" con fecha.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** todas las specs
- **Estado:** Abierta

---

### PREG-55: Aprobación de PAR-01 a PAR-11 y de las reglas "BA (propuesta)"
- **Contexto:** (Auditoría §5.3 #15 · origen 03-reglas-de-negocio.md:1492–1502 (Anexo A) y 1514 (Anexo B #7) · bloquea las specs de F-01, F-02, F-03, F-04, F-05, F-07, F-09 y F-11) Los 11 parámetros del Anexo A y 29 reglas con fuente "BA (propuesta, a confirmar por PO)" (RN-002 a RN-006, RN-009 a RN-011, RN-018, RN-027 a RN-029, RN-033, RN-036, RN-040, RN-043, RN-044, RN-046, RN-052, RN-077, RN-083 a RN-085, RN-088 a RN-091, RN-093 y RN-101) no están aprobados. PREG-22, PREG-23 y PREG-30 tocan solo PAR-04, PAR-09 y PAR-07.)
- **Incógnita:** ¿Se aprueban los parámetros y las reglas propuestas por el BA?
- **Opciones:**
  1. Aprobar todo en bloque y ajustar PAR-07 y PAR-09 tras medir SUP-03.
  2. Aprobar por funcionalidad, al aprobar cada spec.
- **Acción requerida:** Pavel aprueba y en 03 la fuente de cada regla aprobada pasa de "BA (propuesta, a confirmar por PO)" a "PO (aprobada AAAA-MM-DD)".
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** specs de F-01, F-02, F-03, F-04, F-05, F-07, F-09 y F-11
- **Estado:** Abierta

---

### PREG-56: Umbrales de validación de supuestos SUP-01..SUP-06
- **Contexto:** (Checkpoint 1, AL-30 · origen 02-alcance.md:89–94 · bloquea el piloto) SUP-01, SUP-03, SUP-04, SUP-05 y SUP-06 no tienen un umbral numérico, así que no se pueden dar por validados ni refutados. SUP-02 se valida de forma cualitativa (entrevistas) y no requiere umbral. Por decisión del Checkpoint 1, los umbrales se fijan tras las pruebas internas, no ahora.)
- **Incógnita:** ¿Qué umbral valida o refuta cada supuesto?
  - (desde ARQ-Q-19) Por el riesgo de desalojo del almacenamiento del navegador en Safari iOS y en Android de gama baja: ¿qué umbral de SUP-04 se mide? Los equipos y navegadores de referencia están en PREG-79, y las reglas de borrado automático, en PREG-16.
- **Opciones:**
  1. Partir de la propuesta del auditor (sin evidencia): SUP-01 ≥ 50 % de itinerarios conservados tras la primera sesión; SUP-03 = PAR-07 × 50 usuarios sin superar la capa gratuita; SUP-04 = 0 pérdidas en 4 navegadores o dispositivos de prueba; SUP-05 ≥ 30 % de datos factuales en ✓ Verificada en 3–5 destinos; SUP-06 ≥ 70 % de los ajustes resueltos con F-05.
  2. Fijar valores propios a partir de las mediciones de las pruebas internas.
- **Acción requerida:** Tras las pruebas internas previas al piloto, Pavel fija el umbral de cada supuesto en 02 §4.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** Piloto
- **Estado:** Abierta

---

## Preguntas agregadas en el triaje de 06-user-stories (2026-11-07)

PREG-57 a PREG-69 recogen las preguntas del Agente PO y del Agente QA de 06-user-stories.md (PO-Q1 a PO-Q39) que no tienen respuesta en 02, 03 o 04 ni están cubiertas por una PREG anterior. Varias PO-Q del mismo tema se agrupan en una sola PREG. La trazabilidad completa PO-Qn → destino está en la sección 4 y en "§ Preguntas del QA" de 06.

### PREG-57: Textos de rechazo y código HTTP de la validación de la solicitud
- **Contexto:** (Origen 06 HU-002, HU-003 / PO-Q1 y PO-Q2 (textos de RN-011) · bloquea la spec de F-02) `RN-004`, `RN-008`, `RN-010`, `RN-012` y `RN-016` obligan a rechazar la solicitud, pero no fijan el texto que ve el viajero. `RN-011` solo da el texto del caso "Familia sin menores", no el de Solo, Pareja o Amigos con una composición que no coincide. `RN-095` fija HTTP 422 solo para los campos no definidos en el esquema. La composición de cada tipo de grupo ya está en 04-glosario ("Tipo de grupo"); qué tipo sugiere `RN-011` cuando hay más de uno válido sigue en PREG-33.
- **Incógnita:** ¿Qué texto exacto muestra cada rechazo (fecha de inicio anterior a la fecha actual, tipo de grupo ausente o múltiple, más de 10 viajeros, rango de edad fuera del catálogo, ritmo fuera del catálogo y composición que no coincide con Solo, Pareja o Amigos) y qué código HTTP devuelve el backend en esos casos?
- **Opciones (código HTTP):**
  1. HTTP 422 para todo rechazo de validación, con un código de error propio por regla.
  2. HTTP 400 para los rechazos de reglas de negocio y HTTP 422 solo para los campos no definidos (`RN-095`).
- **Acción requerida:** El PO fija los textos como claves de traducción y se agregan a los ejemplos de `RN-004`, `RN-008`, `RN-010`, `RN-011`, `RN-012` y `RN-016`; arquitectura registra el código HTTP en el contrato de la API.
- **A quién preguntar:** Pavel (PO / Arquitecto)
- **Bloquea a:** spec de F-02
- **Estado:** Abierta

---

### PREG-58: Textos de progreso, error y acciones deshabilitadas en las operaciones de IA
- **Contexto:** (Origen 06 HU-004, HU-006, HU-010, HU-011 / PO-Q5, PO-Q7 y PO-Q28 · bloquea las specs de F-03 y F-05) Cada regla da un solo texto: `RN-090`, el de "Regenerar día" deshabilitado; `RN-084`, el del plazo vencido al regenerar; `RN-101`, el indicador de progreso de la generación; `RN-108`, el de "sin opción válida" para la alternativa. No hay texto para la respuesta que sigue siendo inválida tras el reintento de `RN-085`, ni para una falla del proveedor distinta del plazo (HTTP 5xx, cuota del proveedor agotada) cuando no hay proveedor secundario, porque `RN-083` y `RN-084` solo cubren el plazo vencido. Si los 60 s de `RN-083` incluyen el reintento sigue en PREG-09, y qué dispara el failover, en PREG-31.
- **Incógnita:** ¿Qué texto exacto ve el viajero en cada caso: "Generar itinerario" y "Pedir alternativa" deshabilitados sin conexión, plazo vencido de la alternativa, progreso de la alternativa y de la regeneración, "sin opción válida" al regenerar un día, respuesta inválida tras el reintento y falla del proveedor distinta del plazo?
- **Opciones:**
  1. Un texto propio para cada operación y cada causa de falla.
  2. Un texto por operación para cualquier causa de falla (del tipo "No pudimos … Reintentar"), más un texto por operación para el progreso y para el estado deshabilitado.
- **Acción requerida:** El PO, con diseño, fija los textos como claves de traducción y se agregan a los ejemplos de `RN-083`, `RN-084`, `RN-085`, `RN-090`, `RN-101` y `RN-108`.
- **A quién preguntar:** Pavel (PO / Diseño)
- **Bloquea a:** specs de F-03 y F-05
- **Estado:** Abierta

---

### PREG-59: Confirmación o deshacer al eliminar una actividad
- **Contexto:** (Origen 06 HU-010 / PO-Q9 · bloquea la spec de F-05) `RN-038` permite eliminar cualquier actividad, pero no dice si antes se pide confirmación ni si la eliminación se puede deshacer. Eliminar no usa IA y funciona sin conexión.
- **Incógnita:** ¿Eliminar una actividad pide confirmación o permite deshacer? ¿Con qué texto?
- **Opciones:**
  1. Eliminación inmediata, sin confirmación ni deshacer.
  2. Confirmación previa a la eliminación.
  3. Eliminación inmediata con opción de deshacer durante un tiempo acotado.
- **Acción requerida:** El PO decide y fija el texto como clave de traducción; se actualiza `RN-038`.
- **A quién preguntar:** Pavel (PO / Diseño)
- **Bloquea a:** spec de F-05
- **Estado:** Abierta

---

### PREG-60: Alcance de F-10 en v1: registro, eliminación de cuenta, sincronización y orden frente a F-11
- **Contexto:** (Origen 06 HU-012 / PO-Q10, PO-Q11 y PO-Q36 · post-MVP, F-10) `RN-079` a `RN-082` y `RN-098` cubren la asociación de viajes y el token de sesión, pero no qué métodos de registro existen ni qué datos pide la cuenta; PRINCIPIO-006 solo fija Argon2id para las contraseñas y OAuth 2.0 / OpenID Connect para el login social. F-10 incluye sincronizar y recuperar los viajes en otro dispositivo (02 §1.2), y la eliminación de cuenta tiene un plazo constitucional (PRINCIPIO-005), pero 03 no tiene reglas para ninguna de las dos. Además, HU-012 supone varios viajes locales, mientras que el MVP guarda uno solo y conservar varios es F-11 (Should). La retención de cuentas inactivas sigue en PREG-43, y la transición de invitado a usuario registrado, en PREG-39.
- **Incógnita:** ¿Qué métodos de registro y qué datos de cuenta incluye F-10? ¿La eliminación de cuenta y la sincronización entre dispositivos entran en F-10 en v1? ¿F-10 se entrega junto con F-11 o después y, si va antes, la asociación es de un solo viaje?
  - (desde ARQ-Q-15) ¿Qué proveedores OIDC concretos, qué política de contraseña y qué duración de la sesión? ¿Se incluyen la verificación de correo y la recuperación de contraseña, que requieren un servicio de correo sin capa gratuita identificada (07 §7.2)?
  - (desde ARQ-Q-23) Si la sincronización entra en F-10: ¿qué reglas de conflicto y de edición tienen los viajes de la cuenta (endpoint PUT, sin reglas en 03)?
- **Opciones (métodos de registro):**
  1. Solo correo y contraseña.
  2. Solo login social (proveedores por definir).
  3. Correo y contraseña, y login social.
- **Acción requerida:** El PO decide al especificar F-10; se agregan reglas en 03 §2.8 (registro, eliminación de cuenta y sincronización) y, si cambia el orden de entrega de F-10 y F-11, se actualiza 02 §1.2.
- **A quién preguntar:** Pavel (PO / Arquitecto)
- **Bloquea a:** Lanzamiento público — post-MVP (spec de F-10, Should de v1)
- **Estado:** Abierta

---

### PREG-61: Casos límite de la asociación de viajes y de la sesión en F-10
- **Contexto:** (Origen 06 HU-012 / PO-Q30 · post-MVP, F-10) `RN-080` y `RN-081` establecen que, sin consentimiento explícito, un viaje no se asocia y queda en el dispositivo, pero no cubren cerrar el diálogo sin responder, la pérdida de conexión a mitad de una asociación, la respuesta a quien pide un viaje de otra cuenta ni la expiración de la sesión. PREG-21 trata la integridad de los estados reenviados al asociar, y PREG-39, la transición de invitado a usuario registrado.
- **Incógnita:** ¿Cerrar el diálogo de consentimiento equivale a rechazar? ¿Qué queda en la cuenta y cómo se reintenta si se pierde la conexión durante la asociación? ¿Qué código HTTP y qué texto recibe quien pide un viaje de otra cuenta? ¿Qué pasa cuando la sesión expira?
- **Opciones (cierre del diálogo):**
  1. Cerrar equivale a rechazar y no se vuelve a preguntar por ese viaje.
  2. Cerrar equivale a "todavía no": el viaje queda en el dispositivo y se vuelve a preguntar en el siguiente inicio de sesión.
- **Acción requerida:** El PO y arquitectura deciden al especificar F-10; se agregan reglas en 03 §2.8 y se ajustan `RN-080` y `RN-081`.
- **A quién preguntar:** Pavel (PO / Arquitecto)
- **Bloquea a:** post-MVP (spec de F-10)
- **Estado:** Abierta

---

### PREG-62: Itinerario guardado cuando falla la generación que lo reemplaza
- **Contexto:** (Origen 06 HU-001 / PO-Q23 · bloquea la spec de F-01) `RN-076` pide confirmación antes de reemplazar el itinerario guardado y `RN-092` lo protege si se pierde la conexión, pero ninguna regla dice qué pasa si, tras confirmar, la generación nueva falla por el servicio de IA (plazo vencido, HTTP 5xx o respuesta inválida tras el reintento). PREG-37 trata en qué momento se pide la confirmación.
- **Incógnita:** ¿Se conserva el itinerario anterior si la generación que lo reemplaza falla por el servicio de IA y no por la conexión?
- **Opciones:**
  1. El itinerario anterior se conserva hasta que llega un itinerario nuevo válido.
  2. El itinerario anterior se borra al confirmar el reemplazo, falle o no la generación.
- **Acción requerida:** El PO decide; se agrega una regla Unwanted en 03 §2.8 o se amplía `RN-092`.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** spec de F-01
- **Estado:** Abierta

---

### PREG-63: Almacenamiento local no disponible o lleno
- **Contexto:** (Origen 06 HU-001 / PO-Q24 · bloquea la spec de F-01) `RN-073` obliga a guardar el itinerario del invitado en el almacenamiento local, pero no cubre el caso en que ese almacenamiento no está disponible (navegación privada, bloqueo del navegador) o está lleno. SUP-04 supone que alcanza y su umbral está en PREG-56; el borrado automático del almacenamiento web está en PREG-16.
- **Incógnita:** ¿Qué hace el sistema y qué texto muestra si no puede guardar el itinerario? ¿Se muestra el itinerario igual, aunque no quede guardado?
- **Opciones:**
  1. Mostrar el itinerario con un aviso de que no quedó guardado en el dispositivo.
  2. Detectarlo antes de generar y avisar al viajero, sin impedir la generación.
  3. Detectarlo antes de generar y no generar mientras el almacenamiento no esté disponible.
- **Acción requerida:** El PO y arquitectura deciden; se agrega una regla Unwanted en 03 §2.8, con su texto como clave de traducción.
- **A quién preguntar:** Pavel (PO / Arquitecto)
- **Bloquea a:** spec de F-01
- **Estado:** Abierta

---

### PREG-64: Falla o demora de la fuente de lugares al resolver el destino
- **Contexto:** (Origen 06 HU-002 / PO-Q25 · bloquea la spec de F-02) `RN-007` cubre un destino ambiguo o sin resultados, pero no una fuente de lugares (DEP-02) caída o lenta. `RN-088` conserva el formulario si falla la generación, pero la resolución del destino ocurre antes de generar. Los límites de la capa gratuita de lugares están en PREG-50.
- **Incógnita:** Si la fuente de lugares no responde o tarda al resolver el destino, ¿qué texto se muestra, cuál es el plazo máximo y se conserva el formulario?
- **Opciones:**
  1. Un plazo propio para la resolución del destino, con un mensaje con "Reintentar" y el formulario conservado.
  2. La resolución del destino cuenta dentro del plazo de la generación (PAR-09) y usa su mismo mensaje de error.
- **Acción requerida:** El PO y arquitectura deciden; se agrega una regla Unwanted en 03 §2.1 y, si hay plazo propio, un parámetro en el Anexo A de 03.
- **A quién preguntar:** Pavel (PO / Arquitecto)
- **Bloquea a:** spec de F-02
- **Estado:** Abierta

---

### PREG-65: Franja sin actividades y día con 0 actividades
- **Contexto:** (Origen 06 HU-007, HU-010 / PO-Q27 · bloquea las specs de F-04 y F-05) `RN-017` exige un día por cada fecha y `RN-038` permite eliminar cualquier actividad, así que un día puede quedar sin actividades. `RN-019` ordena las franjas, pero no dice qué se muestra en una franja vacía. PREG-08 trata el caso en que la IA no encuentra ninguna opción válida, no el de un día que el viajero vació.
- **Incógnita:** ¿Qué se muestra en una franja sin actividades y en un día con 0 actividades? ¿Se permite un día vacío?
- **Opciones:**
  1. Se permite; la franja vacía no se muestra y el día vacío muestra un mensaje.
  2. Se permite; la franja y el día vacíos se muestran rotulados como libres.
  3. No se permite eliminar la última actividad de un día (requiere ajustar `RN-038`).
- **Acción requerida:** El PO, con diseño, decide; se actualizan `RN-017`, `RN-019` o `RN-038` según la opción.
- **A quién preguntar:** Pavel (PO / Diseño)
- **Bloquea a:** specs de F-04 y F-05
- **Estado:** Abierta

---

### PREG-66: Registro de proveedor y versión del modelo frente a los logs mínimos (propuesta de ADR)
- **Contexto:** (Origen 06 HU-001, HU-004, HU-006 / PO-Q29 · bloquea las specs de F-01 y F-03) PRINCIPIO-005, `RN-074` y `RN-097` limitan lo que registra el backend a `request_id`, duración y estado, sin filas de invitados; PRINCIPIO-009 (y el ejemplo de `RN-086`) exige registrar el proveedor y la versión del modelo de cada generación. Tal como están redactados, no queda dónde registrar esos datos. PREG-36 trata qué cubren los logs (backend o cliente), no este choque.
- **Incógnita:** ¿Dónde se registran el proveedor y la versión del modelo? ¿Se aceptan `provider` y `model_version` en el log?
- **Opciones:**
  1. Aceptar `provider` y `model_version` en cada línea de log, porque no son payload del viaje (aclaración de PRINCIPIO-005 por ADR).
  2. Registrarlos en un destino separado (métricas agregadas por proveedor y versión), sin asociarlos al `request_id`.
- **Acción requerida:** Arquitectura propone un ADR (PATCH si solo aclara la Constitución) y se actualizan `RN-074` y `RN-097`.
- **A quién preguntar:** Pavel (Arquitecto / PO)
- **Bloquea a:** specs de F-01 y F-03
- **Estado:** Abierta

---

### PREG-67: Momento en que se evalúa la "fecha actual"
- **Contexto:** (Origen 06 HU-002 / PO-Q34 · bloquea la spec de F-02) `RN-004` y `RN-006` comparan con la fecha actual en America/Lima, pero no dicen si se toma al enviar la solicitud o al recibirla el backend. Una solicitud con inicio 2026-11-07 enviada a las 23:59:59 en Lima puede llegar el 2026-11-08. La zona horaria de referencia sigue en PREG-23 y PREG-32.
- **Incógnita:** ¿La fecha actual se toma en el momento del envío o en el de la recepción en el backend?
- **Opciones:**
  1. Momento de recepción en el backend.
  2. Momento de envío informado por el cliente, con una tolerancia acotada.
- **Acción requerida:** El PO y arquitectura deciden; se actualizan `RN-004`, `RN-006` y 03 §0.4.
- **A quién preguntar:** Pavel (PO / Arquitecto)
- **Bloquea a:** spec de F-02
- **Estado:** Abierta

---

### PREG-68: Estado de confiabilidad de los datos de una actividad propia
- **Contexto:** (Origen 06 Anexo A, HU-013 / PO-Q37 · post-MVP, F-09) Una actividad propia (F-09) puede incluir un precio o un horario escrito por el viajero. PRINCIPIO-002 y las reglas de 03 §2.7 están pensados para los datos que entrega el LLM, y solo la capa de verificación asigna ✓ Verificada (`RN-063`).
- **Incógnita:** ¿Qué estado de confiabilidad lleva un dato factual que el viajero escribe en una actividad propia?
- **Opciones:**
  1. ! Por confirmar, con el texto obligatorio de PRINCIPIO-003.
  2. Sin estado de confiabilidad, rotulado como dato ingresado por el viajero (requiere ADR sobre PRINCIPIO-002 y PRINCIPIO-003).
  3. La actividad propia no admite campos factuales: solo nombre, franja y duración.
- **Acción requerida:** El PO decide al especificar F-09; se agrega una regla en 03 §2.5 y, si cambia la Constitución, un ADR.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** post-MVP (spec de F-09)
- **Estado:** Abierta

---

### PREG-69: Límite de 5 itinerarios locales alcanzado
- **Contexto:** (Origen 06 Anexo A, HU-016 / PO-Q39 · post-MVP, F-11) `RN-077` permite conservar hasta 5 itinerarios (PAR-08), pero no dice qué pasa cuando el invitado pide generar el sexto ni si sigue aplicando la confirmación de `RN-076`.
- **Incógnita:** Con F-11 activa y 5 itinerarios guardados, ¿qué pasa al pedir el sexto? ¿Sigue aplicando la confirmación de `RN-076`?
- **Opciones:**
  1. Bloquear la generación hasta que el invitado borre un itinerario.
  2. Reemplazar el más antiguo, con la confirmación de `RN-076`.
  3. Pedir al invitado que elija cuál reemplazar.
- **Acción requerida:** El PO decide al especificar F-11; se agrega una regla Unwanted en 03 §2.8 y se ajusta `RN-076`.
- **A quién preguntar:** Pavel (PO)
- **Bloquea a:** post-MVP (spec de F-11)
- **Estado:** Abierta

---

## Preguntas agregadas en el triaje de 07-architecture y 08-backlog (2026-11-07)

PREG-70 a PREG-79 recogen las preguntas del Architect (07 §9, ARQ-Q-01 a ARQ-Q-34), del Backlog Builder (08 §4, BB-Q1 a BB-Q10) y los hallazgos 🟡 QB-14, QB-21, QB-23, QB-24 y QB-25 (más la nota sobre T-024 de "Correcciones aplicadas") que no tienen respuesta en 02, 03, 04, 06 ni en las decisiones vigentes (D-01 a D-11), ni están cubiertas por una PREG anterior. Las preguntas del mismo tema se agrupan en una sola PREG. Ninguna respuesta se da por supuesta: las opciones son alternativas a evaluar, y solo se marca "Recomendación del Architect" cuando 07 la trae. La trazabilidad completa está en la tabla al final de esta sección.

### PREG-70: Hosting de la web Next.js y cookie de sesión en el mismo sitio
- **Contexto:** (Origen 07 §9 ARQ-Q-01 y ARQ-Q-26 · ADR-002, C-01, C-13, §7.2 · bloquea el piloto) D-09 fija Next.js para la web, FastAPI en Render Free y Postgres de Supabase Free, pero no dónde se aloja la web. El único dato disponible es Vercel Hobby: solo uso personal y no comercial, válido para el diploma y no para un lanzamiento comercial. DEP-06 pide un despliegue reproducible para poder migrar entre proveedores gratuitos. Si la web y la API quedan en dominios distintos, la cookie de sesión `atp_session` es de terceros y deja de funcionar con el bloqueo de cookies de terceros (HU-012, F-10).
- **Incógnita:** ¿Dónde se aloja la web con USD 0 durante el piloto y cuándo se revisa ese hosting antes del lanzamiento público? ¿Cómo se sirve la API para que la cookie de sesión sea del mismo sitio que la web?
- **Opciones:**
  1. Vercel Hobby para el piloto (uso no comercial), con la API servida bajo el mismo sitio mediante un *rewrite* `/api/*` → API; el hosting se revisa antes del lanzamiento público.
  2. Otro hosting gratuito para Next.js (no hay datos en los insumos: hay que relevarlo), con el mismo *rewrite* `/api/*`.
  3. Un dominio propio común a la web y a la API (tiene costo: choca con D-05 y RES-02).
  - Recomendación del Architect (solo para la cookie): servir la API bajo el mismo sitio con el *rewrite* `/api/*` (07 ADR-002).
- **Acción requerida:** Pavel (arquitectura) elige el hosting, registra sus límites con fecha en ADR-002 y §7.2 de 07 y fija el esquema de dominios; se actualizan C-01, C-13 y DEP-06.
- **A quién preguntar:** Pavel (Arquitecto)
- **Bloquea a:** Piloto — despliegue del piloto (C-01, ADR-002); la cookie de sesión afecta además a HU-012 (F-10). No cambia ninguna spec Must.
- **Estado:** Abierta

---

### PREG-71: Servicio de ping y condiciones verificadas de Render Free y Supabase Free
- **Contexto:** (Origen 07 §9 ARQ-Q-02, ARQ-Q-16, ARQ-Q-17 y ARQ-Q-18 · ADR-002, C-04, C-07, C-14, C-19, C-20, §7.2 · bloquea la implementación) Las mitigaciones de D-09 se apoyan en supuestos sin confirmar: (a) C-20 llama a `GET /v1/health` cada 10 min (6 llamadas/h × 744 h = 4.464 al mes), pero no hay servicio elegido, un cron con retrasos de más de 15 min deja apagar a Render, y no está confirmado que los términos de Render Free lo permitan; (b) no está confirmado que un `SELECT 1` desde el backend cuente como actividad para la pausa de 7 días de Supabase Free; (c) no se sabe si Render Free admite conexiones IPv6 hacia Supabase o hay que usar su pooler IPv4; (d) el tiempo máximo de respuesta HTTP de Render Free debe ser de al menos 60 s para PAR-09; (e) PRINCIPIO-006 pide cifrado en reposo y RT-07 (PRINCIPIO-005) exige borrar una cuenta, incluidos los respaldos, en ≤ 30 días, pero no está confirmado si Supabase Free cifra con AES-256 ni cómo maneja los respaldos. Es el mismo tipo de verificación que PREG-50 hace para mapas y lugares.
- **Incógnita:** ¿Qué servicio ejecuta el ping con una puntualidad menor de 15 min, y lo permiten los términos de Render Free? ¿Se confirman (b) a (e) en la documentación vigente de cada proveedor? Si alguna no se cumple, ¿qué se cambia?
- **Opciones (servicio de ping):**
  1. Un servicio de cron externo gratuito con puntualidad garantizada de menos de 15 min (por elegir).
  2. El planificador del servicio de CI (PREG-72), aceptando que puede retrasarse.
  3. Sin ping: aceptar el arranque en frío de ~1 min y revisar PAR-09 (PREG-55) o avisar al viajero de la espera.
- **Acción requerida:** Pavel (arquitectura) verifica (b) a (e) en la documentación vigente, registra los resultados con fecha en ADR-002 y §7.2 de 07 y, si alguno falla, propone el cambio (por ejemplo, el pooler IPv4 en `DATABASE_URL` o un ajuste de PAR-09); elige el servicio de C-20.
- **A quién preguntar:** Pavel (Arquitecto)
- **Bloquea a:** Módulo 2 (implementación) — `DATABASE_URL` y `GET /v1/health` (T-003), plazos de C-07 y servicio de C-20; (e) solo afecta a las cuentas (F-10).
- **Estado:** Abierta

---

### PREG-72: Servicio de CI, repositorio y presupuesto del golden set
- **Contexto:** (Origen 07 §9 ARQ-Q-31 · RT-01, RT-11, RT-16, §7.2 · bloquea la implementación) 07 §7.2 deja pendiente el servicio de CI y el repositorio ("no figura en los insumos"). T-001 configura los gates de CI (check `Spec: NNN`, job `golden` con filtro por rutas, cobertura de RT-16) y la protección de `main` de RT-11 depende de este servicio (auditoría Clase 2, QB-20). El golden set y los E2E en 3 viewports consumen minutos de CI y, si corren contra el proveedor real, cuota del LLM (PREG-13, SUP-03). Según ADR-006, el golden set debe pasar al 100 % en cada cambio de proveedor o de modelo.
- **Incógnita:** ¿Qué servicio de repositorio y de CI con minutos gratuitos se usa, quién configura la protección de `main` y cuántas ejecuciones del golden set contra el proveedor real se presupuestan?
- **Opciones (golden set):**
  1. Golden set con el proveedor *fake* en cada PR y contra el proveedor real solo en cada cambio de proveedor o de modelo (ADR-006) y en ejecuciones manuales.
  2. Golden set contra el proveedor real en todo PR que toque las 4 rutas del filtro de T-001, con un tope mensual de ejecuciones.
- **Acción requerida:** Pavel (arquitectura) elige el servicio y lo registra con sus límites y fecha en ADR-002 y §7.2 de 07, fija el presupuesto de ejecuciones contra el proveedor real y configura la protección de `main` (RT-11).
- **A quién preguntar:** Pavel (Arquitecto)
- **Bloquea a:** Módulo 2 (implementación) — T-001 (gates de CI) y RT-11.
- **Estado:** Abierta

---

### PREG-73: Tareas del backlog que esperan decisiones abiertas
- **Contexto:** (Origen 08 §4 BB-Q3, BB-Q5 y BB-Q8; auditoría Clase 2 QB-21, QB-23 y QB-24 · bloquea la implementación) El backlog tiene 26 tareas y deja fuera, a la espera de otras preguntas: (a) el despliegue (Render, Supabase y hosting de la web), C-20 y el `testssl.sh` de RT-08, que dependen de PREG-70, PREG-71 y PREG-72 (BB-Q3, QB-21; C-20 es "v1 (piloto)" y mitiga el arranque en frío frente a PAR-09); (b) los adaptadores reales de LLM y de lugares: T-005 y T-008 trabajan con *fakes* hasta PREG-13 y PREG-50, y ninguna tarea verifica la condición de no entrenamiento de RN-099 (BB-Q5, QB-23; sin esos adaptadores el piloto no pasa a producción, ADR-006); (c) los huecos de HU-012 (Should): el diálogo de consentimiento por viaje de RN-079, OIDC, la verificación de correo, la recuperación de contraseña, el borrado de cuenta en ≤ 30 días (RT-07) y el rechazo de TLS 1.1, que esperan a PREG-60, PREG-61 y la Clase 3 (BB-Q8, QB-24). La matriz HU → T de 08 §3.5 no muestra estos huecos.
- **Incógnita:** ¿Se agregan ya como tareas "bloqueadas por PREG-NN", con su HU para que el hueco se vea en la matriz, aunque el backlog supere las 26 tareas, o se agregan cuando se resuelva cada pregunta? ¿El criterio de la tarea del adaptador de LLM exige que el ADR de selección documente el no entrenamiento y la no retención (checklist de la DoD)?
- **Opciones:**
  1. Agregarlas ya como T-0NN bloqueadas, con su HU (HU-004 y HU-006 para C-20; HU-012 para las de cuentas) y la PREG que las bloquea (propuesta de QB-21).
  2. Agregarlas cuando se resuelva cada pregunta (propuesta de BB-Q3, BB-Q5 y BB-Q8); mientras tanto, solo una nota en 08 §3.5.
  3. Agregar ya las que necesita el piloto, (a) y (b), y esperar para las de HU-012, (c), que es post-MVP.
- **Acción requerida:** Pavel decide; el Backlog Builder agrega las tareas con IDs nuevos (sin reutilizar ninguno) y recalcula el DAG y la ruta crítica de 08 §3.
- **A quién preguntar:** Pavel (PO / Arquitecto)
- **Bloquea a:** Módulo 2 (implementación) — completitud del backlog para el despliegue del piloto (C-01, C-04, C-20, RT-08), los adaptadores reales (C-08, C-15) y HU-012.
- **Estado:** Abierta

---

### PREG-74: T-024 por encima del tope de 2 h
- **Contexto:** (Origen auditoría Clase 2, QB-22 y "Correcciones aplicadas", pendiente (1) · T-024, HU-012 · bloquea la implementación) La convención de 08 dice que ninguna tarea supera 2 h. T-024 (L: revisión `0002_accounts` con 5 tablas, RLS, 4 endpoints, Argon2id, sesión y CSRF) sigue por encima del tope después de los recortes de QB-22, y ninguna otra tarea de C-13 o C-19 puede absorber una parte sin dividirla. Las reglas de revisión del Spec Pack no permiten eliminar ni fusionar IDs.
- **Incógnita:** ¿Se divide T-024, se acepta como excepción documentada al tope de 2 h o se difiere parte de su alcance?
- **Opciones:**
  1. Dividirla: T-024 conserva la migración y RLS, y una tarea nueva (T-0NN) toma register, login, logout y `GET /v1/me` (propuesta de QB-22).
  2. Mantenerla como excepción al tope de 2 h, documentada en 08 §1 y §3.
  3. Diferir parte del alcance (por ejemplo, `GET /v1/me` o CSRF) a las tareas de HU-012 que salgan de PREG-73.
- **Acción requerida:** Pavel decide; el Backlog Builder ajusta 08 (índice, detalle, DAG, totales y ruta crítica).
- **A quién preguntar:** Pavel (PO / Arquitecto)
- **Bloquea a:** Módulo 2 (implementación) — ejecución de T-024 (Should, HU-012).
- **Estado:** Abierta

---

### PREG-75: Valores abiertos que el código deja configurables
- **Contexto:** (Origen auditoría Clase 2 QB-25 · T-012, T-013, T-014 · bloquea la implementación) Varias tareas dejan configurables, sin valor fijado, valores que dependen de PREG abiertas: `GENERATE_DEADLINE_S` (< 60 s) y `ADJUST_DEADLINE_S` (< 30 s) sin valor por defecto (BB-Q4 → PREG-09); si `no_valid_option` cuenta para el límite, como flag (BB-Q6 → PREG-30); los textos de PREG-57 y PREG-58; el caso de IndexedDB no disponible (PREG-63); y la violación de reglas que da 502 en la generación completa (ARQ-Q-11 → PREG-09). Los valores siguen en esas PREG; falta decidir cómo se comporta el código mientras no estén.
- **Incógnita:** Mientras falte un valor, ¿la API falla al arrancar o usa un valor por defecto?
- **Opciones:**
  1. La API no arranca si falta el valor, sin valor por defecto silencioso, y se agrega a los criterios de T-013 y T-014 "la API no arranca sin `GENERATE_DEADLINE_S`" (propuesta de QB-25).
  2. Un valor por defecto provisional, documentado en el ADR y marcado como pendiente de su PREG.
  3. La API no arranca sin el valor en el entorno del piloto, y los tests usan un valor por defecto.
- **Acción requerida:** Pavel (arquitectura) decide; el Backlog Builder ajusta los criterios de T-012, T-013 y T-014.
- **A quién preguntar:** Pavel (Arquitecto)
- **Bloquea a:** Módulo 2 (implementación) — T-012, T-013 y T-014.
- **Estado:** Abierta

---

### PREG-76: Aprobación de las dependencias antes de T-001
- **Contexto:** (Origen 08 §4 BB-Q1 · RT-11, PRINCIPIO-007 · bloquea la implementación) El agente de desarrollo no puede agregar dependencias (RT-11). 08 lista las que nombra 07 (fastapi, uvicorn, pydantic, httpx, structlog, sqlalchemy, asyncpg, alembic, argon2-cffi, pytest, pytest-cov, ruff, mypy, import-linter; next, react, i18next, react-i18next, eslint-plugin-i18next, eslint-plugin-jsx-a11y, typescript-eslint, idb, openapi-typescript, openapi-fetch, vitest, @vitest/coverage-v8, @playwright/test, @axe-core/playwright, gitleaks) y tres que 07 no nombra: pytest-asyncio (tests async), fake-indexeddb (T-016) y una librería de reloj falso para Python (por ejemplo, time-machine; T-006, T-012 y T-022).
- **Incógnita:** ¿Pavel aprueba ese conjunto antes de T-001, incluidas las tres que 07 no nombra?
- **Opciones:**
  1. Aprobar el conjunto completo antes de T-001.
  2. Aprobar las que nombra 07 y decidir las tres nuevas por separado (con alternativa si alguna se rechaza).
  3. Aprobar por tarea, al empezar cada una.
- **Acción requerida:** Pavel aprueba y lo registra en ADR-002 o en un ADR de dependencias; los lockfiles los fija una persona (RT-11).
- **A quién preguntar:** Pavel (Arquitecto)
- **Bloquea a:** Módulo 2 (implementación) — T-001 y, por las tres nuevas, T-006, T-012, T-016 y T-022.
- **Estado:** Abierta

---

### PREG-77: Reglas Must de presentación en las tareas de UI (T-027+)
- **Contexto:** (Origen 08 §4 BB-Q7; auditoría Clase 2 QB-14 · HU-005 a HU-008, RT-14, PRINCIPIO-010 · bloquea el backlog de la Clase 3) Varias reglas Must de presentación no tienen tarea hasta las tareas de UI que se numeran desde T-027 en la Clase 3: el aviso de RN-036; el ícono, el texto y el color de RN-062; el banner de RN-091; los mensajes con "Reintentar" de RN-083 y RN-084; el texto del 429 de RN-043; el diálogo de RN-093; y la orientación de HU-005. T-019 solo prueba "4 encabezados de día". El snapshot de `ReliabilityBadge` en los 3 viewports, la auditoría axe-core (0 *serious* y *critical*) y los objetivos táctiles de 44 × 44 px CSS también dependen de componentes de pantalla (BB-Q7).
- **Incógnita:** ¿Se deja ya en 08 §1 la lista de HU y RN que deben cubrir las T-027+, y se agrega a T-019 la aserción de RN-036 (menor de 3–5 años y ritmo Activo)? ¿El snapshot de `ReliabilityBadge`, la auditoría axe-core y los objetivos táctiles van como criterios de las T-027+?
- **Opciones:**
  1. Ahora: listar en la reserva T-027+ de 08 §1 las HU (HU-005 a HU-008) y las RN que deben cubrir, y agregar a T-019 la aserción de RN-036 (propuesta de QB-14); los criterios de BB-Q7 van en las T-027+.
  2. En la Clase 3: escribir las T-027+ con esas reglas y criterios junto con 09-ui-spec.md, sin cambiar 08 ahora.
- **Acción requerida:** Pavel decide; el Backlog Builder actualiza 08 §1 y T-019 (opción 1) o escribe las T-027+ en la Clase 3 (opción 2).
- **A quién preguntar:** Pavel (PO / Diseño)
- **Bloquea a:** Módulo 1 — extensión del backlog de la Clase 3 (T-027+, ligada a 09) y trazabilidad RN Must → T de 08.
- **Estado:** Abierta

---

### PREG-78: Evidencia del test de 0 filas para el piloto
- **Contexto:** (Origen 08 §4 BB-Q10 · T-023, RT-06, PRINCIPIO-005 · bloquea el piloto) T-023 comprueba que las operaciones de invitado no crean filas contando las tablas de un Postgres local en Docker con `alembic upgrade head` aplicado, no las de la base de Supabase Free (C-19) que usará el piloto.
- **Incógnita:** ¿El test contra el Postgres local basta como evidencia de PRINCIPIO-005 para el piloto?
- **Opciones:**
  1. Basta: las mismas migraciones Alembic y el mismo test en CI contra el Postgres local.
  2. Además, un conteo de filas contra la base de Supabase del piloto antes de abrirlo, con el resultado registrado.
  3. Además, un conteo periódico contra Supabase durante el piloto.
- **Acción requerida:** Pavel decide; si elige 2 o 3, la verificación se agrega al plan del piloto (y, si corresponde, como tarea por PREG-73).
- **A quién preguntar:** Pavel (Arquitecto / PO)
- **Bloquea a:** Piloto — evidencia de RT-06 antes de abrir el piloto.
- **Estado:** Abierta

---

### PREG-79: Presupuesto de rendimiento y equipos de referencia de la web móvil
- **Contexto:** (Origen 07 §9 ARQ-Q-29 y, en parte, ARQ-Q-19 · C-01, C-18, RT-21, ADR-004, ADR-007 · bloquea el piloto) La v1 es solo web responsive, y "mobile" significa el navegador del celular en Android de gama baja con conexión intermitente (D-10; 07, convenciones). 07 no fija un presupuesto de JS inicial ni de tiempo hasta la interacción, ni una red de referencia (3G o 4G), ni los equipos y navegadores de referencia del piloto, que son también donde se mide el desalojo del almacenamiento (Safari en iOS, Android de gama baja). El umbral de SUP-04 sigue en PREG-56, y las reglas de borrado automático, en PREG-16.
- **Incógnita:** ¿Qué presupuesto de rendimiento debe cumplir la web en el celular, con qué red de referencia, y en qué equipos y navegadores se prueba en el piloto?
- **Opciones:**
  1. Fijar el presupuesto antes de implementar (valores que propone arquitectura) y medirlo en CI.
  2. Medir en las pruebas internas previas al piloto y fijar el presupuesto con esas mediciones, como los umbrales de PREG-56.
  3. Sin presupuesto numérico en v1: solo pruebas manuales en los equipos de referencia.
- **Acción requerida:** Pavel (arquitectura) decide y registra el presupuesto y la lista de equipos de referencia en 07 (ADR-007 y RT-21) y en el plan del piloto.
- **A quién preguntar:** Pavel (Arquitecto)
- **Bloquea a:** Piloto — plan de pruebas del piloto y medición de SUP-04.
- **Estado:** Abierta

---

### Trazabilidad del triaje de 07 y 08

Decisión: **A** = ya resuelta en los archivos o por una decisión vigente · **B** = duplicada de una PREG existente (con "amplía" si se agregó una viñeta "(desde …)" a su Incógnita) · **C** = nueva. Resultado: **A 2 · B 28 · C 20** (50 orígenes).

| Origen | Decisión | Destino | Justificación |
|---|---|---|---|
| ARQ-Q-01 | C | PREG-70 | Ninguna PREG trata el hosting de la web; D-09 no lo fija. |
| ARQ-Q-02 | C | PREG-71 | El servicio del ping de C-20 no está en ninguna PREG. |
| ARQ-Q-03 | B (amplía) | PREG-13 | PREG-13 es el proveedor sin entrenamiento; se agregan la no retención y el país de procesamiento. |
| ARQ-Q-04 | B (amplía) | PREG-50 | PREG-50 es la fuente de lugares y sus límites; se agregan las cuatro capacidades que pide C-15. |
| ARQ-Q-05 | B (amplía) | PREG-06 | PREG-06 es el registro de fuentes y la fuente sugerida; se agrega su contenido inicial. |
| ARQ-Q-06 | B (amplía) | PREG-07 | Es la misma pregunta (dónde se calcula la vigencia); se agrega la propuesta ADR-005 a evaluar. |
| ARQ-Q-07 | B (amplía) | PREG-17 | PREG-17 define "dispositivo"; se agregan el `device_id` en IndexedDB y el contador en memoria. |
| ARQ-Q-08 | B (amplía) | PREG-30 | PREG-30 es qué cuenta para el límite; no cubría `no_valid_option`. |
| ARQ-Q-09 | B (amplía) | PREG-18 | La opción 1 de PREG-18 ya es la idempotencia; se agrega la propuesta concreta de 07 §5.2. |
| ARQ-Q-10 | B (amplía) | PREG-09 | PREG-09 trata si el reintento entra en los 60 s; se agrega el margen servidor/cliente. |
| ARQ-Q-11 | B | PREG-09 | PREG-09 cubre las salidas bien formadas que violan reglas y las truncadas. |
| ARQ-Q-12 | B | PREG-05 | Misma pregunta y mismas opciones (esquema, detector, golden set). |
| ARQ-Q-13 | B | PREG-57, PREG-58 | PREG-57 cubre el código HTTP y los textos de rechazo; PREG-58, los de las operaciones de IA. |
| ARQ-Q-14 | B | PREG-23, PREG-32, PREG-67 | Zona horaria de la fecha actual (23), de `{HH:mm}` (32) y momento de la toma (67). |
| ARQ-Q-15 | B (amplía) | PREG-60 | PREG-60 trata registro y eliminación de cuenta; se agregan OIDC, contraseña, correo y sesión. |
| ARQ-Q-16 | C | PREG-71 | Ninguna PREG trata el cifrado ni los respaldos de Supabase; se agrupa con las verificaciones de Render y Supabase. |
| ARQ-Q-17 | C | PREG-71 | Verificación de Supabase (actividad, IPv6/pooler) sin PREG; mismo tema que ARQ-Q-02. |
| ARQ-Q-18 | C | PREG-71 | Verificación del tiempo de respuesta de Render frente a PAR-09 sin PREG; mismo tema. |
| ARQ-Q-19 | B (amplía) | PREG-56 (y PREG-16, PREG-79) | El umbral de SUP-04 está en PREG-56 y el borrado automático en PREG-16; los equipos van a PREG-79. |
| ARQ-Q-20 | B | PREG-63 | Misma pregunta (almacenamiento no disponible o lleno). |
| ARQ-Q-21 | B | PREG-62 | Misma pregunta (itinerario anterior si falla la generación de reemplazo). |
| ARQ-Q-22 | B | PREG-61 | PREG-61 ya pregunta por el cierre del diálogo, el código HTTP y el reintento tras un corte. |
| ARQ-Q-23 | B (amplía) | PREG-60 | PREG-60 trata la sincronización y el orden frente a F-11; se agregan las reglas de conflicto. |
| ARQ-Q-24 | B | PREG-04 | Es el punto que PREG-04 deja abierto (país sin enlace curado o enlace roto). |
| ARQ-Q-25 | B | PREG-01 | Misma pregunta, incluida la vigencia propia de cada dato. |
| ARQ-Q-26 | C | PREG-70 | La cookie de mismo sitio depende del hosting (ARQ-Q-01); sin PREG. |
| ARQ-Q-27 | B (amplía) | PREG-21 | PREG-21 trata la firma HMAC; se agregan la rotación del `kid` y su caso (c) o (d). |
| ARQ-Q-28 | A | — | Resuelta por D-10 (decisiones-vigentes.md): v1 solo web responsive; 06 ya dice "web responsive". |
| ARQ-Q-29 | C | PREG-79 | Ninguna PREG fija un presupuesto de rendimiento ni equipos de referencia (PREG-56 trata supuestos, no rendimiento). |
| ARQ-Q-30 | A | — | La cabecera de 03 cita la Constitución v2.0.0; el fondo del Anexo B #4 de 03 lo resolvió PREG-11 (AL-29). |
| ARQ-Q-31 | C | PREG-72 | Ninguna PREG trata el CI ni el presupuesto del golden set. |
| ARQ-Q-32 | B (amplía) | PREG-44 | Es la decisión pendiente n.° 4 de 00 (PREG-44); se agrega la región de Render y Supabase. |
| ARQ-Q-33 | B | PREG-66 | Misma pregunta (`provider` y `model_version` frente a los logs mínimos). |
| ARQ-Q-34 | B | PREG-08 | PREG-08 cubre la alternativa, el día y la generación completa sin opción válida. |
| BB-Q1 | C | PREG-76 | Ninguna PREG trata la aprobación de dependencias (RT-11). |
| BB-Q2 | B (amplía) | PREG-06 | PREG-06 pregunta quién da de alta las fuentes; se agrega cuándo se carga el contenido real. |
| BB-Q3 | C | PREG-73 | Tareas de despliegue, C-20 y RT-08 sin PREG; se agrupa con las demás tareas diferidas. |
| BB-Q4 | B (amplía) | PREG-09 | Los valores de los plazos del servidor son el margen de ARQ-Q-10 (misma viñeta). |
| BB-Q5 | C | PREG-73 | PREG-13 y PREG-50 eligen proveedor; agregar las tareas de adaptador es otra decisión. |
| BB-Q6 | B | PREG-30 | Igual que ARQ-Q-08 (misma viñeta). |
| BB-Q7 | C | PREG-77 | Criterios de UI de las T-027+ sin PREG; se agrupa con QB-14. |
| BB-Q8 | C | PREG-73 | Las tareas de HU-012 diferidas; el contenido sigue en PREG-60 y PREG-61. |
| BB-Q9 | B | PREG-32 (y PREG-23) | PREG-32 trata la zona horaria en que se muestra `{HH:mm}`; 03 §0.4 la deja provisional. |
| BB-Q10 | C | PREG-78 | Ninguna PREG trata la evidencia del test de 0 filas. |
| QB-14 | C | PREG-77 | Mismo tema que BB-Q7 (reglas Must de presentación sin tarea). |
| QB-21 | C | PREG-73 | Mismo tema que BB-Q3. |
| QB-23 | C | PREG-73 | Mismo tema que BB-Q5 (tarea del adaptador real de LLM y RN-099). |
| QB-24 | C | PREG-73 | Mismo tema que BB-Q8. |
| QB-25 | C | PREG-75 | Los valores están en PREG-09, 30, 57, 58 y 63; el comportamiento del código sin ellos no tiene PREG. |
| T-024 > 2 h (auditoría, "Correcciones aplicadas", pendiente 1) | C | PREG-74 | Ninguna PREG trata el tamaño de T-024. |

---

## Anexo A. Métricas de la auditoría

| Métrica | Valor |
|---|---|
| Hallazgos totales | **59** (10 críticos, 22 altos, 15 medios, 12 bajos) |
| Corregidos por completo | **17** |
| Corregidos en parte (lo que no requería decisión) | **14** |
| Enviados completos a preguntas abiertas (PREG) | **28** |
| Preguntas abiertas resultantes | **40** (19 bloquean el MVP) |
| Reglas nuevas | 10 (RN-102 a RN-111) |
| Reglas reescritas | 33 |

### A.1 Por tipo de hallazgo

| Tipo | Encontrados | Resueltos | Parciales | Abiertos |
|---|---|---|---|---|
| Ambigüedades | **14** | **1** | **2** | **11** |
| Contradicciones | 8 | 1 | 2 | 5 |
| Reglas faltantes | 10 | 0 | 1 | 9 |
| Inconsistencias de glosario | 8 | 6 | 2 | 0 |
| Debilidades de formato | 10 | 8 | 1 | 1 |
| Riesgo de invención | 9 | 1 | 5 | 3 |
| **Total** | **59** | **17** | **13** | **29** |

**Ambigüedades: 14 encontradas; 1 resuelta (A-20), 2 resueltas en parte (A-14, M-01) y 11 abiertas.** M-08 se cuenta como abierta: solo se corrigió su patrón EARS y la ambigüedad de fondo (0 frente a vacío, por persona o por grupo) sigue en PREG-40. Por la misma razón, la tabla de tipos da 13 parciales y 29 abiertos, mientras que el triage da 14 y 28.

---

## Anexo B. Triage de los 59 hallazgos

Decisión: **Corregir** = corregido por completo · **Corregir + PREG** = corregido lo que no requería decisión y el resto enviado a preguntas · **PREG** = enviado completo a preguntas.

| Hallazgo | Sev. | Tipo | Decisión | Resultado |
|---|---|---|---|---|
| C-01 | CRÍTICA | Invención | Corregir + PREG | RN-102, RN-103, RN-104 · PREG-05 |
| C-02 | CRÍTICA | Invención | PREG | PREG-01 (notas en F-06 y glosario) |
| C-03 | CRÍTICA | Invención | Corregir | RN-105 |
| C-04 | CRÍTICA | Invención | Corregir + PREG | RN-106, glosario · PREG-06 |
| C-05 | CRÍTICA | Invención | PREG | PREG-02 |
| C-06 | CRÍTICA | Contradicción | PREG | PREG-03 (propuesta de ADR) |
| C-07 | CRÍTICA | Invención | PREG | PREG-04 (nota en RN-024) |
| C-08 | CRÍTICA | Invención | Corregir + PREG | RN-107 · PREG-07 |
| C-09 | CRÍTICA | Invención | Corregir + PREG | RN-108, ejemplo en RN-007 · PREG-08 |
| C-10 | CRÍTICA | Invención | Corregir + PREG | RN-109, RN-110 · PREG-09 |
| A-01 | ALTA | Contradicción | PREG | PREG-10 |
| A-02 | ALTA | Contradicción | Corregir + PREG | 02 §0 · PREG-20 |
| A-03 | ALTA | Ambigüedad | PREG | PREG-16 |
| A-04 | ALTA | Contradicción | PREG | PREG-11 (propuesta de ADR) |
| A-05 | ALTA | Contradicción | PREG | PREG-12 |
| A-06 | ALTA | Faltante | PREG | PREG-21 |
| A-07 | ALTA | Contradicción | Corregir + PREG | 02 RES-09 · PREG-13 |
| A-08 | ALTA | Formato | Corregir | RN-035, 038, 045, 077, 094 |
| A-09 | ALTA | Formato | Corregir | RN-055 a RN-059 |
| A-10 | ALTA | Faltante | PREG | PREG-22 |
| A-11 | ALTA | Faltante | PREG | PREG-23 |
| A-12 | ALTA | Faltante | Corregir + PREG | RN-111 · PREG-24 |
| A-13 | ALTA | Faltante | PREG | PREG-25 |
| A-14 | ALTA | Ambigüedad | Corregir + PREG | RN-036 · PREG-26 |
| A-15 | ALTA | Faltante | PREG | PREG-39 |
| A-16 | ALTA | Ambigüedad | PREG | PREG-17 |
| A-17 | ALTA | Faltante | PREG | PREG-18 |
| A-18 | ALTA | Ambigüedad | PREG | PREG-15 (nota en Anexo C) |
| A-19 | ALTA | Faltante | PREG | PREG-14 |
| A-20 | ALTA | Ambigüedad | Corregir | RN-096 |
| A-21 | ALTA | Formato | Corregir + PREG | 10 ejemplos de 03 y §0.4 · PREG-27 |
| A-22 | ALTA | Ambigüedad | PREG | PREG-19 |
| M-01 | MEDIA | Ambigüedad | Corregir + PREG | Nota en RN-031 · PREG-28 |
| M-02 | MEDIA | Glosario | Corregir + PREG | Carga diaria, Bloque de descanso, RN-042 · PREG-01 |
| M-03 | MEDIA | Ambigüedad | PREG | PREG-29 |
| M-04 | MEDIA | Ambigüedad | PREG | PREG-30 |
| M-05 | MEDIA | Ambigüedad | PREG | PREG-31 |
| M-06 | MEDIA | Ambigüedad | PREG | PREG-32 |
| M-07 | MEDIA | Faltante | PREG | PREG-33 |
| M-08 | MEDIA | Ambigüedad | Corregir + PREG | RN-046, RN-047 (patrón) · PREG-40 |
| M-09 | MEDIA | Glosario | Corregir | F-06 renombrada; sinónimos en "No usar" |
| M-10 | MEDIA | Glosario | Corregir + PREG | Viaje local, Itinerario guardado, Lista curada, Registro de fuentes · PREG-17 |
| M-11 | MEDIA | Formato | Corregir | RN-024 |
| M-12 | MEDIA | Contradicción | PREG | PREG-34 |
| M-13 | MEDIA | Faltante | PREG | PREG-35 |
| M-14 | MEDIA | Formato | PREG | PREG-36 |
| M-15 | MEDIA | Ambigüedad | PREG | PREG-37 |
| B-01 | BAJA | Contradicción | Corregir | Encabezado de 03 (Brief v1.1) |
| B-02 | BAJA | Glosario | Corregir | RN-080, RN-081; notas en Organizador y Acompañante |
| B-03 | BAJA | Glosario | Corregir | 02 F-01 |
| B-04 | BAJA | Glosario | Corregir | Glosario "Menor" |
| B-05 | BAJA | Glosario | Corregir | Glosario "Estado de confiabilidad" |
| B-06 | BAJA | Glosario | Corregir | Glosario "Orientación por rango de edad" |
| B-07 | BAJA | Formato | Corregir | Anexo C de 03 |
| B-08 | BAJA | Formato | Corregir | Notas en RN-071 y RN-022 |
| B-09 | BAJA | Ambigüedad | PREG | PREG-38 |
| B-10 | BAJA | Formato | Corregir | RN-082, RN-054 |
| B-11 | BAJA | Formato | Corregir | RN-008, RN-012, RN-016 |
| B-12 | BAJA | Formato | Corregir | Convención de atomicidad en 03 §0.2 |
