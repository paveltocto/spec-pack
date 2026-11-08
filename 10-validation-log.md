# 10 — Validation Log · AI Travel Planner

> **Fase BMAD:** Prototipado y Validación · **Agente:** Consolidador de Validación y Spec Finalizer  
> **Insumos:** 00-constitucion.md (v2.0.0), 01-project-brief.md (v1.2), 02-alcance.md, 03-reglas-de-negocio.md, 04-glosario.md, 06-user-stories.md, 07-architecture.md, 08-backlog.md, 09-ui-spec.md  
> **Fecha:** 2026-11-08 · **Versión:** 1.0.0 (Final Módulo 1)  
> **Estado:** Aprobado · Cero hallazgos críticos pendientes

---

## 0. Resumen ejecutivo

Este documento consolida los hallazgos, observaciones y decisiones derivadas de las tres fuentes de validación del prototipo e interfaz de usuario de **AI Travel Planner** (Clase 3):
1. **Validación con usuario/compañero:** evaluación cualitativa con perspectiva de familia viajera con niños.
2. **Auditoría del Agente Prototype Reviewer:** contraste exhaustivo de las pantallas `P-001` a `P-006` frente a las reglas EARS, la Constitución y el Alcance.
3. **Observaciones técnicas y de diseño:** revisión de paridad responsive, accesibilidad WCAG AA y robustez ante fallas de red.

Todas las observaciones han sido categorizadas por severidad y cuentan con una decisión formal documentada (`✅ APLICADA`, `⏳ BACKLOG` o `⏳ PENDIENTE MÓDULO 2`).

---

## 1. Fuentes de validación

### 1.1 Fuente 1: Feedback de usuario y revisión de pares (Familia viajera)
- **Escenario evaluado:** Planificación de viaje familiar a Cusco (2 adultos, 1 niño de 4 años, ritmo Equilibrado).
- **Hallazgos clave:**
  - Necesidad de clarificar la diferencia práctica entre **≈ Estimada** y **! Por confirmar** en actividades con costo.
  - Temor a perder el itinerario al usar la app sin cuenta si se recarga la página o se cierra el navegador.
  - Rechazo intuitivo a cualquier botón de «Guardar todo» o importaciones forzadas al registrarse.

### 1.2 Fuente 2: Reporte del Agente Prototype Reviewer (BMAD)
- **Cobertura funcional evaluada:** 100 % de las HUs Must (`HU-001` a `HU-011`) y la HU Should (`HU-012`).
- **Hallazgos clave:**
  - En la vista móvil de `P-003`, los avisos mandatorios `UI-03` (sugerencia) y `UI-04` (migratorio) no deben ser descartables ni quedar ocultos en acordeones.
  - En `P-001`, cuando se elige ritmo Activo con niños de 0–5 años, el aviso `UI-10` debe ser informativo (State-driven) y nunca bloquear ni cambiar automáticamente el ritmo seleccionado por el usuario.
  - En `P-005` (asociación), la UI debe obligar a una decisión independiente por cada viaje local («Sí, guardar» vs «No, dejar solo en el dispositivo»), garantizando cumplimiento estricto de `RN-079` y `PRINCIPIO-005`.

### 1.3 Fuente 3: Observaciones técnicas y de accesibilidad (QA & Tech Lead)
- **Criterios técnicos evaluados:**
  - Accesibilidad a11y: objetivos táctiles mínimos de 48 × 48 px (`size.touch-min`), foco visible `:focus-visible` de 3 px y contraste no dependiente exclusivamente de color en las insignias de confiabilidad.
  - Tolerancia a desconexión: preservación de datos en cliente (`IndexedDB`) ante fallas de red durante la generación o ajuste de actividades.

---

## 2. Matriz consolidada de observaciones

