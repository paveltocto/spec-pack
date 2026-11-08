# 07 — Arquitectura técnica · AI Travel Planner

> **Fase BMAD:** Solutioning · **Agente:** Architect
> **Insumos:** 00-constitucion.md (v2.0.0), 06-user-stories.md (HU-001 a HU-012; Anexo A fuera de diseño Must), 02-alcance.md, 01-project-brief.md (v1.2), 03-reglas-de-negocio.md (RN-NNN y Anexo A, PAR-01 a PAR-11), decisiones vigentes del PO (D-01 a D-10), **cambio del PO del 2026-11-07 a las 12:26 (v1 y piloto solo web responsive; Expo pasa a post-v1)**
> **Estado:** Borrador · **Fecha:** 2026-11-07
> **País de operación:** Perú (mercado inicial Perú y Latinoamérica; cualquier destino)
> **Revisión 2026-11-07: hallazgos auditoría Clase 2 vuelta 1:** QB-01 (contraste de actividades C-10 → C-15 → C-16 en §2.1 y §4.2), QB-03 (semántica única de rechazo/degradación de `verified` en §3.2, §4.3, §4.5, §4.8, §5.2, ADR-003 y ADR-005), QB-04 (lista blanca de C-14 explícita), QB-15 (`min_age_notice`), QB-16 (RN-035, RN-037, RN-038 y RN-072 en §2.1), QB-17 (dos fases de C-09 en §4.2 y §5.1), QB-18 (`error.params`), QB-28 (D-10, ARQ-Q-28 y ARQ-Q-30), QB-30 (401 `invalid_credentials`).
> **Revisión 2026-11-07: triaje de §9 a 05-preguntas-abiertas.md:** cada ARQ-Q tiene destino (→ PREG-NN o "Resuelta"); ARQ-Q-28 y ARQ-Q-30 resueltas; nuevas PREG-70 (ARQ-Q-01, 26), PREG-71 (ARQ-Q-02, 16, 17, 18), PREG-72 (ARQ-Q-31) y PREG-79 (ARQ-Q-29).

**Convenciones de este documento**

- **Precedencia:** Constitución > ADR aprobado > spec aprobada. Los ADR de este documento están en estado *Propuesto* salvo que se indique otra cosa; ninguno modifica la Constitución, salvo ADR-001, que registra la enmienda 2.0.0 ya aprobada.
- **Valores:** todo valor numérico de negocio sale de un PAR-NN (03, Anexo A; aún propuestas del BA, PREG-55) o de los datos de capa gratuita del checkpoint 2 (consultados el 2026-11-07; hay que reconfirmarlos antes del piloto). Los límites técnicos que propone el Architect y que no salen de esos insumos se marcan **[ARQ-propuesta]** y se calibran con las mediciones de SUP-03 y SUP-04 (PREG-56).
- **"Mobile" en v1** significa la **web en el navegador del celular** (viewport de 360 a 430 px, Android de gama baja, conexión intermitente). La app Expo es un componente **post-v1** (C-02, ADR-007).
- **Idioma:** el código, las rutas, los campos y las tablas van en inglés; el texto, en español (PRINCIPIO-011).
- **Ejemplos JSON:** todos son **ejemplos ilustrativos**. Ningún horario, precio, URL de fuente ni restricción que aparezca en ellos es un dato verificado (03 §0.4).
- Las preguntas sin resolver están en la sección final, **§9 Preguntas del Architect** (ARQ-Q-NN).

---

## 1. Overview del sistema

AI Travel Planner es una aplicación **web responsive** (Next.js, instalable como PWA) que genera itinerarios de viaje día por día con IA. Funciona como invitado sin cuenta y ofrece una cuenta opcional. Toda regla de negocio, toda validación y toda asignación de estados de confiabilidad se ejecutan en una **API FastAPI sin estado de viaje** (Render Free). La API procesa en memoria las solicitudes del invitado y no las persiste. Los itinerarios del invitado viven solo en el **IndexedDB del navegador**. Solo los usuarios con cuenta (HU-012, Should) tienen filas en **Postgres de Supabase Free**, que se usa solo como base de datos. El LLM se llama siempre a través de un adaptador propio. Su salida pasa por una **capa de verificación** que es la única que puede marcar un dato como `verified`. El paquete `core` en TypeScript, con tipos generados del OpenAPI, se comparte con la futura app Expo para que agregarla no exija cambiar el backend.

### 1.1 Diagrama de arquitectura (v1)

```
                           ┌──────────────────────────────────────────────┐
  Navegador móvil 360–430 px│  C-01 Cliente web (Next.js, PWA instalable)  │
  Navegador escritorio ≥1024│  ├─ UI responsive + i18n es-419 (C-03)       │
                           │  ├─ C-03 core: tipos OpenAPI, cliente HTTP,   │
                           │  │   constantes PAR, validación de UX          │
                           │  └─ C-18 Almacenamiento local del invitado    │
                           │      (IndexedDB: viaje activo, device_id)     │
                           └───────────────┬──────────────────────────────┘
                                           │ HTTPS (TLS ≥ 1.2), JSON gzip
                                           │ X-Device-Id · cookie HttpOnly (solo con cuenta)
                                           ▼
 C-20 Ping cada 10 min ──GET /v1/health──► ┌────────────────────────────────────────────┐
 (servicio externo, ARQ-Q-02)              │ C-04 API FastAPI (Render Free, 1 proceso)  │
                                           │                                            │
                                           │  C-05 Validación de solicitud ──┐           │
                                           │  C-12 Límite de operaciones ────┤           │
                                           │  C-07 Orquestación de generación│           │
                                           │    ├─ C-06 Contexto LLM (lista permitida)   │
                                           │    ├─ C-08 Adaptador de LLM ───────────────┼──► Proveedor de LLM
                                           │    ├─ C-09 Validación de salida y reglas   │    (PREG-13, sin entrenamiento
                                           │    ├─ C-10 Verificación y confiabilidad ◄──┼──   ni retención)
                                           │    │     ├─ C-16 Registro de fuentes       │
                                           │    │     └─ C-15 Adaptador de lugares ─────┼──► Fuente de lugares (PREG-50)
                                           │    └─ C-11 Guardia de contenido            │
                                           │  C-17 Lista curada de enlaces oficiales    │
                                           │  C-13 Cuentas y asociación ────────────────┼──► C-19 Postgres (Supabase Free)
                                           │  C-14 Observabilidad y salud (/v1/health) ─┼──► SELECT 1 (evita la pausa de 7 días)
                                           └────────────────────────────────────────────┘

  Post-v1: C-02 Cliente mobile (Expo) ── reutiliza C-03 core y los mismos endpoints /v1 ──► C-04
```

### 1.2 Principios de diseño

| # | Principio de diseño | Origen |
|---|---|---|
| DP-01 | **Backend como única fuente de verdad.** Los clientes validan solo para UX con constantes generadas desde el backend; la API repite toda validación. | PRINCIPIO-009 |
| DP-02 | **Invitado sin estado en el servidor.** Generar, pedir una alternativa, regenerar un día y recalcular estados son operaciones sin estado: el cliente envía lo mínimo necesario y el backend no escribe ninguna fila. | PRINCIPIO-005 |
| DP-03 | **Nada "verified" sin la capa de verificación.** El esquema que el LLM debe cumplir no admite `verified`; solo C-10 lo asigna, con una fuente del registro (C-16) y una atestación HMAC. | PRINCIPIO-002, PRINCIPIO-003 |
| DP-04 | **Lista permitida hacia el LLM.** El contexto del LLM se construye campo por campo desde una lista blanca; la entrada rechaza todo campo no declarado. | PRINCIPIO-004 |
| DP-05 | **Proveedor de LLM intercambiable.** Solo C-08 importa el SDK del proveedor; cada generación registra el proveedor y la versión del modelo. | PRINCIPIO-009 |
| DP-06 | **Credenciales fuera del almacenamiento local.** La sesión web va en una cookie `HttpOnly; Secure; SameSite`; IndexedDB guarda solo el itinerario y un `device_id` anónimo. | PRINCIPIO-006 |
| DP-07 | **Diseño para Android de gama baja y red intermitente.** Respuestas comprimidas, sin reintentos automáticos de operaciones de IA, escritura local atómica y límites de espera de PAR-09 en el cliente. | PRINCIPIO-012, RN-090 a RN-093 |
| DP-08 | **Textos solo por claves de i18n.** El backend devuelve `message_key` y parámetros, nunca texto de UI. | PRINCIPIO-011, RN-100 |
| DP-09 | **USD 0 por construcción.** Cada componente tiene una capa gratuita identificada y un riesgo documentado (§7.2). | D-05, RES-02 |

---

## 2. Componentes principales

Estado: **v1** = se construye para el piloto; **post-v1** = componente futuro, sin HUs en v1.

### 2.1 Tabla de componentes

| ID | Nombre | Estado | Responsabilidad | Tecnología | HUs | RN que ejecuta |
|---|---|---|---|---|---|---|
| C-01 | Cliente web | v1 | UI responsive del formulario, del itinerario y de los ajustes; estados de confiabilidad con ícono, texto y color; avisos; banner sin conexión; indicador de progreso a los 3 s; flujo de cuenta y asociación. Valida solo para UX. | Next.js (App Router) + React + TypeScript estricto, i18next (`react-i18next`), PWA (manifest + service worker mínimo, sin caché offline del itinerario: F-14 es Could) | HU-001 a HU-012 | Presentación de RN-019, RN-026, RN-036 (aviso), RN-062, RN-072 (ningún flujo invitado pide cuenta), RN-075, RN-076, RN-078, RN-090, RN-091, RN-093, RN-100, RN-101. Las reglas se aplican en C-04. |
| C-02 | Cliente mobile Expo | **post-v1** | App nativa futura con las mismas funcionalidades que C-01. Consume C-03 y los mismos endpoints `/v1` sin cambios en el backend. | Expo (React Native), almacén seguro del SO para tokens, SQLite local (se fija en un ADR al activarla) | Ninguna en v1 | — |
| C-03 | Paquete `core` | v1 | Tipos generados del OpenAPI, cliente HTTP con los plazos de PAR-09, constantes PAR exportadas desde el backend, validaciones de UX, recursos i18n es-419 compartidos, interfaz `GuestTripStore` (puerto de almacenamiento) y helpers de formato (`Intl`). Sin reglas de negocio. | TypeScript estricto, `openapi-typescript`, `openapi-fetch`, i18next resources, Vitest | HU-001 a HU-012 | Sin reglas de decisión de negocio: expone los PAR como constantes de UX. Ejecuta en el dispositivo las operaciones locales sin IA de RN-035 (conservar una actividad con orientación) y RN-038 (eliminar una actividad), que no pasan por el backend (`GuestOperationController`). |
| C-04 | API backend | v1 | Enrutamiento `/v1`, TLS/HSTS, CORS restringido, compresión gzip, `request_id`, límite de tamaño de cuerpo, mapeo de errores a `message_key`. Un solo proceso (requisito de C-12). | Python 3.12, FastAPI, Uvicorn (1 worker), Pydantic v2, httpx | HU-001 a HU-012 | Orquesta los módulos C-05 a C-14; RN-072 (ningún endpoint de invitado exige sesión) |
| C-05 | Validación de solicitud | v1 | Valida `TripRequest` con `extra="forbid"`: fechas, duración, anticipación, grupo, composición, rangos de edad, catálogo de necesidades y ritmo. Vuelve a resolver el destino desde `place_ref` (C-15). Toma la "fecha actual" en America/Lima (PREG-23, PREG-32, PREG-67). | Pydantic v2 (modelos estrictos), validadores de dominio | HU-002, HU-003 | RN-001 a RN-016, RN-095 |
| C-06 | Constructor de contexto del LLM | v1 | Construye el payload al proveedor **solo** con `destination` (nombre resuelto), `start_date`, `end_date`, `group_type`, `adults`, `children_age_ranges`, `group_needs`, `pace`, y para los ajustes el resumen de actividades existentes. Nunca IP, `user_id`, `device_id` ni texto escrito por el viajero. Cobertura 100 %. | Python puro (función pura `build_llm_context`) | HU-003, HU-004, HU-010, HU-011 | RN-096, RN-013, RN-015 (PRINCIPIO-004), RN-037 (pasa el `pace` de la solicitud sin cambiarlo) |
| C-07 | Orquestación de generación | v1 | Casos de uso `generate`, `alternative` y `regenerate_day`: plazo de servidor, 1 reintento ante respuesta inválida (PAR-10), conmutación opcional a un proveedor secundario y respuesta "sin opción válida". | Python asyncio, `asyncio.timeout` | HU-004, HU-005, HU-006, HU-010, HU-011 | RN-083, RN-084, RN-085, RN-086, RN-087, RN-088 (contrato), RN-108 |
| C-08 | Adaptador de LLM | v1 | Puerto `LlmProvider` (`complete_structured(prompt, schema, deadline) → raw_json, provider_id, model_version`). Es **el único módulo** que importa SDKs de proveedores. Exige salida JSON estructurada. | Python; SDK del proveedor elegido (PREG-13) detrás del puerto | HU-004, HU-006, HU-010, HU-011 | RN-086, RN-099 (configuración sin entrenamiento ni retención) |
| C-09 | Validación de salida y reglas de itinerario | v1 | En dos fases (§4.2): fase 1, antes de C-10, valida la salida del LLM contra `LlmItineraryOutput` (sin `verified` en el enum, `reliability_status` obligatorio en todo dato factual); fase 2, después de C-10, calcula la carga diaria y aplica las reglas de ritmo, edad, descanso, cierre, duplicados y aislamiento del día. | Pydantic v2 + funciones puras | HU-004, HU-005, HU-007, HU-010, HU-011 | RN-017, RN-018, RN-020, RN-021, RN-022, RN-027 a RN-031, RN-033, RN-034, RN-039, RN-040, RN-041, RN-042, RN-109, RN-110, RN-111 |
| C-10 | Capa de verificación y confiabilidad | v1 | Contrasta cada dato factual con las fuentes del registro (C-16, C-15). **Por cada actividad** (sub-paso de §4.2): `contrast_activity` consulta C-15 `resolve(activity.name, destino)` → `lookup(place_ref)`, mapea lo que devuelve la fuente (existencia, cierre permanente, horario, edad mínima) al `source_id` que C-16 registra para `PlacesSource` en cada categoría, y registra la fuente de cada evidencia; si C-15 no encuentra el lugar o falla, no hay evidencia y el dato queda `to_confirm` (RN-105). Luego asigna `verified` (con `source`, `checked_at`, `valid_until`, `attestation`), `estimated` (con `estimation_basis`) o `to_confirm` (con `suggested_source`); calcula la vigencia; recalcula estados sin persistir. Cobertura 100 %. | Python puro + HMAC-SHA256 (`hmac` de la stdlib) | HU-005, HU-008, HU-009, HU-001 (reapertura), HU-012 (asociación) | RN-032, RN-055 a RN-058, RN-060, RN-061, RN-063 a RN-068, RN-070, RN-071, RN-082, RN-103, RN-105, RN-106, RN-107 |
| C-11 | Guardia de contenido | v1 | Rechaza URLs de reserva, pago o afiliados; requisitos migratorios o sanitarios redactados; calificativos de "seguro" sin fuente; datos factuales en texto libre (detector + golden set, PREG-05). | Python: lista de patrones versionada + casos del golden set | HU-009 | RN-023, RN-025, RN-059, RN-102, RN-104 |
| C-12 | Límite de operaciones | v1 | Cuenta las operaciones de IA exitosas por dispositivo en una ventana de 24 h (PAR-07). Reserva un cupo antes de llamar al LLM, lo confirma si la operación tiene éxito y lo libera si falla. Devuelve 429 con `retry_at`. | Python; almacén en memoria del proceso, con clave HMAC(`device_id`) (ADR-008) | HU-006, HU-010, HU-011, HU-001 | RN-043, RN-089, RN-093 (no se reenvía sin confirmación: contrato del cliente) |
| C-13 | Cuentas y asociación | v1 (Should) | Registro e inicio de sesión con contraseña Argon2id; OAuth 2.0 / OIDC opcional (PREG-60); sesiones opacas; asociación de un viaje local por solicitud con consentimiento explícito; listado y lectura de viajes propios. | `argon2-cffi`, `authlib` (OIDC), SQLAlchemy 2 (async) + `asyncpg`, Alembic | HU-012 | RN-079, RN-080, RN-081, RN-082, RN-098 |
| C-14 | Observabilidad y salud | v1 | Logger con **lista blanca de C-14** (única fuente para HU-001 y T-003): `request_id`, `route`, `status`, `duration_ms`; `provider` y `model_version` solo si lo aprueba PREG-66 (detrás de un flag apagado hasta entonces). Cualquier otra clave se descarta antes de escribir la línea. `/v1/health` que ejecuta `SELECT 1`. | `structlog` con un procesador de lista blanca; FastAPI | HU-001 (logs), HU-006 (salud) | RN-097 |
| C-15 | Adaptador de fuente de lugares | v1 | Puerto `PlacesSource`: `resolve(text, near=None) → candidates` (RN-007; `near` = `place_ref` del destino cuando C-10 contrasta una actividad, §4.2 paso 4), `lookup(place_ref) → place` (existencia, cierre permanente, horario si la fuente lo da). El texto escrito por el viajero llega a la fuente de lugares, nunca al LLM. | Python + httpx; proveedor pendiente (PREG-50) | HU-002, HU-004, HU-005, HU-009 | RN-007, RN-021, RN-030, RN-031, RN-105, RN-111 (insumo de C-10: `resolve`/`lookup` del destino y de cada actividad; la fuente se identifica con su `source_id` en C-16) |
| C-16 | Registro de fuentes | v1 | Archivo versionado `source_registry.yaml` con `source_id`, nombre visible, dominios permitidos, categorías que puede verificar, vigencia declarada y `suggested_source_key` por categoría. Solo una persona lo modifica (CODEOWNERS). | YAML en el repo del backend, validado con Pydantic al arrancar | HU-008, HU-009 | RN-064, RN-106 (insumo), RN-067 (`{fuente_sugerida}`, PREG-06) |
| C-17 | Lista curada de enlaces oficiales | v1 | Archivo versionado `official_links.yaml` (país ISO 3166-1 alfa-2 → enlaces oficiales de migraciones, con `reviewed_at`). La CI falla si algún `reviewed_at` tiene más de 30 días (DEP-04). | YAML en el repo + test en CI | HU-007 | RN-024 |
| C-18 | Almacenamiento local del invitado | v1 | Guarda en IndexedDB el viaje activo (1 en el MVP; hasta 5 con F-11, PAR-08), el `device_id` anónimo, la bandera del aviso de primer guardado y la operación interrumpida pendiente. Escritura atómica. Sin tokens ni datos identificativos. | IndexedDB vía `idb` (envoltorio de promesas), detrás de `GuestTripStore` (C-03) | HU-001, HU-006, HU-010, HU-011, HU-012 | RN-035 y RN-038 (persiste la actividad conservada o eliminada), RN-073, RN-075 (bandera), RN-076, RN-078, RN-092, RN-098 |
| C-19 | Base de datos | v1 (Should) | Persistencia **solo** de usuarios con cuenta: usuarios, identidades OAuth, sesiones y viajes asociados. RLS activado sin políticas (deniega por defecto) para anular la Data API pública de Supabase. | Postgres de Supabase Free (solo base de datos; sin Supabase Auth); conexión por el pooler | HU-012 (y HU-006 de forma indirecta: el ping toca la base) | RN-074 (por ausencia: ninguna tabla de invitados), RN-080, RN-082 |
| C-20 | Ping de mantenimiento | v1 (piloto) | Llama a `GET /v1/health` cada 10 min durante el piloto para evitar el apagado de Render (15 min) y la pausa de Supabase (7 días). | Servicio de cron externo gratuito, pendiente (ARQ-Q-02) | HU-004, HU-006 (PAR-09 frente al arranque en frío) | Soporta RN-083 y RN-084 |

