# Modelo de Datos — SENA Gestión de Horarios
### Ingeniería inversa desde mockup (24 capturas) + auditoría de `models.md`

> **Metodología**: cada entidad está marcada con su nivel de evidencia:
> - 🟢 **Observado** — visto directamente en una pantalla del mockup
> - 🟡 **Inferido** — necesario por lógica relacional, no visto pero requerido para que el sistema funcione
> - 🔵 **Del documento (no verificado)** — viene de `models.md` pero ninguna pantalla lo confirma ni lo contradice
> - 🔴 **Corregido** — `models.md` decía algo que el mockup contradice; se documenta el conflicto

---

## 0. Hallazgos de la auditoría (léelo antes de usar el modelo)

`models.md` es un documento real y detallado, pero como te advirtieron, tiene fallas:

| # | Problema | Evidencia |
|---|----------|-----------|
| 1 | **`AvailabilityException` no existe en el documento** — falta toda la entidad de excepciones de disponibilidad del instructor (incapacidad, capacitación, comisión) | Vista completa en 2 capturas: formulario + listado con aprobación |
| 2 | `TrackingSession` del doc simplifica a dos `%` calculados; el mockup usa **contadores crudos** (asistentes / total_aprendices) + tipo de sesión + bandera de seguimiento adicional | Captura "Registrar seguimiento": campos `Asistentes`, `Total de aprendices`, `Tipo de sesión`, `Requiere seguimiento adicional` |
| 3 | `SentNotification` del doc no tiene `priority` ni `notification_type` — el mockup los muestra explícitamente como badges | Panel de notificaciones: "Prioridad: Alta/Normal", tipo "Cambio de horario", "Horario publicado", etc. |
| 4 | `Learner.ficha_id` como columna simple no soporta `enrollment_status = TRANSFERRED` que el mismo documento define — se necesita historial | Contradicción interna del propio documento |
| 5 | El documento afirma "los binarios nunca se almacenan en la BD" pero no conecta `AvailabilityException.support_document` con `document-service` — hueco de integración | Formulario de excepción sube `.pdf` directo |
| 6 | Jerarquía territorial completa (Macroregión → Microregión → Departamento → Municipio con códigos DANE) y árbol curricular de 3 niveles (`TechLine/TechNetwork/KnowledgeNetwork`) — **ninguna pantalla del mockup los muestra** | No observado — se incluye como 🔵 opcional, no obligatorio |

El modelo que sigue prioriza lo 🟢 observado. Lo 🔵 se incluye al final como anexo opcional, no como núcleo.

---

## 1. Diagrama de dominios (bounded contexts observados)

```
IAM (usuarios, roles, permisos)
   │
   ├─→ ACTORS (instructores, aprendices)
   │        │
REFERENCE-DATA (centro, sede, catálogos)
   │        │
   ├─→ ACADEMIC-MANAGEMENT (programa, ficha, competencia)
   │        │
   ├─→ TRAINING-ENVIRONMENT (ambiente, mantenimiento)
   │        │
   ├─→ SCHEDULING (horario, sesión de clase, conflictos)
   │        │
   ├─→ AVAILABILITY (excepciones de disponibilidad del instructor)
   │        │
   ├─→ MONITORING (seguimiento de ficha, KPIs, alertas)
   │        │
   └─→ NOTIFICATION (notificaciones a usuarios)
```

---

## 2. IAM — Usuarios y control de acceso

### `User` 🟢
Observado en "Administración → Usuarios" y "Nuevo usuario".

