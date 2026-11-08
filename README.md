# 🗺️ AI Travel Planner — Spec Pack v2.0

> **Especificación técnica y funcional bajo la metodología Spec-Driven Development (SDD)**  
> **Proyecto:** AI Travel Planner  
> **Programa:** Diploma en AI-First Software Architecture — DMC Institute  
> **Autor / Product Owner:** Pavel Tocto Facundo  
> **Versión:** 2.0.0 · Noviembre 2026  

---

## 🎨 Prototipo Interactivo en Google Stitch

Puedes explorar el diseño de interacción, los flujos de usuario y los componentes visuales del proyecto en el prototipo interactivo desarrollado en **Google Stitch**:

👉 **[Abrir Prototipo Interactivo en Google Stitch](https://stitch.withgoogle.com/preview/17930466028784477531?node-id=c23586326797406981e42c1229fc69e9)**

### Pantallas y flujos incluidos en el prototipo:
* **`P-001` Solicitud de viaje:** Formulario guiado con resolución de destino, selección de fechas, presupuesto, ritmo e integración de viajeros (adultos, niños y accesorios como cochecitos).
* **`P-002` Generación en curso:** Estado interactivo de carga y retroalimentación en tiempo real durante la orquestación del LLM y validación de reglas.
* **`P-003` Itinerario:** Visualización día por día con insignias obligatorias de confiabilidad (`✓ Verificada`, `≈ Estimada`, `! Por confirmar`), avisos mandatorios informativos y personalización de actividades.
* **`P-004` / `P-005` / `P-006` Gestión de Cuenta y Asociación:** Flujos de ingreso/registro opcional y asociación granular de viajes locales preservando la privacidad del usuario.

---

## 📖 Descripción del Proyecto

**AI Travel Planner** es una aplicación inteligente (web responsive en v1 y mobile post-v1) diseñada para resolver el problema de la sobrecarga de información y la falta de realismo en la planificación de viajes. A diferencia de los asistentes de IA generalistas que alucinan horarios o inventan precios, AI Travel Planner genera itinerarios personalizados, estructurados temporalmente y respetuosos con las limitaciones reales del grupo de viaje.

### Pilares Fundamentales

1. **Anti-alucinación y Confiabilidad de Datos:** La IA no puede emitir datos factuales (precios, horarios, disponibilidad, restricciones) sin validación. Cada dato lleva obligatoriamente un estado:
   * `✓ Verificada`: Confirmado con fuente oficial o API contratada.
   * `≈ Estimada`: Valor aproximado con base de cálculo explícita.
   * `! Por confirmar`: No verificado; alerta para confirmación previa por el usuario.
2. **Modo Invitado Primero (Guest-First):** La aplicación no exige registro obligatorio. El itinerario se almacena localmente en el dispositivo (`IndexedDB`) garantizando cero fricción de entrada.
3. **Privacidad por Diseño:** Cumplimiento de la Ley N.° 29733 (Perú) y regulaciones latinoamericanas. No se solicitan datos sensibles ni fechas exactas de nacimiento de menores.
4. **Enfoque Familiar Nativo:** Considera desde el MVP restricciones para niños (ritmos de viaje, cochecitos, descansos obligatorios y actividades adaptadas a la edad).

---

## 📂 Estructura del Spec Pack

El repositorio contiene el juego completo de especificaciones estructuradas en orden de precedencia y trazabilidad:

| Archivo | Documento | Fase / Agente BMAD | Descripción |
|---|---|---|---|
| [`00-constitucion.md`](./00-constitucion.md) | **Constitución del Proyecto** | Principios Rectores | Principios no negociables del sistema (anti-alucinación, precedencia, privacidad y neutralidad tecnológica). |
| [`01-project-brief.md`](./01-project-brief.md) | **Project Brief** | Discovery / Analyst | Problema, oportunidades, supuestos [S], evidencia [E] y métricas del producto. |
| [`02-alcance.md`](./02-alcance.md) | **Alcance y MoSCoW** | PM / Planning | Alcance funcional v1/MVP (In-Scope, Out-of-Scope) y priorización MoSCoW. |
| [`03-reglas-de-negocio.md`](./03-reglas-de-negocio.md) | **Reglas de Negocio** | Business Analyst | Reglas de negocio formalizadas en sintaxis EARS (`RN-001` a `RN-100`) y catálogo de parámetros (`PAR-01` a `PAR-11`). |
| [`04-glosario.md`](./04-glosario.md) | **Glosario del Dominio** | Analyst / PM | Lenguaje ubicuo (Ubiquitous Language) del dominio de viajes, estados y entidades. |
| [`05-preguntas-abiertas.md`](./05-preguntas-abiertas.md) | **Preguntas Abiertas y PO Log** | PO / Product Owner | Registro histórico de dudas, trade-offs arquitectónicos y decisiones del PO. |
| [`06-user-stories.md`](./06-user-stories.md) | **Historias de Usuario** | PM / Requirements | `HU-001` a `HU-012` detalladas con criterios de aceptación en formato Given/When/Then (Gherkin). |
| [`07-architecture.md`](./07-architecture.md) | **Arquitectura Técnica** | Solutioning / Architect | Diagramas C4, ADRs técnicos, especificación OpenAPI, seguridad y stack tecnológico. |
| [`08-backlog.md`](./08-backlog.md) | **Backlog Técnico** | Engineering / Tech Lead | Desglose ordenado de tareas técnicas (`T-001` a `T-030`) por fases de ejecución. |
| [`09-ui-spec.md`](./09-ui-spec.md) | **Especificación de UI** | UX/UI Designer | Inventario de pantallas (`P-001`–`P-006`), flujos de usuario (`UF-01`–`UF-04`), componentes y accesibilidad (WCAG AA). |
| [`10-validation-log.md`](./10-validation-log.md) | **Log de Validación** | Validation & QA | Hallazgos consolidados de revisión con usuarios, auditoría de agentes BMAD y mitigaciones técnicas. |

---

## 🛠️ Stack Tecnológico (v1)

```
                            ┌──────────────────────────────────────────────┐
  Navegador móvil (360–430) │  C-01 Cliente Web (Next.js 15, React 19)     │
  Navegador desktop (≥1024) │  ├─ Tailwind CSS + Lucide Icons + Radix UI   │
                            │  ├─ IndexedDB (Almacenamiento del invitado)  │
                            │  └─ C-03 @travel-planner/core (TypeScript)   │
                            └───────────────┬──────────────────────────────┘
                                            │ HTTPS / JSON (TLS ≥ 1.2)
                                            ▼
                            ┌──────────────────────────────────────────────┐
                            │  C-04 API REST (FastAPI / Python 3.12)       │
                            │  ├─ Validación Pydantic y reglas de negocio  │
                            │  ├─ Orquestador LLM con Guardrails           │
                            │  ├─ Capa de Verificación y Fuentes           │
                            │  └─ Base de Datos: PostgreSQL (Supabase)     │
                            └──────────────────────────────────────────────┘
```

* **Frontend:** Next.js (App Router), TypeScript, Tailwind CSS, empaquetado como Progressive Web App (PWA) instalable.
* **Backend:** FastAPI (Python), validación con Pydantic y tipado estricto.
* **Inteligencia Artificial:** Adaptador modular de LLM con validación de esquemas y guardia de contenido.
* **Persistencia:**
  * Modo Invitado: `IndexedDB` en el navegador (stateless en backend, cero retención).
  * Modo Cuenta (opcional): PostgreSQL vía Supabase.

---

## 🧭 Metodología de Desarrollo

Este repositorio aplica el paradigma **Spec-Driven Development (SDD)** complementado con el framework **BMAD** (*Business, Marketing, Architecture, Development*), operando bajo el siguiente orden estricto de precedencia:

$$\text{Constitución} \succ \text{ADR Aprobado} \succ \text{Spec Aprobada} \succ \text{Plan / Tasks} \succ \text{Código}$$

Ningún código se escribe, genera ni despliega sin contar previamente con una especificación técnica aprobada que garantice la coherencia, la seguridad de datos y la calidad de la experiencia del viajero.