| # | Observación | Fuente | Tipo | Severidad | Acción acordada | Afecta a |
|---|---|---|---|:---:|---|---|
| **OBS-01** | Confusión entre el significado de "≈ Estimada" y "! Por confirmar" en precios de museos y traslados. | Compañero / Reviewer | Comprensión | **Alta** | Incorporar `UI-20` (Leyenda de estados explicativa desplegable) y texto obligatorio de base de cálculo o fuente sugerida bajo la insignia en `UI-02`. | HU-008, RN-061, P-003, 04-glosario |
| **OBS-02** | Ansiedad por pérdida de datos en modo invitado al recargar la página. | Compañero | Fricción | **Alta** | Diseñar `UI-16` (`FirstSaveNotice`) con mensaje explícito: «Tu itinerario se guardó en este dispositivo...» y confirmar reapertura automática directa sin formulario. | HU-001, RN-075, P-003, T-028 |
| **OBS-03** | Riesgo de sobreescritura accidental del itinerario activo al crear una nueva solicitud desde P-003. | Prototype Reviewer | Gap | **Alta** | Implementar `UI-15` (`ConfirmDialog`) con alerta: «Esto reemplazará tu itinerario a {destino}. ¿Continuar?» con foco inicial en Cancelar. | HU-001, RN-076, P-001, T-028 |
| **OBS-04** | Los botones de acción de IA en tarjetas quedaban inactivos sin explicar la causa durante fallas de conexión. | Tech Lead / Reviewer | Fricción | **Media** | Especificar `UI-14` con `aria-describedby` que expone el motivo visible (ej. «Necesitas conexión para regenerar este día»). | HU-006, RN-090, P-003, T-027 |
| **OBS-05** | En móvil, el selector de menores podría inducir a pedir nombres o fechas de nacimiento completas. | Prototype Reviewer | Cumplimiento | **Alta** | Restringir `UI-07` estrictamente a botones de opción para 4 rangos de edad cerrados (0–2, 3–5, 6–11, 12–17) sin campos de texto libre. | HU-003, PRINCIPIO-004, P-001, T-028 |
| **OBS-06** | Falta de trazabilidad en el Backlog de las tareas de frontend correspondientes a la especificación de UI. | Tech Lead | Gap | **Alta** | Crear las tareas `T-027` a `T-032` en `08-backlog.md` cubriendo componentes base, pantallas P-001 a P-006 y auditorías a11y. | 08-backlog, C-01, T-027 a T-032 |
| **OBS-07** | Ausencia de términos de UI en el glosario de lenguaje ubicuo. | Prototype Reviewer | Orden | **Media** | Incorporar la Sección 8 en `04-glosario.md` con los 20 términos clave de interfaz (`ReliabilityBadge`, `DayNav`, `OfflineBanner`, etc.). | 04-glosario, 09-ui-spec |
| **OBS-08** | En pantallas táctiles de gama baja, algunos controles tenían áreas clicables menores a 44 pt. | QA / Tech Lead | a11y | **Media** | Elevar el estándar de tamaño táctil mínimo a 48 × 48 px (`size.touch-min`) con padding transparente para WCAG AAA táctil. | PRINCIPIO-010, UI-01 a UI-26, T-032 |
| **OBS-09** | En caso de desconexión a mitad de generación o regeneración, el usuario no sabía si reintentar consumía cuota. | Reviewer / Compañero | Comprensión | **Media** | Crear `UI-15` de operación interrumpida: permite reintento voluntario al volver conexión sin penalizar cuota ni duplicar llamadas. | HU-006, RN-093, P-001, P-003 |
| **OBS-10** | El aviso migratorio no debe depender de la nacionalidad del viajero para evitar solicitar datos sensibles. | Prototype Reviewer | Privacidad | **Alta** | Mantener aviso migratorio genérico en todo itinerario (`UI-04`), remitiendo a fuentes oficiales curadas sin pedir país de origen. | HU-007, RN-024, PRINCIPIO-008, P-003 |

---

## 3. Decisiones tomadas

### 3.1 Decisiones aplicadas en la especificación (`✅ APLICADA`)
1. **D-UI-01 (Confiabilidad visual no dependiente del color):**  
   Se fijaron en `09-ui-spec.md` (§4.2) y `04-glosario.md` (§8) las tres insignias oficiales:
   - `✓ Verificada` (verde `#0E7090`, ícono check, borde 1.5 px).
   - `≈ Estimada` (ámbar `#B45309`, ícono tilde/aproximado, borde 1.5 px).
   - `! Por confirmar` (azul/violeta neutro `#4338CA`, ícono exclamación, borde 1.5 px).
   Acompañadas siempre del texto obligatorio explicativo.
2. **D-UI-02 (Aislamiento de datos de menores):**  
   En `UI-07` de `P-001`, se prohibió taxativamente cualquier input de texto libre, nombre o fecha exacta. Solo se admiten selectores cerrados de rangos de edad.
3. **D-UI-03 (Confirmación estricta de reemplazo):**  
   Se integró `UF-06` y el diálogo `UI-15` que exige confirmación explícita antes de reemplazar un viaje guardado en el dispositivo del invitado.
4. **D-UI-04 (Consentimiento individual por viaje en asociación):**  
   En `P-005` y `UI-22`, cada viaje local se decide uno a uno. No existe opción de "vincular todo automáticamente".
5. **D-UI-05 (Glosario de UI integrado):**  
   Se agregó formalmente la Sección 8 a `04-glosario.md` con definiciones unívocas en inglés y español.

### 3.2 Decisiones integradas al backlog (`⏳ BACKLOG`)
1. **D-UI-06 (Scaffold y desarrollo de componentes UI):**  
   Programada en `T-027` (Componentes base UI y tokens accesibles).
2. **D-UI-07 (Implementación de pantallas P-001 a P-003):**  
   Programadas en `T-028`, `T-029` y `T-030`.