| Atributo | Tipo | Notas |
|---|---|---|
| `id` | UUID PK | |
| `email` | VARCHAR(255) UNIQUE | dominio `@sena.edu.co` / `@soy.sena.edu.co` para aprendices |
| `password_hash` | TEXT | |
| `first_name` | VARCHAR(100) | campo "Nombre" separado, visto en form |
| `last_name` | VARCHAR(100) | campo "Apellido" separado |
| `actor_type` | ENUM(`INSTRUCTOR`,`LEARNER`,`STAFF`) | "Tipo de actor" en el form |
| `actor_id` | UUID NULLABLE | vínculo opcional a `Instructor`/`Learner` — "Actor vinculado (opcional)" |
| `is_active` | BOOLEAN | badge Activo/Inactivo en listado |
| `last_login_at` | TIMESTAMPTZ | columna "Último acceso" |
| `created_at` / `updated_at` | TIMESTAMPTZ | |

### `Role` 🟢
7 roles confirmados por landing + pantallas: **Coordinador Académico, Instructor, Aprendiz, Director de Centro, Soporte** (5 vistos), y 2 no capturados (probablemente Administrador de Sistema y Líder de Área, según `models.md`).

| Atributo | Tipo |
|---|---|
| `id` | UUID PK |
| `code` | VARCHAR(50) UNIQUE — ej. `COORDINATOR`, `INSTRUCTOR`, `LEARNER`, `DIRECTOR`, `SUPPORT` |
| `display_name` | VARCHAR(100) |
| `is_system_role` | BOOLEAN |

### `UserRole` 🟢
Tabla intermedia rica — **no** es un simple FK. Confirmado exacto en el modal "Asignar rol a Juan Pérez".

| Atributo | Tipo | Notas |
|---|---|---|
| `id` | UUID PK | |
| `user_id` | UUID FK → User | |
| `role_id` | UUID FK → Role | |
| `training_center_id` | UUID NULLABLE FK → TrainingCenter | "vacío = rol global" (texto literal del mockup) |
| `expires_at` | TIMESTAMPTZ NULLABLE | campo "Expira el (opcional)" |
| `reason` | TEXT | campo "Motivo de la asignación" |
| `assigned_by` | UUID FK → User | auditado: "queda auditado quién asigna, cuándo y por qué" |
| `assigned_at` | TIMESTAMPTZ | |

UNIQUE lógico: `(user_id, role_id, training_center_id)`.

### `Module` / `Feature` / `RoleFeature` 🔵
No observados directamente (el mockup no expone una pantalla de gestión de permisos granulares), pero son **necesarios** para explicar el comportamiento RBAC visto (guard 403, mensaje "acceso propio `SCH_VIEW_OWN`" en pantalla de Aprendiz). Se documenta el código de permiso `SCH_VIEW_OWN` como evidencia directa de que existe un catálogo de features tipo `MODULE_ACTION`.

| Entidad | Atributo clave | Evidencia |
|---|---|---|
| `Feature.code` | ej. `SCH_VIEW_OWN` | 🟢 **visto literalmente** en el mensaje de acceso del Aprendiz |
| `Module`, `RoleFeature` | estructura completa | 🔵 inferido — necesario para que `Feature.code` tenga sentido relacional |

### `UserSession` 🟢
Vista en pestaña "Sesiones" del detalle de usuario — **distinta** de `ClassSession` (ojo con la ambigüedad del nombre "sesión").

| Atributo | Tipo | Notas |
|---|---|---|
| `id` | UUID PK | |
| `user_id` | UUID FK → User | |
| `device_label` | VARCHAR(100) | "Chrome · Windows 11" |
| `ip_address` | VARCHAR(45) | |
| `created_at` | TIMESTAMPTZ | |
| `expires_at` | TIMESTAMPTZ | |
| `is_revoked` | BOOLEAN | acción "Revocar" en UI |

---

## 3. REFERENCE-DATA — Centro y catálogos

### `TrainingCenter` 🟢
Visto en "Mi centro".

| Atributo | Tipo |
|---|---|
| `id` | UUID PK |
| `center_code` | VARCHAR(10) UNIQUE — "9226" |
| `name` | VARCHAR(200) — "Centro de la Industria, la Empresa y los Servicios" |
| `address` | TEXT |
| `phone` | VARCHAR(20) |
| `is_active` | BOOLEAN |