### 2.2 Estructura del monorepo

```
/                         pnpm workspaces (TS) + uv (Python)
├─ apps/web/              C-01 Next.js
├─ apps/mobile/           C-02 Expo (post-v1, vacío en v1)
├─ apps/api/              C-04..C-17 FastAPI
│  └─ src/atp_api/
│     ├─ api/routes/          itineraries.py, destinations.py, quota.py, auth.py, trips.py, health.py
│     ├─ domain/              params.py (PAR-01..PAR-11, única fuente), trip_request.py, itinerary.py, facts.py
│     ├─ validation/          C-05
│     ├─ llm_context/         C-06 (cobertura 100 %)
│     ├─ orchestration/       C-07
│     ├─ adapters/llm/        C-08 (único lugar con SDK de proveedor)
│     ├─ adapters/places/     C-15
│     ├─ itinerary_rules/     C-09
│     ├─ reliability/         C-10 (cobertura 100 %)
│     ├─ content_guard/       C-11
│     ├─ quota/               C-12
│     ├─ accounts/            C-13
│     ├─ observability/       C-14
│     └─ data/                source_registry.yaml (C-16), official_links.yaml (C-17)
├─ packages/core/         C-03 (src/api/schema.d.ts generado, src/params.json generado, src/i18n/es-419.json)
├─ specs/NNN-nombre/spec.md
└─ docs/adr/              copia de los ADR de este documento
```

---

## 3. Modelo de datos

Tipos: `uuid` (UUID v4), `date` (AAAA-MM-DD), `timestamptz` (ISO 8601 en UTC con `Z`), `decimal` (cadena con 2 decimales, p. ej. `"15.00"`), `enum` (valores cerrados en inglés).

### 3.1 Enumeraciones compartidas

| Enum | Valores | Origen |
|---|---|---|
| `GroupType` | `solo`, `couple`, `friends`, `family` | RN-008, 04-glosario |
| `AgeRange` | `0-2`, `3-5`, `6-11`, `12-17` | PRINCIPIO-004, RN-012 |
| `GroupNeed` | `stroller`, `reduced_mobility`, `dietary_restrictions`, `low_physical_effort` (catálogo definitivo: PREG-25) | PRINCIPIO-004, RN-014 |
| `Pace` | `relaxed` (≤ 360 min), `balanced` (≤ 480 min), `active` (≤ 600 min) | RN-016, PAR-05 |
| `TimeSlot` | `morning` (06:00–12:00), `afternoon` (12:00–18:00), `evening` (18:00–23:00); límites: PREG-22 | RN-018, PAR-04 |
| `ActivityKind` | `visit`, `meal`, `rest` (bloque de descanso, RN-033), `travel` | RN-033, RN-060 |
| `ReliabilityStatus` | `verified`, `estimated`, `to_confirm` | PRINCIPIO-003 |
| `LlmReliabilityStatus` | `estimated`, `to_confirm` (**sin `verified`**) | RN-063 |
| `FactCategory` | `price`, `opening_hours`, `availability`, `min_age`, `height_restriction`, `health_restriction`, `accessibility`, `weather`, `travel_time`, `duration`, `place_existence` (la inclusión de `duration` y `place_existence` está pendiente en PREG-01; v1 las trata como factuales por RN-020 y RN-105) | PRINCIPIO-002, RN-020, RN-105 |

### 3.2 (c) Esquemas transitorios de la API

Viven solo en memoria durante la solicitud. El backend no los persiste para invitados (PRINCIPIO-005).

**`FactualDatum<T>`**: todo dato factual de cualquier respuesta.

| Campo | Tipo | Obligatorio | Regla |
|---|---|---|---|
| `fact_id` | string | sí | Estable dentro del itinerario: `"{activity_id}.{category}"`. |
| `category` | `FactCategory` | sí | |
| `value` | T \| null | sí | `null` solo con `to_confirm` (el dato se muestra como "! Por confirmar", nunca desaparece: RN-071). |
| `reliability_status` | `ReliabilityStatus` | sí | Exactamente uno (RN-061). |
| `source` | `{source_id, name, url}` | si `verified` | `source_id` ∈ C-16; sin `source` registrada se rechaza (RN-064, RN-106) según la semántica por origen de abajo (QB-03). |
| `estimation_basis` | `{key, params?}` | si `estimated` | `key` de un catálogo cerrado de bases de cálculo (i18n), p. ej. `basis.typical_duration_similar_activities` → "{base_de_cálculo}" de PRINCIPIO-003. |
| `suggested_source` | `{key}` | si `to_confirm` | `key` de un catálogo cerrado (i18n), p. ej. `suggested_source.establishment` → "el establecimiento" (PREG-06). |
| `checked_at` | timestamptz | sí | Con `verified`: momento de la consulta a la fuente. Con `estimated` o `to_confirm`: momento en que C-10 asignó el estado. |
| `valid_until` | timestamptz \| null | si `verified` | `checked_at` + 7 días para `price`, `opening_hours`, `min_age`, `height_restriction`, `health_restriction` y `accessibility` (PRINCIPIO-003, RN-068); vigencia declarada por la fuente para `availability`, `weather` y `travel_time`; sin vigencia declarada no puede ser `verified` (RN-070). `place_existence`: PREG-01. |
| `attestation` | string | si `verified` | `v1.{kid}.{base64url(HMAC-SHA256(key, canonical(fact_id, category, value, source_id, checked_at, valid_until)))}`. Solo el backend puede emitirla (ADR-005). |

Regla de estado vigente (C-10, función `effective_status(datum, now)`, cobertura 100 %): `verified` sigue siendo `verified` mientras `now ≤ valid_until`, la atestación es válida y la fuente sigue en el registro. En cualquier otro caso pasa a `to_confirm` con el `suggested_source` de la categoría. Así se cumplen los bordes de HU-008: a los 7 días exactos (10:00) sigue `verified`; a las 10:01 pasa a `to_confirm`. Nunca sube de estado.

**Semántica única de rechazo y degradación de un `verified` (QB-03; RN-063, RN-064, RN-106; PRINCIPIO-002: "un dato con `verified` sin `source` registrada se rechaza").** Se decide por el origen del dato:

| Caso | Origen y condición | Resultado observable |
|---|---|---|
| (a) | Salida del LLM con algún `reliability_status: "verified"` | `LlmItineraryOutput` no valida (enum sin `verified`) → la salida es inválida y se aplica la política existente: 1 reintento (RN-085, PAR-10) y, si vuelve a fallar, **502 `generation_invalid_output`** con `retryable: true`, sin descontar cupo (RN-089). En los ajustes, igual con `alternative` y `regenerate-day`. El LLM nunca asigna `verified`. |
| (b) | Entrada del cliente con un dato mal formado (`verified` con `source` `null` o `""`, sin `attestation`, `valid_until` o `checked_at`; dos estados; `value: null` fuera de `to_confirm`) o con un campo no declarado | **422 `validation_error`**; `details[].code` = `reliability_datum_malformed` (dato) o `field_not_allowed` (campo); `details[].field` = ruta del dato (p. ej., `itinerary.days[0].activities[1].price`). No se procesa nada más ni se consume cupo. |
| (c) | Entrada del cliente con un `verified` cuya `attestation` no verifica (alterada, `kid` desconocido o no emitida por el backend), o con un `source_id` que no está en C-16 y que ninguna atestación válida respalda | **422 `validation_error`** con `details[].code` = `invalid_attestation`. Es un rechazo: no se degrada ni se guarda nada. |
| (d) | Entrada del cliente con un `verified` cuya `attestation` verifica, pero cuya fuente fue retirada de C-16 después de emitirse o cuya vigencia venció (`now > valid_until`) | **200**: el dato se devuelve (o se guarda, en §4.8) como `to_confirm` con `reason` = `source_removed` o `expired` y el `suggested_source` de la categoría. Es vigencia (RN-068, RN-070, RN-107), no rechazo. Nunca vuelve a `verified`. |

Una atestación válida prueba que el backend emitió el dato con una fuente registrada en ese momento; por eso (c) se rechaza y (d) se degrada. Implementación: `reliability.verify_incoming(datum, now)` lanza la excepción que el mapeo de errores convierte en 422 para (b) y (c), y devuelve el resultado de `effective_status` para (d). `effective_status` solo recibe datos ya verificados y sus motivos son `still_valid`, `expired` y `source_removed`. Qué pasa con un `kid` retirado tras la rotación de `ATTESTATION_KEY` sigue en ARQ-Q-27.

**`TripRequest`** (entrada; `extra="forbid"`)

| Campo | Tipo | Regla |
|---|---|---|
| `destination.place_ref` | string (1–200 caracteres) [ARQ-propuesta] | Identificador opaco devuelto por `/v1/destinations/resolve`. El backend lo vuelve a resolver (RN-007, RN-096). |
| `start_date` | date | Existe (RN-002); ≥ hoy en America/Lima (RN-004); ≤ hoy + 365 días (RN-006, PAR-02). |
| `end_date` | date | Existe; ≥ `start_date` (RN-003); duración ≤ 14 días (RN-005, PAR-01). |
| `group_type` | `GroupType` | Exactamente uno (RN-008); composición coherente (RN-011). |
| `adults` | int ≥ 1 | RN-009; `adults + len(children_age_ranges)` ≤ 10 (RN-010, PAR-03). |
| `children_age_ranges` | `AgeRange[]` (0–9 elementos) | Solo rangos del catálogo (RN-012). **Sin nombres, fechas, documentos, fotos ni texto** (PRINCIPIO-004). |
| `group_needs` | `GroupNeed[]` (sin duplicados, puede ir vacía) | Catálogo cerrado y asociada al grupo (RN-014, RN-015). |
| `pace` | `Pace` | Exactamente uno (RN-016). |

