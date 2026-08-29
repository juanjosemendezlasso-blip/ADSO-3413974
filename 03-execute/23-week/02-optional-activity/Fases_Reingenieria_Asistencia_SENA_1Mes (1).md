# Fases de Implementación: Reingeniería Arquitectónica del Sistema de Registro de Asistencia SENA (ADR-001)

## 1. Análisis Arquitectónico y Proposición del Sistema

El sistema actual está planteando una transición crítica (reingeniería) desde un **Prototipo de Cliente** basado en JavaScript con persistencia insegura y aislada (`localStorage`), hacia una **Arquitectura Web Monolítica por Capas**. 

**Proposiciones Fundamentales del Sistema:**
*   **Centralización:** Eliminar la dependencia del navegador del cliente y utilizar una Base de Datos Relacional Centralizada (MySQL), evidenciada por los archivos `database.sql` y `update-v4.sql`.
*   **Seguridad y Lógica en el Servidor:** Trasladar la lógica de negocio al Backend (PHP) para garantizar la integridad de los datos, gestionando el acceso mediante sesiones seguras (`sesion.php`, `login.php`, `logout.php`).
*   **Escalabilidad Proporcional:** Se ha evaluado y rechazado explícitamente el uso de Microservicios por considerarse sobrearquitectura (complejidad innecesaria para el alcance). El monolito es la opción ideal, sencilla y escalable para las entidades clave identificadas.

---

## 2. Fases de Implementación y Tiempos Estimados (Plan Acelerado)

Considerando las entidades clave (Usuario/Instructor, Ficha, Aprendiz, Sesión, Asistencia) y la estructura de archivos analizada, la implementación de esta plataforma se divide en 5 fases de ejecución rápida (sprints). 

**Tiempo Total Estimado:** 4 Semanas (1 Mes)

### FASE 1: Estructuración y Diseño Base (1 Semana)
El objetivo de esta fase es asentar los cimientos de la arquitectura monolítica por capas rápidamente.
*   **Diseño de Base de Datos:** Creación del modelo relacional final basado en `database.sql` para conectar *Fichas*, *Aprendices*, *Instructores* y *Asistencias*.
*   **Configuración del Entorno:** Establecimiento de la conexión a la base de datos (`config.php`) y configuración del servidor (`.htaccess`, `error_log`).
*   **Estructura de la Interfaz (Frontend Lógico):** Integración de los recursos estáticos y plantillas maestras (`layout.php`, recursos en `/assets/app.css` y `sena-logo.svg`).

### FASE 2: Sistema de Autenticación y Seguridad (Media Semana / 3-4 días)
Implementación ágil de los controles de acceso.
*   **Gestión de Sesiones:** Desarrollo del login, validación de credenciales y creación de variables de sesión (`sesion.php`, `cerrar_sesion.php`, `logout.php`).
*   **Políticas de Datos:** Implementación de las pantallas de consentimiento y tratamiento de información (`politica_datos.php`).
*   **Corrección de Flujos Base:** Aplicación de parches iniciales de acceso (ej. `fix-login.sql`).

### FASE 3: Desarrollo de Módulos de Entidades Clave (1 Semana)
Construcción intensiva de la capa de negocio para las entidades transaccionales.
*   **Módulo de Dashboard:** Creación de la vista principal e indicadores para el instructor (`dashboard.php`).
*   **Gestión de Aprendices y Fichas:** Desarrollo de las interfaces y operaciones (CRUD) para administrar los grupos y los estudiantes matriculados (`aprendices.php`).
*   **Preparación de Sesiones:** Lógica que relaciona una *Ficha* con un horario o *Sesión* específica.

### FASE 4: Núcleo de Operación - Registro de Asistencia (1 Semana)
La funcionalidad crítica de la plataforma desarrollada en un sprint enfocado.
*   **Generación e Integración de QR:** Implementación del sistema de códigos para automatizar el registro (`qr.php`, `codigo.php`).
*   **Lectura y Escaneo:** Desarrollo de la interfaz y la lógica para procesar el escaneo en tiempo real (`scanner.php`, `tomar_asistencia.php`).
*   **Contingencia Manual:** Creación de la vista para llamados a lista tradicionales (`registro_manual.php`, `registrar_asistencia.php`).
*   **Visualización de Registros:** Tabla de consolidado y reportes de inasistencias (`asistencias.php`).

### FASE 5: Pruebas, Ajustes y Despliegue (Media Semana / 3-4 días)
Cierre del proyecto y paso a producción.
*   **Pruebas de Integración:** Verificar que el flujo hacia la base de datos MySQL no presente fallos.
*   **Actualizaciones de Esquema:** Ejecución de scripts de mejora (`update-v4.sql`).
*   **Documentación Final:** Estructuración del manual de implementación en el archivo `README.md`.


---

## 3. Estrategia de Transición: Discovery y Patrón Higuera Estranguladora

Para asegurar una migración segura desde el prototipo actual hacia la nueva arquitectura, se implementará una estrategia conectando la fase de descubrimiento con el reemplazo incremental.

### Fase Previa: Discovery (Descubrimiento)
El proceso de transición inicia con el **Discovery**. Tal como investigaste, a nivel de red el "Service Discovery" permite que los servicios se detecten y comuniquen automáticamente sin configuración manual. Sin embargo, aplicándolo a nivel de proyecto, el "Discovery" es exactamente esa fase de evaluación y descripción exhaustiva del sistema ya realizado (el prototipo en JavaScript y `localStorage`). 
El propósito de esta fase es mapear todas las funcionalidades, rutas y dependencias actuales. **El Discovery finaliza en el punto exacto donde comienza la Higuera Estranguladora**, entregando el mapa detallado de lo que debe ser reemplazado.

### Ejecución: Patrón Higuera Estranguladora (Strangler Fig Pattern)
Con base en la documentación arquitectónica de Microsoft, este patrón consiste en migrar incrementalmente un sistema heredado (el sistema antiguo) reemplazando gradualmente partes específicas por nuevas aplicaciones y servicios.

*   **La Fachada (Intermediario):** Se introduce un enrutador o "fachada" que intercepta las peticiones de los usuarios. Al principio, dirige el tráfico al sistema heredado.
*   **Descomposición Incremental:** A medida que se desarrollan los módulos en PHP y MySQL (Fases 1 a 4), la fachada redirige progresivamente el tráfico hacia el nuevo sistema. Por ejemplo, se puede migrar primero el módulo de *Fichas* y *Aprendices*, mientras la *Asistencia* sigue temporalmente en el sistema antiguo.
*   **Retiro del Sistema Heredado:** Con cada iteración, el nuevo monolito asume más responsabilidades. Cuando toda la funcionalidad ha sido migrada, el sistema heredado original (el de `localStorage`) queda obsoleto, se desactiva y se retira completamente.

**Ventaja para el Proyecto:** Este enfoque reduce los riesgos de una migración abrupta. Permite que la plataforma de asistencia del SENA siga funcionando de cara al usuario mientras se reemplaza progresivamente la tecnología por debajo durante el mes de implementación.