### `Site` (Sede) 🟢
Visto en "Sedes y unidades institucionales" — relación 1:N con `TrainingCenter`.

| Atributo | Tipo | Notas |
|---|---|---|
| `id` | UUID PK | |
| `training_center_id` | UUID FK → TrainingCenter | |
| `name` | VARCHAR(200) | "Sede Centro", "Sede Prado Alto" |
| `site_type` | VARCHAR(50) | "Sede principal", "Unidad académica", "Ambiente externo" |
| `is_active` | BOOLEAN | |

### `Catalog` / `CatalogDetail` 🔵
Pestaña "Catálogos" existe en la UI pero no se capturó su contenido expandido. Se infiere estructura EAV estándar (agrupador + detalle) por ser patrón común y estar referenciado en `models.md`.

---

## 4. ACADEMIC-MANAGEMENT — Programas y fichas

### `TrainingProgram` 🟢
Confirmado: código + nombre visto en "Fichas" (ej. "233104 — Análisis y Desarrollo de Software") y en el detalle de horario.

| Atributo | Tipo |
|---|---|
| `id` | UUID PK |
| `program_code` | VARCHAR(20) UNIQUE — "233104" |
| `name` | VARCHAR(200) — "Análisis y Desarrollo de Software" |
| `training_level` | ENUM(`AUXILIARY`,`OPERATOR`,`TECHNICIAN`,`TECHNOLOGIST`) 🔵 no observado directamente |
| `is_active` | BOOLEAN |

### `Competency` 🟢
Visto como texto libre en selects de sesión ("Desarrollar software según requerimientos", "Modelar bases de datos") — confirma que es catálogo referenciable, no texto libre.

| Atributo | Tipo |
|---|---|
| `id` | UUID PK |
| `program_id` | UUID FK → TrainingProgram |
| `name` | VARCHAR(300) |
| `sena_code` | VARCHAR(20) 🔵 no visto pero estándar SENA |

### `EnrollmentFicha` (Ficha) 🟢
Entidad central — confirmada exhaustivamente en pantalla "Fichas" y "Detalle de sesión".

| Atributo | Tipo | Notas |
|---|---|---|
| `id` | UUID PK | |
| `ficha_number` | VARCHAR(20) UNIQUE | "2874412" |
| `program_id` | UUID FK → TrainingProgram | |
| `training_center_id` | UUID FK → TrainingCenter | |
| `status` | ENUM(`INDUCTION`,`EXECUTION`,`PRODUCTIVE_STAGE`,`COMPLETED`) | "Inducción/Ejecución/Etapa productiva/Finalizada" — **valores exactos confirmados** |
| `shift` | ENUM(`MORNING`,`AFTERNOON`,`NIGHT`,`MIXED`) | "Jornada": Diurna/Nocturna/Mixta |
| `modality` | ENUM(`ON_SITE`,`VIRTUAL`,`HYBRID`) | "Modalidad": Presencial/Híbrida |
| `max_capacity` | INTEGER | "Cupo máximo" |
| `start_date` | DATE | |
| `created_at` | TIMESTAMPTZ | |

🔴 **Corrección respecto al doc**: `models.md` define `learner_count` como entero fijo en `EnrollmentFicha`. El mockup (pantalla de Seguimiento) muestra asistencia como conteo variable por sesión (`Asistentes` vs `Total de aprendices`), lo cual solo tiene sentido si existe una tabla real de matrícula (`FichaEnrollment`) que vincula aprendices individuales a la ficha con fecha de ingreso/salida — no un contador estático.

### `FichaEnrollment` 🟡 (inferido, corrige el punto 4 de la auditoría)