**`Itinerary`** (salida del backend; el invitado lo guarda en C-18 y la cuenta en C-19)

| Entidad | Atributos clave |
|---|---|
| `Itinerary` | `schema_version` (int, = 1), `destination` {`place_ref`, `display_name`, `country_code`}, `start_date`, `end_date`, `pace`, `days: Day[]` (exactamente `end_date − start_date + 1`, RN-017, RN-110) |
| `Day` | `day_number` (1..14), `date`, `daily_load_min` (int, calculado por C-09), `load_limit_min` (PAR-05 según el ritmo), `activities: Activity[]` (ordenadas por `slot` y `position`, RN-019; puede quedar vacía si el viajero borra todo, PREG-65) |
| `Activity` | `activity_id` (string), `kind` (`ActivityKind`), `slot` (`TimeSlot`), `position` (int), `name` (texto ≤ 120 caracteres [ARQ-propuesta]), `description` (texto ≤ 280 caracteres [ARQ-propuesta], sin datos factuales: RN-102), `place` {`place_ref` \| null, `existence`: `FactualDatum<bool>`}, `duration_min`: `FactualDatum<int>` (obligatorio, RN-020), `travel_from_previous_min`: `FactualDatum<int>` \| null (`estimated` mientras no haya fuente de rutas: RN-060), `opening_hours`: `FactualDatum<{open, close, closed_on_date}>` \| null, `price`: `FactualDatum<{min, max, currency, per}>` \| null, `min_age`: `FactualDatum<int>` \| null, `min_age_notice`: {`message_key`} \| null (C-09 lo emite con `activity.min_age.check_each_child` → "Comprueba que cada menor la cumple." cuando `min_age` es `verified` y mayor que el límite inferior y ≤ el límite superior del rango de algún menor, RN-031; el texto lo pone el cliente, DP-08), `accessibility`: `FactualDatum<bool>` \| null, `age_guidance`: {`message_text`, `applies_to`: `AgeRange[]`} \| null (orientación, nunca restricción: RN-034) |
| `Notices` | `suggestion` {`message_key`} (RN-026), `immigration` {`message_key`, `links[]` {`name`, `url`, `country_code`, `reviewed_at`}} (RN-024) |
| `GenerationMeta` | `generation_id` (uuid), `provider` (string), `model_version` (string), `generated_at`, `attempts` (1 o 2) (PRINCIPIO-009) |

**`LlmItineraryOutput`** (lo que el LLM debe devolver; nunca llega al cliente sin pasar por C-09, C-10 y C-11): igual que `Itinerary`, pero cada dato factual usa `LlmReliabilityStatus` y **no tiene** `source`, `checked_at`, `valid_until` ni `attestation`. Si falta `reliability_status` o aparece `verified`, la salida es inválida: se reintenta 1 vez (RN-085) y no se completa ningún estado por defecto (RN-109). Es el caso (a) de la semántica de rechazo de arriba.

**`ErrorBody`** (todas las respuestas 4xx y 5xx)

```json
{
  "error": {
    "code": "validation_error",
    "message_key": "errors.validation",
    "request_id": "req_01JC3W6Q2Z7T9M4K8B1N5H0XYZ",
    "retryable": false,
    "details": [
      { "field": "end_date", "code": "date_not_exists", "message_key": "validation.date.not_exists", "params": { "date": "2027-02-30" } }
    ],
    "params": null
  }
}
```

`details[].field` nombra el campo y nunca devuelve el valor de un campo no declarado. `error.params` (objeto \| null, opcional; QB-18) lleva los parámetros del `message_key` de nivel superior, p. ej. `{ "limit": 15, "retry_at": "2026-12-02T01:05:00Z" }` en el 429 (§4.6); es `null` o se omite cuando el mensaje no tiene parámetros.

### 3.3 (a) Entidades en el dispositivo del invitado (C-18, IndexedDB del navegador)

No se persisten en el backend (PRINCIPIO-005). En v1 solo existe el almacenamiento del navegador; el almacenamiento de la app Expo se define al activar C-02.

Base de datos IndexedDB `atp`, versión 1:

| Object store | Clave | Atributos | Notas de seguridad |
|---|---|---|---|
| `guest_trips` | `local_trip_id` (uuid, generado por el cliente) | `title` ("Cusco, 2026-12-18"), `trip_request: TripRequest`, `itinerary: Itinerary`, `generation_meta`, `created_at`, `updated_at`, `associated_trip_id` (uuid \| null, HU-012) | Contiene `children_age_ranges` sin identificadores, permitido por PRINCIPIO-006 (enmienda AL-29; validación legal antes del lanzamiento público). Máximo 1 registro en el MVP (RN-076, nota de HU-001) y 5 con F-11 (PAR-08). |
| `device` | `"singleton"` | `device_id` (uuid v4 anónimo, ADR-008), `first_save_notice_shown` (bool, RN-075) | Sin correo, nombre, teléfono, token ni credencial (PRINCIPIO-006, RN-098). Si el `device_id` cuenta como "dato identificativo" está pendiente en ARQ-Q-07. |
| `pending_operation` | `"singleton"` | `type` (`generate` \| `alternative` \| `regenerate_day`), `local_trip_id`, `day_number?`, `activity_id?`, `started_at` | Permite mostrar "Se interrumpió la regeneración del Día 3. ¿Reintentar?" (RN-093) sin reenviar nada automáticamente. |

Reglas de escritura:

1. Las escrituras de `guest_trips` se hacen en **una sola transacción `readwrite`**, y solo después de recibir una respuesta 200 completa y validada contra los tipos de `core`. Nunca se escribe un día a medias (RN-092, HU-011).
2. Al reemplazar un viaje (RN-076), el viaje anterior se borra en la misma transacción en que se escribe el nuevo, y solo tras una respuesta 200. Si la generación falla o se corta la conexión, el viaje anterior queda intacto (HU-001; falla de IA: PREG-62).
3. El cliente pide `navigator.storage.persist()` en el primer guardado para reducir el riesgo de que el navegador borre los datos (riesgo de WebKit: ARQ-Q-19).
4. Si IndexedDB no está disponible o la cuota se agota, el comportamiento queda pendiente (PREG-63; umbral SUP-04: PREG-56).
5. Una regla de lint prohíbe usar `localStorage`, `sessionStorage`, `document.cookie` e `indexedDB` fuera de `packages/core/src/storage/` y prohíbe las claves `token`, `session`, `password`, `email` y `jwt` (PRINCIPIO-006).

### 3.4 (b) Entidades en la base de datos (C-19, solo usuarios con cuenta)

No existe ninguna tabla de invitados ni de límite de operaciones. El test de PRINCIPIO-005 ("ninguna fila nueva tras generar como invitado") se cumple por construcción.

| Tabla | Columnas clave (tipo) | Relaciones | Notas de seguridad |
|---|---|---|---|
| `users` | `id` uuid PK; `email` citext UNIQUE NOT NULL; `password_hash` text NULL (formato `$argon2id$v=19$m=19456,t=2,p=1$…`); `email_verified_at` timestamptz NULL (PREG-60); `created_at` timestamptz; `deletion_requested_at` timestamptz NULL; `deleted_at` timestamptz NULL | 1–N `oauth_identities`, `sessions`, `trips` | Argon2id con 19 MiB, t = 2 y p = 1 (PRINCIPIO-006); `password_hash` es NULL si la cuenta es solo OAuth. Correo = dato personal (Ley 29733); nunca se escribe en los logs. |
| `oauth_identities` | `id` uuid PK; `user_id` uuid FK → users ON DELETE CASCADE; `provider` text; `subject` text; `created_at` | UNIQUE (`provider`, `subject`) | No se guardan tokens del proveedor OAuth: solo `sub`. |
| `sessions` | `id` uuid PK; `user_id` uuid FK ON DELETE CASCADE; `token_hash` bytea UNIQUE (SHA-256 de un token aleatorio de 32 bytes); `created_at`; `expires_at`; `revoked_at` NULL; `client` text (`web`; `mobile` post-v1) | N–1 `users` | El token en claro nunca se persiste; viaja solo en la cookie `HttpOnly; Secure; SameSite=Lax`. Duración de la sesión: ARQ-Q-15. |
| `trips` | `id` uuid PK; `user_id` uuid FK ON DELETE CASCADE; `origin_local_trip_id` uuid NOT NULL; `title` text; `start_date` date; `end_date` date; `trip_request` jsonb; `itinerary` jsonb; `generation_meta` jsonb; `schema_version` int; `associated_at` timestamptz; `updated_at` timestamptz | UNIQUE (`user_id`, `origin_local_trip_id`), que hace idempotente la asociación | `trip_request` contiene `children_age_ranges` sin identificadores. Conserva `checked_at` y `valid_until` originales (RN-082). Toda consulta filtra por `user_id` de la sesión. |
| `trip_association_consents` | `id` uuid PK; `user_id` uuid FK ON DELETE CASCADE; `origin_local_trip_id` uuid; `consent_text_key` text; `granted_at` timestamptz | 1–1 con `trips` (por `user_id`, `origin_local_trip_id`) | Evidencia del consentimiento explícito por viaje (PRINCIPIO-005, Ley 29733). Solo se registran los consentimientos otorgados: un rechazo no envía nada al servidor (RN-081). |

Controles transversales de C-19:

- **RLS activado en todas las tablas sin ninguna política** y revocación de privilegios a los roles `anon` y `authenticated`. La Data API de Supabase no expone nada; el backend se conecta con un rol propio de mínimo privilegio.
- Cifrado en reposo y respaldos: gestionados por el proveedor. Hay que confirmar AES-256 y el comportamiento de los respaldos en Free (ARQ-Q-16).
- Eliminación de cuenta en ≤ 30 días, incluidos los respaldos (PRINCIPIO-005): job de borrado definitivo sobre `deletion_requested_at`, con un test del plazo. El alcance funcional está en PREG-60.
- Migraciones con Alembic; ninguna migración crea tablas de invitados (revisión de PR por CODEOWNERS).

---

## 4. API Contracts

**Base:** `https://<host-api>/v1` (solo HTTPS; HTTP plano se rechaza con 301 → HTTPS y HSTS `max-age=31536000`). **Formato:** `application/json; charset=utf-8`, gzip si el cliente envía `Accept-Encoding: gzip`. **Cabeceras comunes:** `X-Device-Id: <uuid>` (obligatoria en las operaciones de IA y en `/quota`), `X-Request-Id` (respuesta). **Tamaño máximo de cuerpo:** 256 KiB [ARQ-propuesta, calibrar con SUP-04]; si se supera, 413.

**Códigos de estado transversales:**

| Código | Cuándo | `error.code` |
|---|---|---|
| 400 | JSON mal formado; consentimiento ausente o `false` en la asociación | `malformed_json`, `consent_required` |
| 401 | Sesión ausente, expirada o revocada (endpoints con cuenta); credenciales incorrectas en el login (mismo código si el correo no existe, §4.7) | `unauthenticated`, `invalid_credentials` |
| 404 | Recurso inexistente **o perteneciente a otra cuenta** (no se distingue, propuesta para PREG-61) | `not_found` |
| 409 | Correo ya registrado | `email_taken` |
| 413 | Cuerpo mayor que el límite | `payload_too_large` |
| 422 | Validación de negocio o de esquema fallida, incluido un campo no declarado (RN-095), un dato de confiabilidad mal formado o un `verified` con atestación inválida (casos (b) y (c) de §3.2, QB-03). Los textos y el código para los demás rechazos son propuesta para PREG-57. | `validation_error` (con `details[].code` = `field_not_allowed`, `reliability_datum_malformed`, `invalid_attestation`, etc.) |
| 429 | Límite de operaciones de IA alcanzado (RN-043) | `quota_exceeded` |
| 502 | El LLM devolvió una salida inválida también en el reintento (RN-085), o la fuente de lugares falló (PREG-64) | `generation_invalid_output`, `places_unavailable` |
| 503 | Proveedor de LLM no disponible y sin secundario, o con el secundario también caído (RN-086); base no disponible en `/health` | `ai_unavailable`, `degraded` |
| 504 | Se agotó el plazo de servidor de la operación (RN-083, RN-084) | `generation_timeout` |

Las respuestas 502, 503 y 504 llevan `retryable: true` y **no descuentan cupo** (RN-089). Los textos visibles (PREG-58) los pone el cliente a partir de `message_key`.

### 4.1 `GET /v1/destinations/resolve` — resolver destino (HU-002)

No es una operación de IA y no cuenta para el límite. El texto `q` llega a la fuente de lugares (C-15), nunca al LLM (RN-096), y no se registra en los logs (RN-097).

Request: `GET /v1/destinations/resolve?q=San%20Juan` (`q`: 2–100 caracteres [ARQ-propuesta]).

Response 200 (ejemplo, ambiguo):

```json
{
  "status": "ambiguous",
  "candidates": [
    { "place_ref": "plc_7f3a91", "display_name": "San Juan de Lurigancho (Lima, Perú)", "country_code": "PE" },
    { "place_ref": "plc_c02e44", "display_name": "San Juan (Puerto Rico)", "country_code": "PR" }
  ]
}
```

- `status` ∈ `resolved` (1 candidato), `ambiguous` (≥ 2: el cliente pide elegir, RN-007), `not_found` (0: clave `destination.not_found` → "No encontramos ese destino. Revisa cómo está escrito.").
- 422 si `q` está fuera de rango; 502 `places_unavailable` si la fuente falla o no responde (texto y plazo: PREG-64).

### 4.2 `POST /v1/itineraries/generate` — generar itinerario (HU-002 a HU-009, HU-006)

Operación de IA: cuenta 1 si tiene éxito (PAR-07). Plazo del cliente: **60 s** (PAR-09). Plazo del servidor: `GENERATE_DEADLINE_S` < 60 s; el margen y la inclusión del reintento están en PREG-09 (ARQ-Q-10). El backend no persiste nada (PRINCIPIO-005).

Pipeline (QB-17: C-09 en dos fases; QB-01: contraste por actividad):

