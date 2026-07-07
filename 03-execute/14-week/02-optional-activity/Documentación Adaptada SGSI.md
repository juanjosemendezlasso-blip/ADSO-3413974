# Documentación Adaptada del Proyecto SGSI

## Sistema de Gestión y Soporte a Incidencias (SGSI)

## Introducción

Como parte del proceso de desarrollo del software, se realizó la adaptación de la documentación trabajada en las actividades anteriores al proyecto definitivo denominado Sistema de Gestión y Soporte a Incidencias (SGSI).

El propósito de esta adaptación es orientar el análisis y diseño del sistema hacia una solución que permita gestionar de manera eficiente las incidencias reportadas por los usuarios, facilitando su seguimiento, atención y cierre mediante una plataforma centralizada.

## Descripción del Proyecto

El Sistema de Gestión y Soporte a Incidencias (SGSI) es una aplicación orientada a la administración de solicitudes de soporte técnico dentro de una organización.

El sistema permitirá registrar incidencias, clasificarlas según su categoría y prioridad, asignarlas al personal responsable, realizar seguimiento a cada caso y registrar su resolución. Además, proporcionará herramientas para la consulta de información y la generación de reportes que apoyen la toma de decisiones.

## Planteamiento del Problema

En muchas organizaciones, la gestión de incidencias continúa realizándose mediante llamadas telefónicas, correos electrónicos o aplicaciones de mensajería, dificultando el control y seguimiento de las solicitudes.

Esta situación ocasiona pérdida de información, retrasos en la atención, duplicidad de solicitudes y poca trazabilidad del proceso de soporte técnico.

El SGSI busca solucionar estos inconvenientes mediante un sistema que centralice toda la información relacionada con las incidencias y permita administrar cada caso de forma organizada.

## Objetivo General

Desarrollar un sistema de gestión y soporte a incidencias que permita registrar, administrar y controlar las solicitudes realizadas por los usuarios, facilitando su seguimiento hasta la resolución.

## Objetivos Específicos

- Registrar las incidencias reportadas por los usuarios.
- Administrar la información de usuarios y técnicos.
- Clasificar las incidencias según su categoría y prioridad.
- Realizar seguimiento al estado de cada incidencia.
- Mantener un historial de atención.
- Generar reportes para apoyar la gestión del servicio.

## Alcance

El sistema permitirá administrar las incidencias desde su creación hasta su cierre.

Dentro de sus funcionalidades principales se incluyen la autenticación de usuarios, el registro de incidencias, la asignación de responsables, la actualización de estados, la consulta del historial y la generación de reportes administrativos.

## Actores del Sistema

| Actor | Descripción |
|--------|-------------|
| Administrador | Configura el sistema y administra la información general. |
| Técnico | Atiende las incidencias asignadas y registra su solución. |
| Usuario | Reporta incidencias y consulta el estado de sus solicitudes. |

## Módulos del Sistema

| Código | Módulo |
|--------|---------------------------|
| M01 | Gestión de Usuarios |
| M02 | Gestión de Incidencias |
| M03 | Gestión de Categorías |
| M04 | Gestión de Prioridades |
| M05 | Gestión de Estados |
| M06 | Reportes |
| M07 | Configuración |

## Flujo General del Sistema

```text
Inicio de sesión
        │
        ▼
Registrar incidencia
        │
        ▼
Clasificar incidencia
        │
        ▼
Asignar responsable
        │
        ▼
Atender incidencia
        │
        ▼
Actualizar estado
        │
        ▼
Cerrar incidencia
        │
        ▼
Generar reporte
```

## Beneficios

La implementación del SGSI permitirá mejorar la administración del proceso de soporte técnico mediante la centralización de la información, reduciendo los tiempos de respuesta y facilitando el seguimiento de cada incidencia.

El sistema contribuirá a mejorar la organización de las actividades de soporte, mantener un historial de atención y generar información útil para la toma de decisiones.