| Atributo | Tipo | Notas |
|---|---|---|
| `id` | UUID PK | |
| `ficha_id` | UUID FK → EnrollmentFicha | |
| `learner_id` | UUID FK → Learner | |
| `enrolled_at` | DATE | |
| `status` | ENUM(`ACTIVE`,`DROPOUT`,`TRANSFERRED`,`COMPLETED`) | soporta transferencias reales entre fichas |
| `left_at` | DATE NULLABLE | |

---

## 5. TRAINING-ENVIRONMENT — Ambientes

### `Environment` (Ambiente) 🟢
Muy bien documentado en pantalla "Laboratorio A-204".

| Atributo | Tipo | Notas |
|---|---|---|
| `id` | UUID PK | |
| `training_center_id` | UUID FK → TrainingCenter | |
| `code` | VARCHAR(20) | "A-204", "B-105" |
| `name` | VARCHAR(100) | |
| `environment_type` | ENUM(`LAB`,`CLASSROOM`,`WORKSHOP`) | "Laboratorio", "Aula", "Taller" |
| `capacity` | INTEGER | "30 personas" |
| `location` | VARCHAR(200) | "Bloque A, piso 2" |
| `inspection_status` | ENUM(`CURRENT`,`EXPIRED`,`PENDING`) | "Estado de inspección: Vigente" |
| `last_inspection_at` | DATE | "última 20/07/2026" |
| `required_certification` | VARCHAR(100) NULLABLE | "Certificación requerida: Ninguna" |
| `is_active` | BOOLEAN | |

### `EnvironmentMaintenance` 🟢
Visto como "Mantenimiento programado: Jueves 13/08 · 13:00–16:00" bloqueando franjas.

| Atributo | Tipo |
|---|---|
| `id` | UUID PK |
| `environment_id` | UUID FK → Environment |
| `start_datetime` | TIMESTAMPTZ |
| `end_datetime` | TIMESTAMPTZ |
| `reason` | TEXT |
| `created_by` | UUID FK → User |

---

## 6. ACTORS — Instructores y aprendices

### `Instructor` 🟢 (parcial) + 🔵 (campos no vistos)

| Atributo | Tipo | Evidencia |
|---|---|---|
| `id` | UUID PK | |
| `user_id` | UUID FK → User | 🟢 vínculo confirmado ("Actor vinculado") |
| `full_name` | VARCHAR(200) | 🟢 "Juan Pérez" |
| `document_number` | VARCHAR(20) UNIQUE | 🔵 no visto, estándar |
| `max_hours_per_week` | DECIMAL(4,1) | 🔵 no visto directamente |
| `is_active` | BOOLEAN | |

### `InstructorCompetency` 🟡
Inferido: el select de "Instructor" al agregar sesión y la validación "instructor ya certificado" implican una relación instructor↔competencia, aunque no se vio una pantalla de gestión dedicada.

| Atributo | Tipo |
|---|---|
| `id` | UUID PK |
| `instructor_id` | UUID FK → Instructor |
| `competency_id` | UUID FK → Competency |

### `Learner` (Aprendiz) 🟢

| Atributo | Tipo | Evidencia |
|---|---|---|
| `id` | UUID PK | |
| `user_id` | UUID FK → User | 🟢 "Laura Martínez" vista como usuario con rol Aprendiz |
| `full_name` | VARCHAR(200) | 🟢 |
| `document_number` | VARCHAR(20) UNIQUE | 🔵 no visto |

---

## 7. SCHEDULING — Horarios y sesiones (núcleo del sistema)

### `Schedule` (Horario) 🟢
Exhaustivamente confirmado: "Nuevo horario", listado con estados, publicación.

| Atributo | Tipo | Notas |
|---|---|---|
| `id` | UUID PK | |
| `ficha_id` | UUID FK → EnrollmentFicha | |
| `period` | VARCHAR(10) | "2026-2" |
| `name` | VARCHAR(200) | "ADSO — Trimestre III" |
| `status` | ENUM(`DRAFT`,`UNDER_REVIEW`,`PUBLISHED`) | "Borrador/En revisión/Publicado" — **exacto** |
| `published_at` | TIMESTAMPTZ NULLABLE | |
| `published_by` | UUID FK → User | |
| `created_by` | UUID FK → User | |
| `updated_at` | TIMESTAMPTZ | columna "Última edición" |

