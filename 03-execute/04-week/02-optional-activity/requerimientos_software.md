# Levantamiento de Requerimientos de Software
### Supermercado Mediano · Sistema POS & Portal Web
**Versión 2.0 — 2025**

| Campo | Detalle |
|---|---|
| Tipo de Negocio | Supermercado Mediano |
| Empleados | 30 empleados (cajeros, inventario, administrador) |
| Sistemas a Desarrollar | Sistema POS · Portal Web (vista + carrito de compras) |
| Técnica de Elicitación | Encuesta estructurada (usuarios internos y externos) |

---

## 1. Alistamiento (Preparación para la Elicitación)

Esta fase define el marco estratégico de la encuesta antes de su aplicación, garantizando que cada pregunta entregue información accionable para la definición de requerimientos formales.

### 1.1 Objetivo General

Recopilar información precisa sobre los flujos de trabajo actuales del supermercado —ventas, facturación, control de inventario y manejo de promociones— para definir las funcionalidades exactas del Sistema POS y el diseño del Portal Web.

### 1.2 Alcance del Relevamiento

**Sistemas a levantar:**

- **Sistema POS:** registro de ventas, facturación electrónica/física, control de inventario en tiempo real, gestión de promociones automáticas y cuadre de caja.
- **Portal Web (Fase 1 – Vista):** catálogo de productos, banner de promociones activas, horario y ubicación.
- **Portal Web (Fase 2 – Carrito):** pre-cotización, reserva de productos y proyección a pedidos con domicilio.

### 1.3 Población Objetivo y Muestra

| Segmento | Descripción | Canal de Aplicación |
|---|---|---|
| Usuarios Internos | Cajeros, personal de inventario, administrador (muestra de los 30 empleados) | Encuesta digital (Google Forms) o impresa en sala de descanso |
| Usuarios Externos | Clientes frecuentes del supermercado | Google Forms enviado por WhatsApp o encuesta impresa en punto de pago |
| Tamaño de Muestra Mínimo | 15 empleados + 30 clientes | Suficiente para identificar patrones estadísticos |

### 1.4 Herramientas y Logística

- **Plataforma:** Google Forms (tabulación automática) o encuesta impresa en el punto de pago.
- **Tiempo estimado por encuesta:** 5-8 minutos.
- **Período de recolección:** 5 días hábiles.
- **Responsable:** Líder de proyecto / Analista de requerimientos.

### 1.5 Hipótesis Previas (Pre-elicitación)

- El mayor cuello de botella operativo es el cálculo manual de descuentos en periodos de promoción.
- Los clientes prefieren consultar promociones digitalmente antes de visitar la tienda.
- El carrito de compras será usado principalmente para cotización y reserva (no domicilio), al menos en la fase inicial.
- El sistema debe soportar hasta 15 usuarios simultáneos en horas pico.

---

## 2. Realización Conceptual (Diseño del Instrumento)

El cuestionario se estructura en dos secciones según el perfil del encuestado. Cada pregunta está vinculada explícitamente al requerimiento funcional o no funcional que busca validar.

### 2.1 Sección A — Operación Interna y Sistema POS
*(Dirigida a: Cajeros, Personal de Inventario y Administrador)*

| # | Pregunta | Opciones | Objetivo RF/RNF |
|---|---|---|---|
| A1 | ¿Cómo se registran actualmente las ventas e inventario en su turno? | Manualmente (papel/lápiz) · Hojas de cálculo (Excel) · Sistema o caja registradora antigua | RF01 – Registro de ventas con código de barras |
| A2 | ¿Con qué frecuencia ocurren descuadres de caja o errores al calcular precios en promoción? | Nunca · A veces · Frecuentemente | RF02 – Cálculo automático de descuentos / RNF01 – Rendimiento |
| A3 | Para el nuevo Sistema POS, ¿qué funcionalidades considera de ALTA prioridad? (máximo 3) | Lector de código de barras ágil · Aplicación automática de descuentos · Alerta de bajo inventario · Cierre y cuadre de caja rápido · Generación de facturas electrónicas/físicas | RF01, RF02, RF05, RF06, RF07 |
| A4 | ¿Cuántos puntos de caja/venta operan simultáneamente en hora pico? | 1-3 cajas · 4-6 cajas · 7 o más cajas | RNF04 – Concurrencia del sistema |
| A5 | ¿Existe actualmente un proceso formal para actualizar precios de productos en promoción? ¿Cuánto tiempo tarda? | Respuesta abierta | RF08 – Gestión de promociones en POS |