3. **D-UI-08 (Implementación de pantallas de cuenta P-004 a P-006):**  
   Programada en `T-031` (Should, bajo bandera de feature).
4. **D-UI-09 (Auditoría automatizada axe-core):**  
   Programada en `T-032` (Must, CI gate de accesibilidad).

### 3.3 Decisiones diferidas (`⏳ PENDIENTE MÓDULO 2`)
1. **D-UI-10 (Selección final de proveedor de correo para registro OIDC):**  
   Diferida al Módulo 2 bajo `PREG-60` (no bloquea el MVP ni las pantallas de invitado).
2. **D-UI-11 (Calibración de latencia de modelos LLM en producción):**  
   Diferida a las pruebas con proveedor real (`PREG-09` / `PREG-13`).

---

## 4. Auditoría integral del Spec Finalizer (Evaluación v1.0)

El Agente **Spec Finalizer** auditó los 11 documentos del Spec Pack frente a los tres criterios normativos del diploma:

### 4.1 Criterio 1: Operatividad (¿Un agente puede construir el producto solo leyendo el Spec Pack?)
* **Puntaje:** **5 / 5** (Sobresaliente)
* **Justificación:**  
  - Los contratos de API en `07-architecture.md` (§4) incluyen esquemas JSON completos, tipos, encabezados de idempotencia y códigos de error normalizados.
  - `08-backlog.md` desglosa el trabajo en 32 tareas atómicas (< 2 h), con criterios de completitud binarios verificables mediante comandos automatizados de test (`pytest`, `vitest`, `playwright`).
  - `09-ui-spec.md` proporciona wireframes detallados para móvil y escritorio, estados vacíos/error/carga, árbol de navegación y mapeo exacto de claves i18n (`es-419`).

### 4.2 Criterio 2: Utilidad (¿El Spec Pack resuelve un problema real?)
* **Puntaje:** **5 / 5** (Sobresaliente)
* **Justificación:**  
  - Resuelve la dispersión y falta de confiabilidad en la planificación de viajes en Latinoamérica.
  - Diferencia de manera estricta los datos verificados de los estimados y de aquellos que requieren confirmación, erradicando alucinaciones de precios o requisitos migratorios.
  - Ofrece valor inmediato en menos de 5 minutos como invitado sin obligar a registrarse, protegiendo los datos de familias y menores.

### 4.3 Criterio 3: Orden (¿Hay trazabilidad completa entre archivos?)
* **Puntaje:** **5 / 5** (Sobresaliente)
* **Justificación:**  
  - Trazabilidad bidireccional completa y estricta:  
    $$\text{Constitución (PRINCIPIO-NNN)} \longleftrightarrow \text{Alcance (F-NN)} \longleftrightarrow \text{Reglas (RN-NNN)} \longleftrightarrow \text{Historias (HU-NNN)} \longleftrightarrow \text{Arquitectura (C-NN / API)} \longleftrightarrow \text{Pantallas (P-NNN)} \longleftrightarrow \text{Backlog (T-NNN)}$$
  - Convenciones de IDs uniformes en todos los documentos. Cero dependencias rotas y DAG verificado topológicamente.

---

## 5. Registro de archivos actualizados en el Spec Pack

| Archivo | Cambios incorporados en la versión final v1.0 |
|---|---|
| `00-constitucion.md` | Refinado v2.0.0; limpieza de referencias internas de borrador. |
| `01-project-brief.md` | Refinado v1.2; alineación con alcance responsive y métricas medibles. |
| `02-alcance.md` | Actualizado con dependencias DEP-NN, restricciones RES-NN y MoSCoW consistente. |
| `03-reglas-de-negocio.md` | Expandido con más de 100 reglas EARS; semántica unificada de confiabilidad; limpieza de referencias de borrador. |
| `04-glosario.md` | Expandido con Sección 8 (20 términos de UI de `09-ui-spec.md`). |
| `05-preguntas-abiertas.md` | Depurado y consolidado; trazabilidad de preguntas resueltas vs diferidas al Módulo 2. |
| `06-user-stories.md` | Refinado con enriquecimiento de QA (positivos, negativos, borde) y cruce con pantallas `P-NNN`. |
| `07-architecture.md` | Actualizado con 20 componentes, contratos API, 8 ADRs y alcance v1 web responsive Next.js. |
| `08-backlog.md` | Actualizado con 32 tareas atómicas (incorporación de `T-027` a `T-032`), DAG acíclico y matriz `HU → T`. |
| `09-ui-spec.md` | Completo con 6 pantallas, 6 flujos de usuario, wireframes responsive, tokens y catálogo de textos. |
| `10-validation-log.md` | Creado con consolidación de feedback, matriz de observaciones y dictamen del Spec Finalizer. |