**Invariante confirmada por UI**: "Al publicar, el horario será visible... y ya no admitirá cambios" y "Horario definitivo de solo lectura" — publicado = inmutable.

### `ClassSession` (Sesión de clase) 🟢
La entidad más ricamente confirmada — vista en 5+ pantallas distintas con los mismos campos exactos.

| Atributo | Tipo | Notas |
|---|---|---|
| `id` | UUID PK | |
| `schedule_id` | UUID FK → Schedule | |
| `ficha_id` | UUID FK → EnrollmentFicha | (denormalizado para queries, o derivado de schedule) |
| `competency_id` | UUID FK → Competency | select "Competencia" |
| `instructor_id` | UUID FK → Instructor | select "Instructor" |
| `environment_id` | UUID FK → Environment | select "Ambiente" |
| `day_of_week` | ENUM(`MON`..`FRI`) | columna "Día" |
| `start_time` / `end_time` | TIME | "Franja horaria" 07:00–10:00 |
| `session_date` | DATE | fecha concreta, no solo recurrente |
| `notes` | TEXT | "Traer equipo portátil..." |
| `status` | ENUM(`ACTIVE`,`CANCELLED`) | "Activa/Cancelada" |
| `execution_status` | ENUM(`PENDING`,`EXECUTED`,`NOT_EXECUTED`) | "Pendiente / Marcar ejecutada / No ejecutada" — **campo separado del status**, confirmado en "Detalle de sesión" |

### `SchedulingConflict` 🟢
Confirmado exacto en "Panel de conflictos" y modal "Resolver conflicto".

| Atributo | Tipo | Notas |
|---|---|---|
| `id` | UUID PK | |
| `schedule_id` | UUID FK → Schedule | |
| `conflict_type` | ENUM(`INSTRUCTOR_DOUBLE_BOOKED`,`ENVIRONMENT_DOUBLE_BOOKED`,`SESSIONS_OVERLAP`,`ENVIRONMENT_MAINTENANCE`) | 4º valor visto: "Ambiente en mantenimiento" (no estaba en `models.md`) |
| `description` | TEXT | |
| `session_a_id` | UUID FK → ClassSession | |
| `session_b_id` | UUID FK → ClassSession NULLABLE | |
| `severity` | ENUM(`LOW`,`MEDIUM`,`HIGH`) | "Severidad: Alta/Media" |
| `status` | ENUM(`PENDING`,`RESOLVED`) | |
| `blocks_publication` | BOOLEAN | "Bloquea publicación: Sí" |
| `detected_at` | TIMESTAMPTZ | |
| `resolution_note` | TEXT NULLABLE | "Justificación de la resolución" — **obligatoria, auditada** |
| `resolved_by` | UUID FK → User NULLABLE | |
| `resolved_at` | TIMESTAMPTZ NULLABLE | |

---

## 8. AVAILABILITY — Excepciones de disponibilidad ⚠️ Ausente del documento

### `AvailabilityException` 🟢
**Entidad completa que `models.md` omite por completo.** Confirmada con dos capturas: listado y formulario de creación.

| Atributo | Tipo | Notas |
|---|---|---|
| `id` | UUID PK | |
| `instructor_id` | UUID FK → Instructor | |
| `exception_type` | ENUM(`MEDICAL_LEAVE`,`TRAINING`,`COMMISSION`) | "Incapacidad médica / Capacitación / Comisión" |
| `start_datetime` | TIMESTAMPTZ | |
| `end_datetime` | TIMESTAMPTZ | validación: fin > inicio |
| `description` | TEXT NULLABLE | "Descripción (opcional)" |
| `support_document_id` | UUID FK → Document NULLABLE | obligatorio para incapacidad/comisión/capacitación según UI |
| `status` | ENUM(`PENDING_REVIEW`,`APPROVED`,`REJECTED`) | |
| `reviewed_by` | UUID FK → User NULLABLE | "Revisado por María García" |
| `reviewed_at` | TIMESTAMPTZ NULLABLE | |
| `review_comment` | TEXT NULLABLE | "falta adjuntar el soporte de la comisión" |
| `created_at` | TIMESTAMPTZ | |