### 2.2 Sección B — Portal Web y Carrito de Compras
*(Dirigida a: Clientes Frecuentes y Administrador)*

| # | Pregunta | Opciones | Objetivo RF/RNF |
|---|---|---|---|
| B1 | Si el supermercado tuviera un portal web, ¿qué le gustaría ver principalmente? | Catálogo de productos y precios · Promociones y descuentos vigentes · Horario de atención y ubicación | RF03 – Módulo de catálogo y promociones web |
| B2 | ¿Cómo prefiere enterarse de las promociones del supermercado? | Redes sociales · Página web del supermercado · Folletos físicos en tienda | RF03 – Banner de promociones activas |
| B3 | Si habilitamos un Carrito de Compras en la web, ¿para qué lo usaría? | Pedir domicilio · Reservar y recoger en tienda · Solo cotizar el valor del mercado · No lo usaría, prefiero ir físicamente | RF04 – Carrito de cotización/reserva |
| B4 | ¿Desde qué dispositivo accedería mayormente al portal web? | Celular (Android/iOS) · Computador de escritorio · Tableta | RNF02 – Responsividad y adaptación móvil |
| B5 | ¿Qué problemas nota cuando hay promociones fuertes en el supermercado? | Respuesta abierta | RF02, RF03, RF04 – Identificación de cuellos de botella |

---

## 3. Realización Práctica (Análisis y Extracción de Requerimientos)

Con base en los resultados simulados de la encuesta aplicada a 15 empleados y 30 clientes, se presenta el análisis de datos y la traducción formal a requerimientos de software.

### 3.1 Análisis de Resultados (Simulación de Datos)

| Hallazgo Principal | Dato Cuantitativo | Implicación de Diseño |
|---|---|---|
| Errores al calcular promociones manualmente | 80% de los empleados lo reportan como problema frecuente | El POS debe automatizar todas las reglas de descuento |
| Uso esperado del carrito de compras | 65% optaría por cotización o reserva y recogida en tienda | Fase 1: carrito de cotización/reserva (sin domicilio complejo) |
| Acceso al portal web desde móvil | 90% de clientes accedería desde celular | La interfaz web debe ser mobile-first y responsiva |
| Puntos de venta simultáneos | Entre 10-15 usuarios operando en hora pico | El sistema debe soportar mínimo 15 sesiones concurrentes |
| Interés en ver promociones digitales | 70% de clientes prefiere web o redes sociales sobre folletos físicos | El portal web debe destacar promociones con banner actualizable |

### 3.2 Requerimientos Funcionales (RF)

| Código | Descripción | Prioridad | Módulo |
|---|---|---|---|
| RF01 | El sistema POS debe permitir el registro de ventas mediante lectura de códigos de barras, con tiempo de respuesta menor a 1 segundo por producto. | Alta | POS – Ventas |
| RF02 | El sistema POS debe calcular y aplicar automáticamente los descuentos vigentes cuando un producto esté marcado en promoción, sin intervención manual del cajero. | Alta | POS – Promociones |
| RF03 | El Portal Web debe contar con un módulo de catálogo de productos con precios y un banner destacado de promociones activas, actualizable por el administrador. | Alta | Web – Catálogo |
| RF04 | El Portal Web debe incluir un Carrito de Compras que permita al cliente agregar productos, generar una pre-cotización o reserva de pedido para recoger en tienda. | Alta | Web – Carrito |
| RF05 | El sistema POS debe generar alertas automáticas cuando el stock de un producto caiga por debajo del umbral mínimo configurado. | Media | POS – Inventario |
| RF06 | El sistema POS debe generar facturas electrónicas (y físicas) cumpliendo la normativa vigente, con descarga en PDF y envío por correo opcional. | Alta | POS – Facturación |
| RF07 | El sistema POS debe permitir el cierre y cuadre de caja por turno, generando un reporte de diferencias entre ventas registradas y efectivo físico. | Alta | POS – Caja |
| RF08 | El administrador debe poder crear, editar y desactivar promociones (porcentaje, valor fijo, 2x1) con fechas de inicio y fin, desde un panel de gestión. | Alta | Admin – Promociones |
| RF09 | El sistema debe gestionar roles y permisos diferenciados: Cajero, Operario de Inventario y Administrador/Gerente. | Alta | Admin – Seguridad |
| RF10 | El Portal Web debe mostrar el estado de disponibilidad de los productos (disponible / agotado) sincronizado con el inventario del POS en tiempo cercano al real. | Media | Web – Inventario |