1. C-05 valida la `TripRequest` → C-15 vuelve a resolver el `place_ref` del destino → C-12 reserva el cupo.
2. C-06 arma el contexto → C-08 llama al LLM.
3. **C-09 fase 1 (forma):** valida `raw_json` contra `LlmItineraryOutput` (sin `verified`: caso (a) de §3.2; sin estados por defecto, RN-109), exactamente `end_date − start_date + 1` días consecutivos (RN-017, RN-110) y cada actividad con `slot` y `duration_min` (RN-018, RN-020, RN-022).
4. **C-10 contraste y asignación:** por cada actividad con lugar, `contrast_activity` llama a C-15 `resolve(activity.name, destino)` y, si hay un candidato, `lookup(place_ref)`; mapea existencia, cierre permanente, horario y edad mínima al `source_id` que C-16 registra para la fuente de lugares en cada categoría y registra esa fuente como evidencia (`{category, value, source_id, checked_at}`). Firma: `contrast_activity(activity: LlmActivity, destination: ResolvedDestination, places: PlacesSource, registry: SourceRegistry, now: datetime) → ActivityEvidence`. Luego `assign_status(llm_datum, evidence: Evidence | None, registry, now) → FactualDatum` asigna `verified` solo si hay evidencia con un `source_id` registrado para esa categoría y vigencia; sin evidencia (lugar no encontrado o C-15 caída) el dato queda `estimated` o `to_confirm`, y `place.existence` queda `to_confirm` con `suggested_source.local_tourism_office` (RN-105). Una falla de C-15 en este paso no corta la generación (PRINCIPIO-002: toda consulta recibe respuesta).
5. **C-09 fase 2 (reglas sobre datos ya verificados):** carga diaria (RN-027 a RN-029), descanso (RN-033), exclusión por `min_age` `verified` y `min_age_notice` (RN-030, RN-031), cierre permanente o ese día según un horario `verified` (RN-021, RN-111), duplicados (RN-040) y `age_guidance` como orientación (RN-034). Con violaciones la salida es inválida (ADR-003 punto 6).
6. C-11 aplica la guardia de contenido.
7. Si la fase 1, la fase 2 o C-11 fallan: 1 reintento desde el paso 2 (PAR-10, RN-085); luego 502.
8. C-12 confirma el cupo → 200 (en 5xx lo libera, RN-089).

Request (ejemplo):

```json
{
  "destination": { "place_ref": "plc_8c2f10" },
  "start_date": "2026-12-18",
  "end_date": "2026-12-21",
  "group_type": "family",
  "adults": 2,
  "children_age_ranges": ["3-5"],
  "group_needs": ["stroller"],
  "pace": "balanced"
}
```

Response 200 (ejemplo; se muestra el Día 1 y se abrevian los Días 2 a 4 con `…`):

```json
{
  "itinerary": {
    "schema_version": 1,
    "destination": { "place_ref": "plc_8c2f10", "display_name": "Cusco, Perú", "country_code": "PE" },
    "start_date": "2026-12-18",
    "end_date": "2026-12-21",
    "pace": "balanced",
    "days": [
      {
        "day_number": 1,
        "date": "2026-12-18",
        "daily_load_min": 330,
        "load_limit_min": 480,
        "activities": [
          {
            "activity_id": "act_d1_01",
            "kind": "visit",
            "slot": "morning",
            "position": 1,
            "name": "Qorikancha",
            "description": "Templo inca sobre el que se levantó el convento de Santo Domingo.",
            "place": {
              "place_ref": "plc_31aa07",
              "existence": {
                "fact_id": "act_d1_01.place_existence", "category": "place_existence", "value": true,
                "reliability_status": "to_confirm",
                "suggested_source": { "key": "suggested_source.local_tourism_office" },
                "checked_at": "2026-11-07T17:40:12Z"
              }
            },
            "duration_min": {
              "fact_id": "act_d1_01.duration", "category": "duration", "value": 90,
              "reliability_status": "estimated",
              "estimation_basis": { "key": "basis.typical_duration_similar_activities" },
              "checked_at": "2026-11-07T17:40:12Z"
            },
            "travel_from_previous_min": null,
            "opening_hours": {
              "fact_id": "act_d1_01.opening_hours", "category": "opening_hours",
              "value": { "open": "08:30", "close": "17:30", "closed_on_date": false },
              "reliability_status": "verified",
              "source": { "source_id": "src_example_official_site", "name": "sitio oficial del recinto", "url": "https://example.org/horarios" },
              "checked_at": "2026-11-07T17:40:09Z",
              "valid_until": "2026-11-14T17:40:09Z",
              "attestation": "v1.k1.q3Jt9b0sR2m5yVwX8eZp1L4cN7aD6fG0hK2jM9uQ1sE"
            },
            "price": {
              "fact_id": "act_d1_01.price", "category": "price", "value": null,
              "reliability_status": "to_confirm",
              "suggested_source": { "key": "suggested_source.establishment" },
              "checked_at": "2026-11-07T17:40:12Z"
            },
            "min_age": null,
            "accessibility": null,
            "age_guidance": null
          },
          {
            "activity_id": "act_d1_02",
            "kind": "rest",
            "slot": "afternoon",
            "position": 1,
            "name": "Descanso en el alojamiento",
            "description": "Pausa para la siesta y la comida del menor.",
            "place": { "place_ref": null, "existence": null },
            "duration_min": {
              "fact_id": "act_d1_02.duration", "category": "duration", "value": 90,
              "reliability_status": "estimated",
              "estimation_basis": { "key": "basis.rest_block_for_young_children" },
              "checked_at": "2026-11-07T17:40:12Z"
            },
            "travel_from_previous_min": {
              "fact_id": "act_d1_02.travel_time", "category": "travel_time", "value": 20,
              "reliability_status": "estimated",
              "estimation_basis": { "key": "basis.distance_between_points" },
              "checked_at": "2026-11-07T17:40:12Z"
            },
            "opening_hours": null, "price": null, "min_age": null, "accessibility": null, "age_guidance": null
          }
        ]
      },
      "…"
    ]
  },
  "notices": {
    "suggestion": { "message_key": "itinerary.notice.suggestion" },
    "immigration": {
      "message_key": "itinerary.notice.immigration",
      "links": [
        { "name": "Superintendencia Nacional de Migraciones (Perú)", "url": "https://www.gob.pe/migraciones", "country_code": "PE", "reviewed_at": "2026-11-05" }
      ]
    }
  },
  "generation_meta": {
    "generation_id": "6f1c2b8e-3d0a-4c7e-9b51-2a8e4f0d9c11",
    "provider": "llm_primary",
    "model_version": "<model-version-del-proveedor>",
    "generated_at": "2026-11-07T17:40:12Z",
    "attempts": 1
  },
  "quota": { "limit": 15, "used": 1, "remaining": 14, "window_hours": 24 }
}
```

Nota del ejemplo: el niño (3–5) exige un bloque de descanso de ≥ 60 min por día (RN-033, PAR-06), que aquí es `act_d1_02`. Para ritmo `balanced`, `daily_load_min` debe ser ≤ 480 (PAR-05).

Errores (ejemplos):

- **422** (campo no declarado, HU-003): `{"error":{"code":"validation_error","message_key":"errors.validation","request_id":"req_01JC…","retryable":false,"details":[{"field":"child_name","code":"field_not_allowed","message_key":"validation.field_not_allowed"}]}}`. No se llama al proveedor.
- **422** (duración, HU-002): `details: [{"field":"end_date","code":"trip_too_long","message_key":"validation.trip.max_days","params":{"max_days":14}}]`.
- **422** (composición, HU-003): `details: [{"field":"group_type","code":"family_requires_child","message_key":"validation.group.family_requires_child"}]`.
- **429**: ver §4.6.
- **502**: `{"error":{"code":"generation_invalid_output","message_key":"errors.generation.failed","request_id":"req_01JC…","retryable":true,"details":[]}}`. Aplica cuando falló el único reintento (RN-085); no cuenta para el límite (RN-089).
- **503** `ai_unavailable` y **504** `generation_timeout`: misma forma, con `retryable: true`.

### 4.3 `POST /v1/itineraries/alternative` — alternativa de actividad (HU-010)

Operación de IA sin estado: el cliente envía el viaje vigente, el backend lo valida, verifica las atestaciones (ADR-005; atestación inválida → 422, vencida o con fuente retirada → `to_confirm`: casos (c) y (d) de §3.2) y lo usa para respetar RN-040 (sin duplicados) y RN-042. La alternativa pasa por el mismo pipeline de §4.2 (pasos 2 a 8). No persiste nada. Plazo del cliente: **30 s** (PAR-09). **Eliminar** una actividad no llama a la API: es local, funciona sin conexión y no consume cupo (HU-010).

Request (ejemplo):

```json
{
  "trip_request": { "destination": { "place_ref": "plc_8c2f10" }, "start_date": "2026-12-18", "end_date": "2026-12-21", "group_type": "family", "adults": 2, "children_age_ranges": ["0-2"], "group_needs": [], "pace": "relaxed" },
  "itinerary": { "schema_version": 1, "…": "itinerario vigente completo" },
  "target": { "day_number": 3, "activity_id": "act_d3_02" }
}
```

Response 200, reemplazo (ejemplo):

```json
{
  "result": "replaced",
  "day_number": 3,
  "replaced_activity_id": "act_d3_02",
  "activity": {
    "activity_id": "act_d3_02b",
    "kind": "visit",
    "slot": "afternoon",
    "position": 1,
    "name": "Museo de Arte Precolombino",
    "description": "Colección de piezas precolombinas en una casona colonial.",
    "place": { "place_ref": "plc_5d2e19", "existence": { "fact_id": "act_d3_02b.place_existence", "category": "place_existence", "value": true, "reliability_status": "to_confirm", "suggested_source": { "key": "suggested_source.local_tourism_office" }, "checked_at": "2026-12-05T15:02:31Z" } },
    "duration_min": { "fact_id": "act_d3_02b.duration", "category": "duration", "value": 75, "reliability_status": "estimated", "estimation_basis": { "key": "basis.typical_duration_similar_activities" }, "checked_at": "2026-12-05T15:02:31Z" },
    "travel_from_previous_min": null, "opening_hours": null, "price": null, "min_age": null, "accessibility": null, "age_guidance": null
  },
  "day_load_min": 345,
  "quota": { "limit": 15, "used": 6, "remaining": 9, "window_hours": 24 }
}
```

- La alternativa se ubica en el mismo `day_number` y en el mismo `slot` (RN-039); C-09 descarta la respuesta del LLM si cambia el día o la franja.
- **200 `no_valid_option`** (RN-108): `{"result":"no_valid_option","message_key":"adjust.alternative.no_valid_option","quota":{…}}` → "No encontramos otra actividad que encaje con tu grupo en esta franja."; el cliente no modifica nada. Si cuenta como operación exitosa está pendiente (ARQ-Q-08, PREG-30).
- 404 `not_found` si `activity_id` o `day_number` no existen en el itinerario enviado; 422, 429, 502, 503 y 504 como en §4.2 (504 al vencer `ADJUST_DEADLINE_S` < 30 s).

### 4.4 `POST /v1/itineraries/regenerate-day` — regenerar un día (HU-011)

Mismo cuerpo que §4.3 con `"target": { "day_number": 2 }`. La respuesta incluye **solo el día regenerado**. El cliente reemplaza ese día en una transacción y deja los demás intactos (RN-041 por construcción). Si el LLM devuelve otros días, C-09 los descarta. Plazo del cliente: 30 s.

Response 200 (ejemplo):

```json
{
  "result": "regenerated",
  "day": {
    "day_number": 2,
    "date": "2026-12-19",
    "daily_load_min": 345,
    "load_limit_min": 360,
    "activities": ["…actividades con FactualDatum completos, igual que en §4.2…"]
  },
  "quota": { "limit": 15, "used": 7, "remaining": 8, "window_hours": 24 }
}
```

- La fecha del día se conserva (HU-011, borde del Día 4) y el día cumple RN-021, RN-027 a RN-034 y RN-111 (RN-042).
- 200 `no_valid_option` (RN-108) con `message_key: "adjust.regenerate.no_valid_option"` (texto: PREG-58); el resto de los códigos, como en §4.3.

### 4.5 `POST /v1/itineraries/reliability/refresh` — recalcular estados de un itinerario guardado (HU-001, HU-008, RN-107)

No es una operación de IA ni cuenta para el límite, y **no persiste nada**. Por minimización, el cliente envía **solo** los datos que tienen `valid_until` (los `verified`), sin destino ni grupo. El backend verifica cada atestación y aplica `effective_status` con su reloj. El cliente lo llama al abrir un itinerario guardado con conexión (ADR-005, propuesta para PREG-07).

Request (ejemplo, reapertura el 09/12/2026):

```json
{
  "facts": [
    {
      "fact_id": "act_d1_01.opening_hours",
      "category": "opening_hours",
      "value": { "open": "08:30", "close": "17:30", "closed_on_date": false },
      "reliability_status": "verified",
      "source_id": "src_example_official_site",
      "checked_at": "2026-12-01T15:00:00Z",
      "valid_until": "2026-12-08T15:00:00Z",
      "attestation": "v1.k1.Zx8…"
    }
  ],
  "country_codes": ["PE"]
}
```

Response 200 (ejemplo):

```json
{
  "evaluated_at": "2026-12-09T14:00:00Z",
  "facts": [
    {
      "fact_id": "act_d1_01.opening_hours",
      "reliability_status": "to_confirm",
      "reason": "expired",
      "suggested_source": { "key": "suggested_source.establishment" },
      "checked_at": "2026-12-01T15:00:00Z"
    }
  ],
  "immigration_links": [
    { "name": "Superintendencia Nacional de Migraciones (Perú)", "url": "https://www.gob.pe/migraciones", "country_code": "PE", "reviewed_at": "2026-12-01" }
  ]
}
```

- `reason` ∈ `still_valid` (sigue `verified`), `expired` (se superó `valid_until`), `source_removed` (la fuente ya no está en C-16). Ningún dato sube de estado. Una atestación inválida (no la emitió el backend o fue alterada) ya no es un `reason`: es el caso (c) de §3.2 → 422 con `details[].code == "invalid_attestation"` (QB-03).
- 413 si hay más de 500 hechos [ARQ-propuesta]; 422 si un hecho no cumple el esquema (caso (b)) o su atestación es inválida (caso (c)). Si la llamada falla por red, ver ADR-005 (respaldo con `valid_until`).

### 4.6 `GET /v1/quota` y respuesta 429 — límite de operaciones (HU-006)

Request: `GET /v1/quota` con `X-Device-Id`.

Response 200 (ejemplo: 10 operaciones exitosas y 2 fallidas → quedan 5, RN-089):

```json
{ "limit": 15, "used": 10, "remaining": 5, "window_hours": 24, "retry_at": null }
```

**429 en cualquier operación de IA** (ejemplo de RN-043: la primera operación de la ventana fue el 2026-11-30 a las 20:05 en Lima, es decir el 2026-12-01T01:05Z, y la operación 16 llega el 2026-12-01 a las 18:40 en Lima):

```
HTTP/1.1 429 Too Many Requests
Retry-After: 5100
Content-Type: application/json
```