---

## 9. MONITORING — Seguimiento de fichas e indicadores

### `FichaTracking` 🟢
Confirmado como agregado con estado calculado ("En riesgo" visible en cabecera de "Seguimiento de ficha").

| Atributo | Tipo |
|---|---|
| `id` | UUID PK |
| `ficha_id` | UUID FK → EnrollmentFicha |
| `status` | ENUM(`ON_TRACK`,`AT_RISK`,`CRITICAL`) |
| `last_tracking_at` | DATE |
| `next_tracking_at` | DATE |

### `TrackingRecord` 🟢 (🔴 corrige `TrackingSession` del doc)
El mockup usa **conteos crudos**, no porcentajes precalculados.

| Atributo | Tipo | Notas |
|---|---|---|
| `id` | UUID PK | |
| `ficha_tracking_id` | UUID FK → FichaTracking | |
| `session_type` | ENUM(`ACADEMIC`,`PROJECT`,`WELLBEING`) | "Académico / Proyecto / Bienestar" |
| `session_date` | DATE | |
| `attendees_count` | INTEGER | "Asistentes" — puede superar el total (ver nota UI) |
| `total_learners` | INTEGER | "Total de aprendices" |
| `curricular_progress_pct` | DECIMAL(5,2) | "Avance curricular (%)" |
| `observations` | TEXT NULLABLE | |
| `requires_followup` | BOOLEAN | "Requiere seguimiento adicional" |
| `created_by` | UUID FK → User | |

### `Kpi` / `KpiMeasurement` 🟢
Confirmado en "Panel de indicadores" y detalle "Ficha 2874412 — Asistencia".

| Entidad | Atributo | Tipo | Notas |
|---|---|---|---|
| `Kpi` | `id`, `code`, `name` | — | "Asistencia", "Avance curricular" — catálogo |
| `KpiMeasurement` | `id` | UUID PK | |
| | `ficha_id` | UUID FK → EnrollmentFicha | |
| | `kpi_id` | UUID FK → Kpi | |
| | `period` | VARCHAR(10) | "Ago 2026" |
| | `measured_at` | DATE | |
| | `value` | DECIMAL(5,2) | "76%" |
| | `threshold` | DECIMAL(5,2) | "80%" — umbral vigente en el momento de la medición |
| | `status` | ENUM(`ON_TRACK`,`AT_RISK`,`CRITICAL`) | |

---

## 10. NOTIFICATION — Notificaciones

### `Notification` 🟢 (🔴 corrige `SentNotification` del doc, que carecía de tipo/prioridad)

| Atributo | Tipo | Notas |
|---|---|---|
| `id` | UUID PK | |
| `recipient_user_id` | UUID FK → User | |
| `notification_type` | ENUM(`SCHEDULE_CHANGE`,`SCHEDULE_PUBLISHED`,`SESSION_CANCELLED`,`ACADEMIC_TRACKING`,`GENERAL`) | badges vistos: "Cambio de horario", "Horario publicado", "Seguimiento académico" |
| `title` | VARCHAR(200) | "Cambio de ambiente" |
| `body` | TEXT | |
| `priority` | ENUM(`NORMAL`,`HIGH`) | "Prioridad: Alta/Normal" — **confirmado, ausente del doc** |
| `read_state` | ENUM(`NEW`,`READ`) | badge "Nueva" |
| `delivery_status` | ENUM(`SENT`,`SENDING`,`FAILED`) | "Enviado / Enviando… / No se pudo entregar" |
| `related_entity_type` | VARCHAR(50) NULLABLE | ej. "ficha", "schedule" |
| `related_entity_id` | UUID NULLABLE | ej. ficha 2874412 |
| `created_at` | TIMESTAMPTZ | |