### 3.3 Requerimientos No Funcionales (RNF)

| Código | Categoría | Descripción | Métrica |
|---|---|---|---|
| RNF01 | Rendimiento | El sistema POS debe registrar cada producto escaneado y actualizar el subtotal en menos de 1 segundo, incluyendo la validación de promociones aplicables. | < 1 segundo por operación de registro |
| RNF02 | Usabilidad / Responsividad | El Portal Web debe ser mobile-first y renderizarse correctamente en pantallas desde 320px de ancho. Mínimo 90% de funciones accesibles desde móvil. | Compatible con Chrome/Safari móvil; pruebas en dispositivos 320px–1920px |
| RNF03 | Seguridad | El sistema debe autenticar a los 30 empleados mediante usuario y contraseña. El acceso a módulos administrativos requiere contraseña robusta y sesiones con timeout. | Autenticación basada en roles; sesiones con timeout de 30 min por inactividad |
| RNF04 | Concurrencia | El sistema POS debe soportar al menos 15 sesiones simultáneas de usuarios (cajeros + inventario) sin degradación del rendimiento. | 15 usuarios concurrentes sin caída de rendimiento > 10% |
| RNF05 | Disponibilidad | El sistema POS debe garantizar disponibilidad del 99.5% en horario de operación. El portal web debe estar disponible 24/7. | SLA: 99.5% POS (horario comercial); 99.9% portal web |
| RNF06 | Escalabilidad | La arquitectura del portal web debe permitir habilitar el módulo de domicilios en una futura fase sin rediseño estructural. | Diseño modular con APIs REST desacopladas |
| RNF07 | Mantenibilidad | El código fuente debe seguir estándares documentados, con cobertura mínima de pruebas del 70% en módulos críticos (POS, inventario). | Cobertura de pruebas >= 70%; documentación técnica entregada |

---

## 4. Conclusiones y Próximos Pasos

El proceso de alistamiento, diseño conceptual del instrumento y análisis práctico de los resultados permite definir con precisión el alcance del software requerido. Los principales hallazgos son:

1. **El Sistema POS es el componente de mayor criticidad operativa:** la automatización de promociones (RF02) y el cuadre de caja (RF07) son las funcionalidades con mayor impacto inmediato en la operación de los 30 empleados.
2. **El Portal Web iniciará como vitrina de productos y promociones (RF03),** con el carrito en modo cotización/reserva (RF04). El domicilio completo queda proyectado para una segunda fase, lo que define RNF06 (escalabilidad modular).
3. **La responsividad móvil (RNF02) es innegociable** dado que el 90% de los clientes accede desde celular.
4. **La gestión centralizada de promociones (RF08)** debe unificar tanto la lógica del POS como la visualización en el portal web, evitando la doble actualización manual que actualmente genera errores.

### Próximos Pasos Recomendados

- Aplicar la encuesta real a la muestra definida (15 empleados + 30 clientes) y tabular los resultados.
- Realizar sesiones de validación de requerimientos con el administrador del supermercado.
- Elaborar el Documento de Especificación de Requerimientos de Software (ERS / SRS) formal.
- Definir la arquitectura técnica del POS (local vs. cloud) y del portal web (CMS vs. desarrollo a medida).
- Construir prototipos de baja fidelidad para validar la interfaz del carrito y del módulo de promociones con usuarios reales.

---

> **Nota metodológica:** Los datos cuantitativos presentados en la Sección 3.1 corresponden a una simulación representativa. Los valores definitivos deben reemplazarse con los resultados reales una vez aplicada la encuesta.