```json
{
  "error": {
    "code": "quota_exceeded",
    "message_key": "quota.exceeded",
    "request_id": "req_01JD…",
    "retryable": false,
    "details": [],
    "params": { "limit": 15, "retry_at": "2026-12-02T01:05:00Z" }
  }
}
```

- `retry_at` = momento de la operación exitosa más antigua de la ventana + 24 h. `Retry-After` va en segundos (5.100 s = 1 h 25 min en el ejemplo). El cliente muestra "Alcanzaste el límite de 15 operaciones. Podrás volver a intentarlo desde el 01/12/2026 a las 20:05." convirtiendo `retry_at` a America/Lima (PREG-23, PREG-32). Ventana móvil o fija: PREG-30.
- 422 `validation_error` si falta `X-Device-Id` o no es un UUID v4.
- El 429 se decide **antes** de llamar al proveedor, así que no consume tokens. La operación 15 se acepta y la 16 se rechaza (borde de HU-006).

### 4.7 Autenticación (HU-012, Should)

Métodos concretos de registro, verificación de correo y recuperación de contraseña: PREG-60 (ARQ-Q-15). En v1 solo hay cliente web: la sesión viaja **únicamente** en una cookie (el modo `Authorization: Bearer` para la app Expo es post-v1). La protección CSRF combina `SameSite=Lax`, la verificación de la cabecera `Origin` contra la lista permitida y la cabecera obligatoria `X-Requested-With: atp-web` en los métodos no idempotentes.

**`POST /v1/auth/register`**: request (ejemplo) `{"email":"viajera@example.com","password":"<contraseña>"}`.

- 201: `{"user":{"id":"0b9f…","email":"viajera@example.com","created_at":"2026-12-05T14:58:00Z"},"session":{"expires_at":"<según ARQ-Q-15>"}}` + `Set-Cookie: atp_session=<token>; HttpOnly; Secure; SameSite=Lax; Path=/`.
- 409 `email_taken`; 422 `validation_error` (correo inválido; política de contraseña: PREG-60).
- El hash Argon2id usa m = 19456 KiB, t = 2 y p = 1, con un test unitario de los parámetros (PRINCIPIO-006).

**`POST /v1/auth/login`**: `{"email","password"}` → 200 (mismo cuerpo que 201) + `Set-Cookie`; 401 `invalid_credentials` (mismo mensaje si el correo no existe).

**`POST /v1/auth/logout`** → 204: revoca la sesión y envía `Set-Cookie` con `Max-Age=0`.

**`GET /v1/auth/oauth/{provider}/start`** → 302 al proveedor OIDC (PKCE + `state` + `nonce`). **`GET /v1/auth/oauth/{provider}/callback`** → 302 a la web con la cookie de sesión; 400 `oauth_failed`. Proveedores: PREG-60.

**`GET /v1/me`** → 200 `{"user":{…}}`; 401.

### 4.8 `POST /v1/trips/associations` — asociar un viaje de invitado con consentimiento (HU-012)

**Un viaje por solicitud.** La API no ofrece "asociar todos": el consentimiento queda atado a un viaje concreto (RN-079, RN-080). Solo se llama después de que el usuario pulsa "Sí" en "¿Guardar 'Cusco, 2026-12-18' en tu cuenta?". Un rechazo o un cierre del diálogo no envía nada (RN-081; PREG-61).

Request (ejemplo; requiere la cookie de sesión):

```json
{
  "local_trip_id": "9b1d5c3a-77e2-4f0b-a6c4-1e2d3f4a5b6c",
  "consent": { "granted": true, "consent_text_key": "account.associate.prompt", "trip_label": "Cusco, 2026-12-18" },
  "trip": {
    "trip_request": { "destination": { "place_ref": "plc_8c2f10" }, "start_date": "2026-12-18", "end_date": "2026-12-21", "group_type": "couple", "adults": 2, "children_age_ranges": [], "group_needs": [], "pace": "balanced" },
    "itinerary": { "schema_version": 1, "…": "itinerario completo" },
    "generation_meta": { "generation_id": "6f1c…", "provider": "llm_primary", "model_version": "<…>", "generated_at": "2026-11-07T17:40:12Z", "attempts": 1 }
  }
}
```

Response 201 (ejemplo):

```json
{
  "trip_id": "c7e2a1f0-5b3d-4e8a-9f60-0d1c2b3a4e5f",
  "local_trip_id": "9b1d5c3a-77e2-4f0b-a6c4-1e2d3f4a5b6c",
  "associated_at": "2026-12-05T15:00:04Z",
  "reliability_downgrades": 0
}
```

- **200** con el mismo cuerpo si ya existía (`user_id`, `origin_local_trip_id`). Esto hace idempotente el reintento tras un corte de red.
- El backend valida `trip` con los mismos esquemas que §4.2 (`extra="forbid"`). Los datos `verified` con atestación válida conservan `checked_at` y `valid_until` originales (RN-082); los que tienen atestación válida pero vencieron o cuya fuente fue retirada se guardan como `to_confirm` (`expired` / `source_removed`) y se cuentan en `reliability_downgrades` (caso (d) de §3.2); una atestación inválida rechaza la asociación con 422 y no se guarda nada (caso (c), QB-03).
- 400 `consent_required` si falta `consent` o `granted ≠ true` (no se guarda nada); 401; 413; 422.

### 4.9 `GET /v1/trips` y `GET /v1/trips/{trip_id}` — viajes de la cuenta (HU-012)

- `GET /v1/trips` → 200 `{"items":[{"trip_id":"c7e2…","title":"Cusco, 2026-12-18","start_date":"2026-12-18","end_date":"2026-12-21","updated_at":"2026-12-05T15:00:04Z"}]}`; 401.
- `GET /v1/trips/{trip_id}` → 200 `{"trip_id","trip_request","itinerary","generation_meta"}`, con los estados ya recalculados por `effective_status` al momento de la lectura (RN-107; HU-012: el 2026-12-08 a las 10:16 el horario consultado el 01/12 a las 10:15 aparece `to_confirm`); **404 `not_found`** si no existe o pertenece a otra cuenta (no revela su existencia; PREG-61); 401.
- Sincronización y edición de viajes de la cuenta (PUT): sin reglas en 03, pendiente en PREG-60 (ARQ-Q-23).

### 4.10 `GET /v1/health` — salud (HU-006, mitigación de ADR-002)

- 200 `{"status":"ok","db":"ok","checked_at":"2026-11-07T17:50:00Z"}`: ejecuta `SELECT 1` contra C-19 (sin escribir filas) para evitar la pausa de 7 días de Supabase Free.
- 503 `{"status":"degraded","db":"unreachable","checked_at":"…"}`.
- Sin autenticación ni datos sensibles. El ping de C-20 lo llama cada 10 min.

---

## 5. Flujo del invitado y asociación de viajes

### 5.1 Secuencia nominal

```
Invitado (navegador)            C-01/C-03/C-18 (web)                 C-04 API                     C-19 DB
 1 abre la app ───────────────► lee IndexedDB `guest_trips`
                                 ├─ vacío → formulario (HU-001 borde)
                                 └─ hay viaje → muestra itinerario ──► POST /reliability/refresh ─► (sin DB)
                                                                       ◄── estados vigentes
 2 escribe "Cusco" ────────────► GET /destinations/resolve ──────────► C-15 ──► status=resolved
 3 completa grupo y ritmo ─────► validación de UX (core) + aviso RN-036
 4 pulsa "Generar" ────────────► ¿hay viaje guardado? → "Esto reemplazará tu itinerario a Cusco. ¿Continuar?" (RN-076)
                                 guarda pending_operation(generate)
                                 POST /itineraries/generate ──────────► C-05→C-15→C-12→C-06→C-08→C-09 (fase 1)→C-10 (+C-15/C-16)→C-09 (fase 2)→C-11
                                 a los 3 s: "Armando tu itinerario día por día…" (PAR-11)
                                 ◄──────────────────────────── 200 itinerary (≤ 60 s, PAR-09)    (0 filas)
 5                               transacción IndexedDB: escribe el viaje nuevo (y borra el anterior)
                                 borra pending_operation
                                 1.ª vez: "Tu itinerario se guardó en este dispositivo. Si borras los datos del navegador, lo perderás." (RN-075)
 6 ajusta (eliminar / alternativa / regenerar día) → mismo patrón: escribe solo tras un 200
 7 decide crear cuenta ────────► POST /auth/register (o login / OIDC) ► C-13 ─────────────────► INSERT users, sessions
                                 ◄── Set-Cookie HttpOnly
 8                               lista los viajes locales con associated_trip_id = null
                                 si la lista está vacía → no pregunta nada (HU-012 borde)
                                 por cada viaje, un diálogo: "¿Guardar 'Cusco, 2026-12-18' en tu cuenta?" [Sí] [No]
                                 sin opción "Guardar todo" marcada por defecto (RN-079)
 9 responde Sí a Cusco ────────► POST /trips/associations (1 viaje) ► valida + atestaciones ──► INSERT trips, consents
   responde No a Arequipa        (nada se envía; Arequipa sigue local: RN-081)
10                               ◄── 201 {trip_id} → marca associated_trip_id en IndexedDB
                                 muestra la confirmación; la copia local se conserva
```

### 5.2 Fallas de red en medio del flujo

| Momento | Qué pasa | Garantía |
|---|---|---|
| Sin conexión antes de actuar | El cliente detecta `navigator.onLine === false` o una falla de `fetch`. Muestra el banner "Sin conexión. Algunas acciones no están disponibles." y deshabilita Generar, Pedir alternativa y Regenerar día ("Necesitas conexión para regenerar"). Eliminar sigue disponible. | RN-090, RN-091; textos restantes: PREG-58 |
| Corte durante generar, pedir alternativa o regenerar | `fetch` falla o llega a PAR-09 (60 s / 30 s) y el cliente aborta con `AbortController`. IndexedDB no se toca: el viaje o día anterior queda completo. `pending_operation` persiste. | RN-092, HU-011 |
| Vuelve la conexión | Si existe `pending_operation`, el cliente muestra "Se interrumpió la regeneración del Día 3. ¿Reintentar?". **Nunca reenvía automáticamente**. Si el usuario no confirma, borra `pending_operation` y no consume cupo. | RN-093 |
| El servidor completó la operación, pero la respuesta se perdió | El cupo pudo descontarse aunque el cliente no recibió nada. Riesgo aceptado para el piloto; mitigación propuesta: cabecera `Idempotency-Key` por operación, cuya respuesta se guarda en memoria del proceso 10 min [ARQ-propuesta] (ARQ-Q-09). | RN-089 (parcial) |
| Corte durante `reliability/refresh` | Se muestra el itinerario con el respaldo de ADR-005: todo dato con `valid_until < now` se presenta como "! Por confirmar". El cliente reintenta 1 vez cuando vuelve la conexión (no es una operación de IA). | RN-107 (propuesta PREG-07) |
| Corte durante el registro o el login | No se creó sesión o el cliente no recibió la cookie: se pide reintentar. Un registro repetido devuelve 409 → se ofrece el login. | — |
| Corte durante la asociación de un viaje | El viaje sigue completo en IndexedDB (HU-012). El cliente guarda en memoria la cola de viajes **ya consentidos** de esta sesión y, al volver la conexión, reintenta **solo esos** (`POST` idempotente: 201 o 200). Si se cierra la pestaña, la cola se pierde y el consentimiento se vuelve a pedir en el siguiente inicio de sesión (propuesta para PREG-61). | RN-080, RN-081 |
| Asociación con datos alterados | Un dato `verified` con atestación inválida rechaza la asociación con 422 y no se guarda nada (caso (c) de §3.2); nunca se acepta un `verified` emitido por el cliente. Si la atestación es válida pero venció o la fuente se retiró, se guarda como `to_confirm` (caso (d)). | RN-063, RN-106 |

---

## 6. ADRs

### ADR-001: Enmienda constitucional 2.0.0