---

## 11. DOCUMENT — Documentos (soporte, no observado directamente pero requerido)

### `Document` 🟡
Requerido para que `AvailabilityException.support_document_id` funcione (se vio subir un `.pdf` en el formulario).

| Atributo | Tipo |
|---|---|
| `id` | UUID PK |
| `file_name` | VARCHAR(255) |
| `storage_key` | VARCHAR(500) |
| `mime_type` | VARCHAR(100) |
| `uploaded_by` | UUID FK → User |
| `uploaded_at` | TIMESTAMPTZ |

---

## 12. Relaciones clave (resumen ER)

```
TrainingCenter 1──* Site
TrainingCenter 1──* Environment
TrainingCenter 1──* EnrollmentFicha

TrainingProgram 1──* Competency
TrainingProgram 1──* EnrollmentFicha

EnrollmentFicha 1──* FichaEnrollment *──1 Learner
EnrollmentFicha 1──1 Schedule (por período)
EnrollmentFicha 1──1 FichaTracking

Schedule 1──* ClassSession
Schedule 1──* SchedulingConflict
ClassSession *──1 Instructor
ClassSession *──1 Environment
ClassSession *──1 Competency
SchedulingConflict *──2 ClassSession (session_a, session_b)

Instructor 1──* InstructorCompetency *──1 Competency
Instructor 1──* AvailabilityException
Instructor *──1 User

Environment 1──* EnvironmentMaintenance

FichaTracking 1──* TrackingRecord
FichaTracking 1──* KpiMeasurement *──1 Kpi

User 1──* UserRole *──1 Role
User 1──* UserSession
User 1──* Notification

AvailabilityException *──1 Document (support_document_id)
```

---

## 13. Anexo — Extensiones del documento no verificadas (🔵)

Estas partes de `models.md` son plausibles y coherentes con cómo opera realmente el SENA a nivel nacional, pero **ninguna pantalla del mockup las muestra**. Inclúyelas solo si tu alcance real necesita reportería nacional/regional:

- Jerarquía territorial completa: `Macroregion → Microregion → Department → Municipality` con códigos DANE
- Árbol curricular de 3 niveles antes de `TrainingProgram`: `TechLine → TechNetwork → KnowledgeNetwork`
- `ProductiveStage` / `Company` / `CompanyVisit` (etapa productiva en empresa) — coherente con el estado "Etapa productiva" visto en Fichas, pero sin pantalla propia capturada
- `AuditRecord` global inmutable (solo INSERT) — no se vio una pantalla de auditoría general, aunque sí se vieron varios campos individuales de auditoría (quién/cuándo/motivo) dentro de otras entidades
- `Status`/`Status_Category`/`Status_Transition` como sistema genérico de máquina de estados parametrizable — sobre-ingeniería posible; los estados vistos en el mockup funcionan bien como ENUM simple

---

## 14. Recomendación de siguiente paso

Si vas a generar el DDL, sugiero orden de creación por dependencias:
1. `TrainingCenter`, `Site`
2. `User`, `Role`, `UserRole`
3. `TrainingProgram`, `Competency`, `Instructor`, `Learner`
4. `Environment`, `EnvironmentMaintenance`
5. `EnrollmentFicha`, `FichaEnrollment`
6. `Schedule`, `ClassSession`, `SchedulingConflict`
7. `AvailabilityException`, `Document`
8. `FichaTracking`, `TrackingRecord`, `Kpi`, `KpiMeasurement`
9. `Notification`, `UserSession`

¿Quieres que convierta esto a DDL (PostgreSQL) o a un diagrama ER visual?