- **Estado:** Aceptado. Aprobado por Pavel (PO) en el Checkpoint 1 del 2026-11-07. La aprobación final del documento 00 sigue pendiente (estado *Draft* de 00).
- **Contexto:** la Constitución 1.0.0 (2026-11-06) nombraba frameworks y librerías (FastAPI, Pydantic, React + Vite, React Native + Expo, SecureStore, pytest, mypy, Ruff, Playwright, Maestro) antes de que se eligiera el stack (D-07), exigía un idioma del itinerario elegible desde v1 y dejaba tensiones con el alcance (requisitos migratorios, almacenamiento local de rangos de edad: Anexo B #4 de 03 y PREG-11). La auditoría de alineación registró los hallazgos AL-NN.
- **Decisión:** se adopta la versión **2.0.0** con estas enmiendas (historial de 00):

  | Hallazgo | Tipo | Cambio | Principios |
  |---|---|---|---|
  | AL-26 | MAJOR | v1 solo en es-419, sin selector de idioma; la elección del idioma del itinerario queda condicionada a que exista un segundo idioma (relaja PRINCIPIO-011). | PRINCIPIO-011 |
  | AL-22 | PATCH | Neutralidad tecnológica: se quitan los nombres de herramientas; se fijan en el ADR de stack (ADR-002), que debe cumplir cada "Cómo se verifica". | 004, 005, 006, 007, 009, 010, 012 |
  | AL-23 | PATCH | Mercado inicial Perú y Latinoamérica; se exige un ADR por país antes del lanzamiento público fuera de Perú. | Preámbulo |
  | AL-01 / AL-02 / AL-24 | PATCH | La IA no redacta requisitos migratorios; aviso con enlaces oficiales en todo itinerario. | PRINCIPIO-003, PRINCIPIO-008 |
  | AL-29 | PATCH | PRINCIPIO-006 prohíbe guardar en el almacenamiento local credenciales, tokens y datos identificativos; se permite guardar el itinerario del invitado con rangos de edad sin identificadores. | PRINCIPIO-006 |

  Es MAJOR porque AL-26 relaja un principio (regla de modificación del preámbulo de 00).
- **Alternativas consideradas:** (1) *Mantener la 1.0.0*: habría fijado el stack antes del Checkpoint 2 y obligado a construir un selector de idioma sin un segundo idioma. Se descartó. (2) *Publicar la enmienda como MINOR*: viola la regla de versionado, porque AL-26 relaja un principio. Se descartó. (3) *Diferir AL-29 al ADR de almacenamiento*: dejaba sin resolver la contradicción entre PRINCIPIO-005 y PRINCIPIO-006 que bloqueaba F-01. Se descartó.
- **Consecuencias:** ADR-002 nombra las herramientas que la Constitución exige verificar; un segundo idioma (pt-BR) requiere un ADR nuevo; AL-29 exige validación legal antes del lanzamiento público; la cabecera de 03 ya cita la Constitución v2.0.0 y el fondo de su Anexo B #4 lo resolvió PREG-11 (ARQ-Q-30, resuelta). El cambio del PO a "v1 solo web" (ADR-007) **no requiere enmienda**: los principios que mencionan mobile (006, 010, 012) se cumplen en v1 sobre la web en navegador móvil y se vuelven a aplicar a la app al activar C-02.

### ADR-002: Stack tecnológico

- **Estado:** Aceptado en lo que decidió el PO: D-09 (Checkpoint 2, 12:23), modificado por el cambio del PO de las 12:26 (web responsive en v1, Expo post-v1). Las herramientas concretas de calidad están en estado Propuesto y requieren aprobación humana de dependencias (PRINCIPIO-007).
- **Contexto:** 1 persona, USD 0, piloto de 20 a 50 usuarios; reglas de negocio solo en el backend y `core` compartido (PRINCIPIO-009); Argon2id obligatorio y bcrypt prohibido (PRINCIPIO-006); invitado sin persistencia en el backend (PRINCIPIO-005); esperas de 60 s y 30 s (PAR-09). Datos de capa gratuita del checkpoint 2, consultados el 2026-11-07.
- **Decisión:**

  | Capa | Elección |
  |---|---|
  | Web (v1) | **Next.js** (React, TypeScript estricto), web responsive para 360–430 px y ≥ 1024 px, **instalable como PWA** (manifest + service worker mínimo; sin costo adicional) |
  | Mobile | **Expo (React Native) post-v1** (C-02); en v1, "mobile" = la web en el navegador del celular (ADR-007) |
  | Backend | **FastAPI** (Python 3.12, Pydantic v2) en **Render Free**, 1 instancia y 1 worker |
  | Base de datos | **Postgres de Supabase Free solo como base de datos** (sin Supabase Auth ni Data API: RLS que deniega todo) |
  | Autenticación | **Propia**: contraseñas con **Argon2id** (`argon2-cffi`: `PasswordHasher(time_cost=2, memory_cost=19456, parallelism=1)`), **OAuth 2.0 / OIDC opcional** (`authlib`), sesiones opacas con hash SHA-256 en la tabla `sessions` |
  | Monorepo | pnpm workspaces (TS) + uv (Python); paquete **`core` TypeScript con tipos generados del OpenAPI** de FastAPI (`openapi-typescript`) y cliente `openapi-fetch`; los PAR se exportan desde `domain/params.py` a `core/src/params.json` |
  | i18n | i18next con un archivo `es-419.json` único en `core` |
  | Almacenamiento local | IndexedDB vía `idb` (ADR-004) |

  **Herramientas de calidad que exige la Constitución:**

  | Exigencia (principio) | Backend (Python) | `core` y web (TypeScript) |
  |---|---|---|
  | Tipos en modo estricto (PRINCIPIO-012) | `mypy --strict` | `tsc` con `"strict": true`, `noUncheckedIndexedAccess` |
  | Linter sin errores (PRINCIPIO-012) | Ruff (incluida la regla `TID251` banned-api) | ESLint + `typescript-eslint` |
  | Validación de esquemas que rechaza campos no declarados (PRINCIPIO-004, RN-095) | **Pydantic v2 con `model_config = ConfigDict(extra="forbid", strict=True)`** en todos los modelos de entrada y en `LlmItineraryOutput` | Tipos generados (solo UX) |
  | Pruebas y cobertura ≥ 80 %; 100 % en `reliability/` y `llm_context/` (PRINCIPIO-012) | pytest + pytest-cov (`--cov-fail-under=80`, y un segundo job con `--cov-fail-under=100` sobre `reliability` y `llm_context`) | Vitest + `@vitest/coverage-v8` (umbral 80 % en `core`) |
  | E2E web con las mismas aserciones en mobile y desktop (PRINCIPIO-012) | — | **Playwright** con proyectos `mobile-360` (360 × 800, emulación táctil, perfil Android), `mobile-430` (430 × 932) y `desktop-1024` (1024 × 768) que ejecutan el **mismo** archivo de escenarios |
  | E2E mobile nativo | Post-v1: **Maestro** al activar C-02 | — |
  | Accesibilidad (PRINCIPIO-010) | — | `@axe-core/playwright` (0 violaciones *serious* y *critical*), `eslint-plugin-jsx-a11y`; checklist manual con TalkBack (Chrome Android) y VoiceOver (Safari iOS) sobre la web |
  | Snapshot de las 3 etiquetas de estado (PRINCIPIO-003) | — | Vitest + Testing Library: snapshots del componente `ReliabilityBadge` en los tres viewports |
  | Sin literales de UI (PRINCIPIO-011) | — | `eslint-plugin-i18next` (`no-literal-string`) + test de que cada clave usada existe en `es-419.json` |
  | Contrato OpenAPI = `core` (PRINCIPIO-009) | Export de `openapi.json` en CI | Regeneración de `schema.d.ts` y `params.json` + `git diff --exit-code` |
  | SDK de LLM solo en el adaptador (PRINCIPIO-009) | Ruff `TID251` (prohíbe el módulo del SDK fuera de `adapters/llm/`) + `import-linter` (contratos de capas) | — |
  | Credenciales fuera del almacenamiento local (PRINCIPIO-006) | — | ESLint `no-restricted-globals` / `no-restricted-properties` (§3.3, regla 5) |
  | Secretos (PRINCIPIO-006) | **gitleaks** en CI y pre-commit | ídem |
  | TLS (PRINCIPIO-006) | `testssl.sh` contra cada entorno en CI programado | ídem |
  | Logs sin payload (PRINCIPIO-005) | Procesador de lista blanca en `structlog` + test que captura los logs y falla si hay claves fuera de la lista | — |
  | Golden set de no invención (PRINCIPIO-002, PRINCIPIO-012) | `tests/golden/` en pytest con aserciones sobre la salida estructurada; obligatorio en todo PR que toque `orchestration/`, `adapters/llm/`, `reliability/`, `content_guard/` o los prompts | — |
  | Spec aprobada (PRINCIPIO-001) | Check de CI que lee `Spec: NNN` y valida el frontmatter de `specs/NNN-*/spec.md` | ídem |

  **Mitigaciones de D-09:** (1) **Ping cada 10 min** a `GET /v1/health` durante el piloto (C-20). Render apaga el servicio tras 15 min sin tráfico y tarda ~1 min en volver, lo que choca con los 60 s de PAR-09; con el ping, el servicio queda encendido 744 h en un mes de 31 días, dentro de las 750 h/mes del workspace. (2) **`/v1/health` ejecuta `SELECT 1`** contra Supabase para evitar la pausa tras 7 días sin solicitudes.
- **Alternativas consideradas** (checkpoint 2):
  - **A. Expo universal + Supabase** (Expo Router con react-native-web; Supabase Edge Functions en TypeScript/Deno; Supabase Auth solo con OAuth o enlace mágico). *Era la recomendación del Orquestador.* Motivos de descarte, tomados de los contras registrados en el checkpoint (el PO eligió B sin dejar otro motivo por escrito): web con react-native-web menos pulida; reglas de negocio en Edge Functions de Deno, sin Python ni Pydantic para validar la salida del LLM; Supabase Auth obligaba a eliminar las contraseñas para no usar bcrypt (PRINCIPIO-006); pausa tras 7 días sin uso.
  - **C. Next.js (web + API en Vercel Hobby) + Expo + Supabase**. Descartada porque Vercel Hobby es "solo uso personal y no comercial" (válido para el diploma, no para lanzar); hay que mantener 2 UIs iguales; también dependía de Supabase Auth sin contraseñas.
  - **Postgres gratis de Render** (1 GB) en lugar de Supabase: descartado porque **expira a los 30 días** (render.com/docs/free).
- **Consecuencias:**
  - (+) Python y Pydantic validan la salida del LLM con `extra="forbid"`; Argon2id directo; el backend no cambia cuando se agregue Expo, porque `core` y `/v1` ya son agnósticos al cliente.
  - (−) Dos lenguajes (TypeScript y Python) y tres piezas desplegadas (web, API, base) para una sola persona.
  - (−) El ping hace que el servicio de la API sea el **único** encendido 24/7 que admite el workspace de Render (744 de 750 h); cualquier otro servicio en Render Free ya no cabe.
  - (−) El hosting de la web Next.js **no lo fija D-09** (ARQ-Q-01). La cookie de sesión entre dominios distintos (web ≠ API) es frágil ante el bloqueo de cookies de terceros: la propuesta es servir la API bajo el mismo sitio que la web mediante un *rewrite* `/api/*` → API (ARQ-Q-26).
  - (−) Uvicorn con 1 worker: lo exige C-12 en memoria (ADR-008). Para 20 a 50 usuarios es suficiente, a confirmar con la medición de SUP-03.
  - Los datos de capa gratuita hay que reconfirmarlos antes del piloto (R5 del brief).

### ADR-003: Reglas de negocio y estados de confiabilidad solo en el backend

- **Estado:** Propuesto.
- **Contexto:** PRINCIPIO-002 (el LLM no inventa datos; solo la capa de verificación asigna Verificada), PRINCIPIO-003 (formato y vigencia), PRINCIPIO-009 (el backend es la única fuente de verdad) y PRINCIPIO-012 (resultado funcional idéntico entre plataformas). Si las reglas o los estados se calcularan en el cliente, la web y la futura app podrían divergir y un cliente podría falsificar un "✓ Verificada".
- **Decisión:**
  1. Todas las RN del MVP se ejecutan en C-05 a C-12 (tabla §2.1). El cliente usa las constantes de `core/params.json`, generadas desde `domain/params.py`, **solo** para avisos y validación de UX (p. ej., deshabilitar "Generar" si faltan 14 días); nunca para decidir.
  2. El LLM responde con `LlmItineraryOutput`, cuyo enum de estado **no contiene `verified`**. Si llega `verified` o falta un estado, la salida es inválida (RN-063, RN-109) → 1 reintento (PAR-10) → 502. Es el caso (a) de la semántica única de §3.2 (QB-03); los casos (b) a (d), para datos que envía el cliente, están en ADR-005.
  3. C-10 es el único código que construye un `FactualDatum` con `verified`, a partir de la evidencia que obtiene contrastando cada actividad con C-15 (`contrast_activity`, §4.2 paso 4): exige que `source_id` esté en C-16, que la categoría esté permitida para esa fuente y que haya vigencia (7 días o la que declare la fuente; sin fecha declarada no hay `verified`: RN-070). Lo firma con HMAC-SHA256 (ADR-005).
  4. La vigencia la calcula `reliability.effective_status(datum, now)` en el backend (generación, refresh, lectura de viajes de la cuenta y asociación).
  5. Los textos de los estados los renderiza el cliente desde las claves i18n de `core` (`reliability.verified.label` → "✓ Verificada", etc.). El backend entrega solo `reliability_status`, `source.name`, `checked_at` y las claves `estimation_basis.key` y `suggested_source.key`, de catálogos cerrados.
  6. Las violaciones de reglas en una salida bien formada (carga > PAR-05, actividad excluida por edad o cierre) se tratan como salida inválida y usan el reintento. Propuesta para PREG-09 (ARQ-Q-11).
- **Alternativas consideradas:** (1) *Calcular la vigencia en el cliente*: más simple y funciona sin conexión, pero duplica una regla de negocio y permite falsificar estados. Se descarta como mecanismo principal y se conserva solo como respaldo de presentación (ADR-005). (2) *Dejar que el LLM proponga `verified` y que el backend lo degrade*: aumenta la superficie de error y contradice RN-063. Se descarta. (3) *Completar con `estimated` los datos sin estado*: prohibido por RN-109.
- **Consecuencias:** más llamadas al backend (refresh al reabrir); los tests de `reliability/` y `llm_context/` con 100 % de cobertura son bloqueantes; el golden set corre en todo PR que toque prompts, el modelo o la verificación; la paridad web/mobile se obtiene por construcción, porque ambos clientes solo pintan lo que llega.

### ADR-004: Almacenamiento local del invitado en v1 (solo navegador)

- **Estado:** Propuesto.
- **Contexto:** PRINCIPIO-005 (el itinerario del invitado vive solo en el dispositivo), PRINCIPIO-006 tras AL-29 (sin tokens ni datos identificativos; los rangos de edad sin identificadores están permitidos), RN-073, RN-075, RN-076, RN-078, RN-092. En v1 solo existe el cliente web (ADR-007). Público con Android de gama baja y conexión intermitente.
- **Decisión:** IndexedDB a través de `idb`, encapsulado en `GuestTripStore` (C-03) con el esquema de §3.3. Escrituras en transacciones atómicas, solo tras recibir un 200 completo; `schema_version` para migraciones locales; `navigator.storage.persist()` en el primer guardado; un viaje activo en el MVP y 5 con F-11 (PAR-08). La app Expo (post-v1) implementará la misma interfaz con su propio almacenamiento, definido en un ADR al activarla.
- **Alternativas consideradas:** (1) `localStorage`: síncrono (bloquea el hilo principal en equipos de gama baja), sin transacciones y con cuota menor. Se descarta. (2) Cache Storage o service worker: pensado para recursos HTTP, no para datos de dominio; además, el uso sin conexión es F-14 (Could). Se descarta. (3) Cifrar el itinerario en el cliente: la clave tendría que vivir en el mismo navegador, así que no aporta protección real, y AL-29 ya lo permite sin cifrar. Se descarta, sujeto a la validación legal.
- **Consecuencias:** si el usuario borra los datos del navegador, pierde el itinerario (lo advierte RN-075); posible desalojo de datos en Safari iOS (ARQ-Q-19); el comportamiento ante cuota llena o navegación privada está pendiente (PREG-63); el umbral de SUP-04 hay que medirlo (PREG-56).

### ADR-005: Ajustes y recálculo sin estado con atestación HMAC (propuesta para PREG-07)

- **Estado:** Propuesto.
- **Contexto:** pedir una alternativa, regenerar un día y reabrir un itinerario necesitan el itinerario vigente, pero el backend no puede persistir los datos del invitado (PRINCIPIO-005) y sigue siendo la única fuente de verdad (PRINCIPIO-009). RN-107 exige recalcular la vigencia al reabrir, y RN-082 exige conservar el `checked_at` original al asociar un viaje. PREG-07 sigue abierta.
- **Decisión:**
  1. El cliente envía el itinerario (o, en el refresh, solo los hechos `verified`) en cada operación. El backend lo procesa en memoria y lo descarta al responder.
  2. Cada dato `verified` lleva `attestation` = HMAC-SHA256 con la clave `ATTESTATION_KEY_{kid}` (variable de entorno de Render, nunca versionada), calculada sobre la serialización canónica (JSON con claves ordenadas, UTF-8) de `fact_id`, `category`, `value`, `source_id`, `checked_at` y `valid_until`. La rotación se hace con `kid`, aceptando el `kid` anterior durante un período definido en ARQ-Q-27.
  3. El backend acepta un `verified` del cliente solo si la atestación es válida (semántica única de §3.2, QB-03): un dato mal formado → 422 (caso b); atestación inválida, o fuente no registrada sin atestación válida → **422, rechazo** (caso c), coherente con PRINCIPIO-002 ("un dato con `verified` sin `source` registrada se rechaza"); atestación válida con fuente retirada de C-16 o vigencia vencida → `to_confirm` con `source_removed` / `expired` (caso d, vigencia). *Texto anterior (sustituido el 2026-11-07): "si no, lo trata como `to_confirm`; nunca lo rechaza por eso".* Un cliente legítimo solo tiene atestaciones emitidas por el backend, así que el 422 de (c) solo aparece ante datos alterados; el caso de un `kid` retirado por rotación queda en ARQ-Q-27.
  4. **Respaldo sin conexión** (solo presentación): si el refresh no se puede hacer, el cliente compara `valid_until` (calculado por el backend) con su reloj y muestra "! Por confirmar" cuando venció. Nunca sube un estado. La aceptación de este respaldo es parte de PREG-07.
- **Alternativas consideradas:** (1) *Guardar los itinerarios de invitados en el servidor con un id anónimo*: viola PRINCIPIO-005. Se descarta. (2) *Confiar en los estados que envía el cliente*: un cliente podría inventar un "✓ Verificada". Se descarta. (3) *Recalcular solo en el cliente*: duplica la regla (PRINCIPIO-009). Queda solo como respaldo.
- **Consecuencias:** los cuerpos de las solicitudes crecen (límite de 256 KiB [ARQ-propuesta]); hay que gestionar una clave secreta; el recálculo depende de la conexión, que es coherente con el MVP (la reapertura requiere conexión: nota de HU-001).

### ADR-006: Abstracción del proveedor de LLM

- **Estado:** Propuesto (proveedor concreto pendiente: PREG-13).
- **Contexto:** PRINCIPIO-009 (capa de abstracción propia y registro del proveedor y la versión), PRINCIPIO-005 y RN-099 (sin entrenamiento ni retención), DEP-01 (riesgo alto: límites y cambios de la capa gratuita), RN-086 (secundario opcional), RES-09.
- **Decisión:** puerto `LlmProvider` en `adapters/llm/` con una implementación por proveedor y una selección por configuración (`LLM_PRIMARY`, `LLM_SECONDARY` opcional). Salida estructurada contra el JSON Schema de `LlmItineraryOutput`. Cada respuesta devuelve `provider_id` y `model_version`, que viajan en `generation_meta` (guardado con el itinerario en el dispositivo o en la cuenta) y, si lo aprueba PREG-66, en la línea de log. El SDK se prohíbe fuera del adaptador con Ruff `TID251` e `import-linter`. Solo se puede configurar un proveedor si su ADR de selección documenta la no retención, el no entrenamiento y el país de procesamiento (decisión pendiente n.° 3 de 00).
- **Alternativas consideradas:** (1) *Llamar al SDK desde los endpoints*: viola PRINCIPIO-009. (2) *Usar un gateway de terceros multi-proveedor*: agrega un procesador de datos más (decisión pendiente n.° 4 de 00) y otra capa gratuita que evaluar. Se descarta para el piloto.
- **Consecuencias:** cambiar de proveedor no toca el dominio; el golden set debe pasar al 100 % en cada cambio de proveedor o de modelo; mientras PREG-13 siga abierta, F-03 y F-05 no pueden pasar a producción.

### ADR-007: Distribución en v1: solo web responsive; app Expo post-v1

- **Estado:** Aceptado (decisión del PO del 2026-11-07 a las 12:26; registrada como D-10: ARQ-Q-28, resuelta). Resuelve RES-11 y PREG-16 para v1.
- **Contexto:** equipo de 1 persona (RES-01); las HUs son "ambas" y deben dar el mismo resultado funcional (PRINCIPIO-012), así que mantener dos clientes duplica el trabajo de UI y de E2E; distribuir en iOS fuera de Expo Go requiere Apple Developer (USD 99/año), fuera de USD 0; Expo Free permite hasta 15 builds Android y 15 iOS, y 1.000 MAU de Expo Update; Google Play tiene un pago único (RES-11).
- **Decisión:** v1 y el piloto se entregan **solo como web responsive** con Next.js: navegador móvil de 360 a 430 px y escritorio ≥ 1024 px, instalable como PWA (sin costo). La app Expo (C-02) pasa a **post-v1**. El backend `/v1`, los contratos OpenAPI y el paquete `core` se mantienen agnósticos al cliente para que agregar Expo no cambie el backend. "Mobile" en v1 = la web en el navegador del celular.
- **Alternativas consideradas:** (1) *Web + APK Android interno vía EAS + iOS vía web* (la opción que este documento proponía antes del cambio): dos UIs y dos suites E2E (Playwright + Maestro) para una persona, con iOS igualmente en web. Se descarta para v1. (2) *Publicar en tiendas*: costo de cuentas de desarrollador incompatible con USD 0. Se descarta. (3) *Expo universal (react-native-web) como única UI*: es la opción A del checkpoint, ya descartada por el PO.
- **Consecuencias:**
  - En v1, **PRINCIPIO-012 (paridad) se verifica entre los viewports mobile y desktop de la misma web**: el mismo archivo de escenarios Playwright corre en los proyectos `mobile-360`, `mobile-430` y `desktop-1024` con las mismas aserciones funcionales. Al activar C-02 se agrega Maestro con los mismos escenarios.
  - PRINCIPIO-006 en v1: los tokens van solo en cookies `HttpOnly; Secure; SameSite`; el almacén seguro del SO aplica a C-02.
  - PRINCIPIO-010 en v1: TalkBack se prueba sobre Chrome Android y VoiceOver sobre Safari iOS; los objetivos táctiles miden ≥ 44 × 44 px CSS (equivalente web de los 44 × 44 pt).
  - Diseño para Android de gama baja: presupuesto de JS inicial y métricas de rendimiento por medir (ARQ-Q-29); sin reintentos automáticos de IA; gzip; formularios sin dependencias pesadas.
  - 06-user-stories ya dice "web responsive" (§0 e índice, por D-10): en v1 los dos viewports de la web (ARQ-Q-28, resuelta).

### ADR-008: Límite de operaciones por dispositivo sin persistencia (propuesta para PREG-17 y PREG-30)

- **Estado:** Propuesto.
- **Contexto:** RN-043 (15 operaciones exitosas por dispositivo cada 24 h, PAR-07), RN-089 (las fallidas no cuentan), PRINCIPIO-005 (test de 0 filas nuevas para invitados), RN-096 (ni IP ni `device_id` al LLM). PREG-17 (identificación del dispositivo) y PREG-30 (conteo y ventana) siguen abiertas.
- **Decisión:** el cliente genera en el primer arranque un `device_id` UUID v4 aleatorio, lo guarda en IndexedDB (`device`) y lo envía en `X-Device-Id`. C-12 guarda en **memoria del proceso** un diccionario `HMAC-SHA256(QUOTA_KEY, device_id) → [timestamps de operaciones exitosas]` con una ventana móvil de 24 h y purga las entradas vencidas. Cada operación reserva un cupo con un `asyncio.Lock` (evita que dos solicitudes simultáneas superen 15), lo confirma con un 200 (incluido `no_valid_option` si PREG-30 lo cuenta) y lo libera ante 5xx o un corte. La IP no se usa.
- **Alternativas consideradas:** (1) *Tabla en Postgres*: sobrevive a los reinicios, pero crea filas por invitado y rompe el test de PRINCIPIO-005. Se descarta salvo que el PO lo reinterprete (ARQ-Q-07). (2) *Límite por IP*: penaliza las NAT compartidas (redes móviles, campus) y trata un dato personal. Se descarta. (3) *Firmar un contador en el cliente*: se evade borrando el almacenamiento, igual que el `device_id`, y es más complejo. Se descarta.
- **Consecuencias:** un reinicio o despliegue de la API pone los contadores en cero (riesgo aceptado para el piloto; el ping reduce los reinicios por inactividad); borrar los datos del navegador evade el límite (riesgo aceptado para 20 a 50 usuarios conocidos); exige 1 solo proceso (ADR-002); el `device_id` en el almacenamiento local depende de la interpretación de PRINCIPIO-006 (ARQ-Q-07).

---

## 7. Restricciones técnicas

### 7.1 Restricciones y su origen

| # | Restricción técnica | Valor concreto | Origen |
|---|---|---|---|
| RT-01 | Ningún PR sin `Spec: NNN` aprobada (salvo la etiqueta `no-spec`, puesta por una persona) | Check de CI bloqueante | PRINCIPIO-001 |
| RT-02 | Todo dato factual de una respuesta lleva `reliability_status`, `checked_at` y, según el estado, `source` + `valid_until` + `attestation`, `estimation_basis` o `suggested_source` | Validación Pydantic de las respuestas antes de serializar | PRINCIPIO-002, PRINCIPIO-003 |
| RT-03 | Vigencia de `verified` | 7 días para precio, horario y restricciones; la declarada por la fuente para disponibilidad, clima y traslados; sin fecha → nunca `verified` | PRINCIPIO-003, RN-068, RN-070 |
| RT-04 | Entrada sin campos no declarados; los menores solo con `AgeRange` | `extra="forbid"`; 422 | PRINCIPIO-004, RN-095 |
| RT-05 | Contexto del LLM limitado a 8 campos permitidos (+ resumen de actividades en los ajustes) | Test que inspecciona el payload al proveedor | PRINCIPIO-004, RN-096 |
| RT-06 | 0 filas en la base por operaciones de invitado; logs solo con campos de la lista blanca | Test de integración + test de logs | PRINCIPIO-005, RN-074, RN-097 |
| RT-07 | Eliminación de cuenta, incluidos los respaldos | ≤ 30 días | PRINCIPIO-005 |
| RT-08 | TLS ≥ 1.2 (1.3 preferido); HTTP → 301 + HSTS `max-age=31536000` | `testssl.sh` en CI | PRINCIPIO-006 |
| RT-09 | Contraseñas con Argon2id | m = 19 MiB (19456 KiB), t = 2, p = 1 | PRINCIPIO-006 |
| RT-10 | Sesión web solo en una cookie `HttpOnly; Secure; SameSite=Lax`; nada sensible en IndexedDB | Lint + test E2E que inspecciona el almacenamiento | PRINCIPIO-006, RN-098 |
| RT-11 | Agentes de IA sin merge, sin despliegue, sin tocar ADR, specs ni tests, sin agregar dependencias | Protección de `main`, CODEOWNERS sobre `docs/adr/`, `specs/**/spec.md`, lockfiles y `apps/api/src/atp_api/data/*.yaml` | PRINCIPIO-007 |
| RT-12 | Sin enlaces de reserva, pago o afiliados; sin requisitos migratorios redactados; sin "seguro" sin fuente | C-11 + golden set | PRINCIPIO-008, RN-023, RN-025, RN-104 |
| RT-13 | Reglas solo en el backend; `core` generado del OpenAPI; SDK de LLM solo en `adapters/llm/` | CI con diff de los tipos generados; Ruff `TID251` | PRINCIPIO-009 |
| RT-14 | WCAG 2.2 AA: contraste ≥ 4.5:1 (texto normal) y ≥ 3:1 (texto grande); objetivos ≥ 44 × 44 px CSS; zoom al 200 % sin pérdida de contenido; teclado completo | axe-core en Playwright (0 *serious* y *critical*) | PRINCIPIO-010 |
| RT-15 | es-419 únicamente; sin literales de UI; `Intl` con `es-PE` para montos | `eslint-plugin-i18next`; test de claves | PRINCIPIO-011 |
| RT-16 | Cobertura ≥ 80 % (backend y `core`) y 100 % (`reliability/`, `llm_context/`); golden set al 100 % cuando corresponde; mismos E2E en 360, 430 y 1024 px | CI bloqueante | PRINCIPIO-012 |
| RT-17 | Plazos del cliente: generación 60 s; alternativa y regenerar día 30 s; progreso visible a los 3 s | `AbortController` en `core` | PAR-09, PAR-11 |
| RT-18 | Reintentos automáticos ante salida inválida del LLM: 1; reintentos automáticos del cliente en operaciones de IA: 0 | C-07; contrato de `core` | PAR-10, RN-085, RN-093 |
| RT-19 | Límite de 15 operaciones de IA exitosas por dispositivo cada 24 h | C-12 | PAR-07 |
| RT-20 | Viaje de hasta 14 días, inicio a ≤ 365 días, hasta 10 viajeros | C-05 | PAR-01, PAR-02, PAR-03 |
| RT-21 | Viewports soportados en v1: 360–430 px (móvil) y ≥ 1024 px (escritorio); entre 431 y 1023 px el layout debe ser usable sin scroll horizontal | Proyectos de Playwright | Decisión del PO del 2026-11-07 a las 12:26 (ADR-007) |
| RT-22 | Cuerpo de las solicitudes ≤ 256 KiB; ≤ 500 hechos por refresh | 413 | [ARQ-propuesta], calibrar con SUP-04 (PREG-56) |
| RT-23 | Lista curada de enlaces oficiales revisada al menos cada 30 días | Test de CI sobre `reviewed_at` | DEP-04, RN-024 |
| RT-24 | Costo mensual USD 0 en todos los componentes | Tabla §7.2 | D-05, RES-02 (ningún PRINCIPIO de 00 lo fija; restricción de alcance) |

### 7.2 Costo USD 0 por componente

Datos del checkpoint 2 (consultados el 2026-11-07; hay que reconfirmarlos antes del piloto).

| Componente | Servicio | Capa gratuita | Límite relevante | Riesgo |
|---|---|---|---|---|
| C-01 Cliente web (hosting) | **Pendiente (ARQ-Q-01)**. Candidato evaluado en el checkpoint: Vercel Hobby | Vercel Hobby: solo uso personal y no comercial; funciones de hasta 300 s; 1.000.000 de invocaciones y 4 h de CPU activa al mes | Uso no comercial | Válido para el diploma, no para un lanzamiento comercial; define también la solución de cookies (ARQ-Q-26) |
| C-04 API FastAPI | Render Free (web service) | 750 h/mes por workspace | Se apaga tras 15 min sin tráfico; ~1 min para volver | Arranque en frío > 60 s de PAR-09 → mitigado con C-20; el ping consume 744 h de las 750 h (ningún otro servicio 24/7 en el workspace) |
| C-19 Base de datos | Supabase Free (solo Postgres) | 500 MB de base; 5 GB de egress | Pausa tras 7 días sin solicitudes | Mitigado con `/v1/health` → `SELECT 1`; falta confirmar que esa consulta cuenta como actividad (ARQ-Q-17); cifrado y respaldos (ARQ-Q-16) |
| C-13 Autenticación | Propia (en C-04) | Sin costo adicional | — | Verificación de correo y recuperación de contraseña requieren un servicio de correo sin capa gratuita identificada (ARQ-Q-15) |
| C-20 Ping | **Pendiente (ARQ-Q-02)** | — | 6 llamadas/h × 744 h = 4.464 llamadas/mes | Sin servicio elegido; un cron con retrasos > 15 min deja apagar a Render |
| C-08 Proveedor de LLM | **Pendiente (PREG-13)** | Desconocida | Solicitudes y tokens por día (RES-05) | Viabilidad con USD 0 y sin entrenamiento no confirmada (RES-09); determina si PAR-07 alcanza (SUP-03) |
| C-15 Fuente de lugares | **Pendiente (PREG-50)** | Desconocida | — | Sin fuente, toda existencia de lugar es `to_confirm` (RN-105) |
| CI y repositorio | **Pendiente (ARQ-Q-31)** | No figura en los insumos | Minutos de CI por mes | El golden set y los E2E en 3 viewports consumen minutos y cuota del LLM |
| C-02 Expo (post-v1) | Expo Free | 15 builds Android y 15 iOS; 1.000 MAU de Expo Update | iOS fuera de Expo Go exige Apple Developer (USD 99/año) | Fuera de v1 (ADR-007) |
| (Descartado) Postgres de Render | Render Free | 1 GB | Expira a los 30 días | Descartado en ADR-002 |
| (No usado) Supabase Auth | Supabase Free | 50.000 MAU | — | No se usa (D-09) |

---

## 8. Trazabilidad

| HU | Prioridad | Componentes | Endpoints |
|---|---|---|---|
| HU-001 Invitado y reapertura local | Must | C-01, C-03, C-18, C-10, C-14, C-12 | `POST /v1/itineraries/generate`, `POST /v1/itineraries/reliability/refresh` (sin endpoint para eliminar: es local) |
| HU-002 Destino y fechas | Must | C-01, C-03, C-05, C-15 | `GET /v1/destinations/resolve`, `POST /v1/itineraries/generate` (422) |
| HU-003 Grupo, menores, necesidades y ritmo | Must | C-01, C-03, C-05, C-06 | `POST /v1/itineraries/generate` (422) |
| HU-004 Itinerario día por día y ritmo | Must | C-07, C-06, C-08, C-09, C-10, C-15, C-20 | `POST /v1/itineraries/generate` |
| HU-005 Adaptación por rangos de edad | Must | C-09, C-10, C-15, C-16, C-01 | `POST /v1/itineraries/generate` |
| HU-006 Esperas, fallas, límite y sin conexión | Must | C-01, C-03, C-07, C-08, C-12, C-18, C-14, C-20 | `POST /v1/itineraries/generate` / `alternative` / `regenerate-day` (429, 502, 503, 504), `GET /v1/quota`, `GET /v1/health` |
| HU-007 Vista por días, franjas y avisos | Must | C-01, C-03, C-09, C-17 | `POST /v1/itineraries/generate` (`notices`), `POST /v1/itineraries/reliability/refresh` (`immigration_links`) |
| HU-008 Estados de confiabilidad vigentes | Must | C-10, C-16, C-01, C-03 | `POST /v1/itineraries/generate`, `POST /v1/itineraries/reliability/refresh`, `GET /v1/trips/{trip_id}` |
| HU-009 Solo contenido respaldado | Must | C-09, C-10, C-11, C-16, C-15 | `POST /v1/itineraries/generate`, `alternative`, `regenerate-day` |
| HU-010 Eliminar o pedir alternativa | Must | C-01, C-18 (eliminar); C-07, C-09, C-10, C-11, C-12 (alternativa) | `POST /v1/itineraries/alternative` |
| HU-011 Regenerar un día | Must | C-01, C-18, C-07, C-09, C-10, C-11, C-12 | `POST /v1/itineraries/regenerate-day` |
| HU-012 Cuenta opcional y asociación | Should | C-01, C-03, C-13, C-19, C-10, C-18 | `POST /v1/auth/register`, `POST /v1/auth/login`, `POST /v1/auth/logout`, `GET /v1/auth/oauth/{provider}/start` y `/callback`, `GET /v1/me`, `POST /v1/trips/associations`, `GET /v1/trips`, `GET /v1/trips/{trip_id}` |
| — (post-v1) | — | C-02 | Reutiliza todos los anteriores sin cambios |

Cobertura de componentes: todos los componentes de v1 (C-01 y C-03 a C-20) trazan a al menos una HU. C-02 es post-v1 y no tiene HUs en v1.

---

## 9. Preguntas del Architect

> **Triaje 2026-11-07:** cada ARQ-Q tiene destino en la última columna: "→ PREG-NN" (05-preguntas-abiertas.md, donde sigue abierta) o "Resuelta: …" (con su fuente). La columna "PREG existente" conserva la referencia original del Architect. Detalle y justificación de cada destino: 05, "Trazabilidad del triaje de 07 y 08".

| ID | Pregunta | Bloquea a | PREG existente | Destino (triaje 2026-11-07) |
|---|---|---|---|---|
| ARQ-Q-01 | ¿Dónde se aloja la web Next.js con USD 0? D-09 no lo fija. El único dato disponible es Vercel Hobby (no comercial). | ADR-002, C-01, despliegue del piloto, ARQ-Q-26 | — (relacionada con DEP-06) | → PREG-70 |
| ARQ-Q-02 | ¿Qué servicio gratuito ejecuta el ping cada 10 min y con qué garantía de puntualidad (< 15 min)? ¿Lo permiten los términos de Render Free? | Mitigación de ADR-002, C-20, HU-004, HU-006 | — | → PREG-71 |
| ARQ-Q-03 | Proveedor de LLM con no entrenamiento, no retención y país de procesamiento compatibles con USD 0 | C-08, ADR-006, HU-004, HU-010, HU-011 | PREG-13 | → PREG-13 |
| ARQ-Q-04 | Fuente de lugares (resolución, existencia, cierre permanente, horarios) y sus límites gratuitos | C-15, HU-002, RN-007, RN-021, RN-105 | PREG-50 | → PREG-50 |
| ARQ-Q-05 | Contenido inicial del registro de fuentes y de los catálogos `estimation_basis` y `suggested_source` | C-16, C-10, HU-008, HU-009 | PREG-06 | → PREG-06 |
| ARQ-Q-06 | ¿Se acepta ADR-005 (refresh sin estado + atestación + respaldo con `valid_until`) como respuesta a "dónde se calcula la vigencia"? | HU-001, HU-008, C-10 | PREG-07 | → PREG-07 |
| ARQ-Q-07 | ¿Un `device_id` UUID anónimo en IndexedDB es compatible con PRINCIPIO-006? ¿Se acepta un contador en memoria que se reinicia con cada despliegue, o el PO prefiere persistirlo (lo que choca con "0 filas" de PRINCIPIO-005)? | ADR-008, C-12, HU-006 | PREG-17, PREG-30 | → PREG-17 |
| ARQ-Q-08 | ¿Una respuesta `no_valid_option` (alternativa o regenerar día) cuenta como operación exitosa para PAR-07? | C-12, HU-010, HU-011 | PREG-30 | → PREG-30 |
| ARQ-Q-09 | ¿Se aprueba `Idempotency-Key` (en memoria, 10 min) para no descontar cupo cuando la respuesta se pierde por la red? | C-12, §5.2 | PREG-30 | → PREG-18 |
| ARQ-Q-10 | ¿Los 60 s y 30 s de PAR-09 incluyen el reintento? ¿Qué margen tiene el plazo del servidor respecto al cliente? Requiere medir latencias. | C-07, RT-17 | PREG-09 | → PREG-09 |
| ARQ-Q-11 | Una salida bien formada que viola reglas (carga > PAR-05, actividad excluida tras la verificación, salida truncada): ¿reintento o corrección? (ADR-003 propone reintento) | C-07, C-09, HU-004, HU-005 | PREG-09 | → PREG-09 |
| ARQ-Q-12 | Mecanismo para detectar datos factuales en texto libre (esquema, detector o golden set) | C-11, HU-009 | PREG-05 | → PREG-05 |
| ARQ-Q-13 | ¿Se acepta 422 + `message_key` para todos los rechazos de validación? Faltan los textos de rechazo y de error de las operaciones de IA. | §4, C-01 | PREG-57, PREG-58 | → PREG-57, PREG-58 |
| ARQ-Q-14 | Zona horaria de la "fecha actual" y de `{HH:mm}`; momento en que se toma la fecha (envío o recepción) | C-05, C-01, HU-002, HU-008 | PREG-23, PREG-32, PREG-67 | → PREG-23, PREG-32, PREG-67 |
| ARQ-Q-15 | Métodos de registro (correo y contraseña, qué proveedores OIDC), política de contraseña, verificación de correo, recuperación de contraseña (requieren un servicio de correo sin capa gratuita identificada), duración de la sesión y eliminación de cuenta | C-13, HU-012 | PREG-60 | → PREG-60 |
| ARQ-Q-16 | ¿Supabase Free cifra en reposo con AES-256 y cómo maneja los respaldos? ¿Cómo se garantiza el borrado en los respaldos en ≤ 30 días? | C-19, PRINCIPIO-005, PRINCIPIO-006 | PREG-60 | → PREG-71 |
| ARQ-Q-17 | ¿Un `SELECT 1` desde el backend cuenta como actividad para la pausa de 7 días de Supabase? ¿Render Free permite conexiones IPv6 o hay que usar el pooler de Supabase (IPv4)? | ADR-002, C-14, C-19 | — | → PREG-71 |
| ARQ-Q-18 | ¿Cuál es el tiempo máximo de respuesta HTTP de Render Free? Debe ser ≥ 60 s para PAR-09. | ADR-002, C-07 | — | → PREG-71 |
| ARQ-Q-19 | Riesgo de desalojo del almacenamiento del navegador en Safari iOS y en Android de gama baja: ¿qué umbral de SUP-04 se mide y en qué equipos? | ADR-004, C-18, HU-001 | PREG-56 | → PREG-56 (y PREG-16; equipos de referencia: PREG-79) |
| ARQ-Q-20 | Comportamiento si IndexedDB no está disponible (navegación privada) o está lleno | C-18, HU-001 | PREG-63 | → PREG-63 |
| ARQ-Q-21 | ¿Qué pasa con el itinerario anterior si la generación de reemplazo falla por la IA (no por la red)? La propuesta es conservarlo. | C-18, HU-001 | PREG-62 | → PREG-62 |
| ARQ-Q-22 | Asociación: ¿cerrar el diálogo equivale a rechazar? ¿Se acepta 404 para un viaje de otra cuenta y la cola en memoria para reintentos tras un corte? | C-13, §4.8, §5.2, HU-012 | PREG-61 | → PREG-61 |
| ARQ-Q-23 | Sincronización y edición de viajes en la cuenta (reglas de conflicto, endpoint PUT); HU-012 supone 2 viajes locales, pero el MVP guarda 1 (depende de F-11) | C-13, HU-012 | PREG-60 | → PREG-60 |
| ARQ-Q-24 | Enlaces del aviso migratorio cuando el país no está en la lista curada o el enlace está roto | C-17, HU-007 | PREG-04 | → PREG-04 |
| ARQ-Q-25 | ¿La dirección, la duración, la distancia y la existencia del lugar son datos factuales? ¿Con qué vigencia se verifica la existencia? | C-10, `FactCategory` | PREG-01 | → PREG-01 |
| ARQ-Q-26 | Si la web y la API están en dominios distintos, la cookie de sesión es de terceros: ¿se acepta servir la API bajo el mismo sitio con un *rewrite* `/api/*` (depende de ARQ-Q-01) o un dominio propio (con costo)? | C-01, C-13, HU-012 | — | → PREG-70 |
| ARQ-Q-27 | Política de rotación de `ATTESTATION_KEY` (período de aceptación del `kid` anterior) frente a itinerarios guardados de larga duración. Desde QB-03, una atestación que no verifica se rechaza con 422 (§3.2 caso c): hay que decidir también si un `kid` retirado se trata como (c) o como vigencia (d). | ADR-005, C-10 | — | → PREG-21 |
| ARQ-Q-28 | Registrar el cambio de las 12:26 (v1 solo web; Expo post-v1) como D-NN y aclarar en 06 que "Plataformas: ambas" en v1 = viewports móvil y escritorio de la web. **Resuelta (2026-11-07):** registrada como D-10; 06 dice "web responsive" (§0 e índice). | ADR-007, 06, PRINCIPIO-012 | PREG-16 (queda resuelta para v1) | Resuelta: v1 y piloto solo web responsive (Next.js); la app Expo pasa a post-v1, y 06 ya dice "web responsive" (D-10) |
| ARQ-Q-29 | Presupuesto de rendimiento para Android de gama baja (JS inicial, tiempo hasta la interacción, red de referencia 3G/4G) y equipos de referencia del piloto | C-01, RT-21 | PREG-56 | → PREG-79 |
| ARQ-Q-30 | 03 cita la Constitución v1.0.0 en su cabecera, y su Anexo B #4 menciona IndexedDB según el texto anterior de PRINCIPIO-005: ¿se actualiza 03? **Resuelta (2026-11-07):** la cabecera de 03 ya cita v2.0.0; el fondo del Anexo B #4 lo resolvió PREG-11 (AL-29). | ADR-001 | PREG-11 | Resuelta: la cabecera de 03 cita la Constitución v2.0.0, y el fondo del Anexo B #4 de 03 (rangos de edad en el almacenamiento local) lo resolvió PREG-11 con la enmienda de PRINCIPIO-006 (03, cabecera; 05 PREG-11, AL-29); solo queda actualizar la redacción de esa fila de 03 |
| ARQ-Q-31 | Servicio de CI y repositorio (minutos gratuitos) y presupuesto de ejecuciones del golden set contra el proveedor real (consume cuota del LLM) | RT-01, RT-16 | PREG-13, PREG-56 | → PREG-72 |
| ARQ-Q-32 | Región de Render y de Supabase (transferencia internacional de datos de cuentas) | C-04, C-19 | — (decisión pendiente n.° 4 de 00) | → PREG-44 |
| ARQ-Q-33 | ¿Se pueden registrar `provider` y `model_version` en la línea de log del invitado, o basta con `generation_meta` en la respuesta? | C-14, HU-001 | PREG-66 | → PREG-66 |
| ARQ-Q-34 | Qué se ofrece cuando no hay una opción válida (alternativa, día o generación completa) | C-07, HU-010, HU-011 | PREG-08 | → PREG-08 |
