# Diagrama de Clases del Sistema de Gestión y Soporte a Incidencias (SGSI)

Integrantes

Jaminton Peña

Julian Camargo

Juan Horta

## Introducción

El diagrama de clases es una representación gráfica de la estructura del Sistema de Gestión y Soporte a Incidencias (SGSI). Mediante este modelo se identifican las principales entidades del sistema, sus atributos, métodos y las relaciones existentes entre ellas.

Este diagrama constituye una base para comprender la organización del sistema antes de iniciar la etapa de desarrollo, facilitando el análisis y la comunicación entre los integrantes del proyecto.

## Objetivo

Representar la estructura del SGSI mediante un diagrama de clases que permita identificar las entidades principales, sus responsabilidades y las relaciones que existen entre ellas.

## Descripción del Diagrama

El modelo propuesto está conformado por siete clases principales: Rol, Usuario, Categoría, Prioridad, Estado, Incidencia y Comentario.

Cada una representa un elemento fundamental dentro del funcionamiento del sistema. La clase Incidencia constituye el núcleo del modelo, ya que almacena la información relacionada con los reportes registrados por los usuarios y mantiene relación con las demás entidades.

Las clases Categoría, Prioridad y Estado permiten clasificar y administrar cada incidencia, mientras que la clase Comentario registra las observaciones realizadas durante el proceso de atención. Por su parte, la clase Rol define los permisos de los usuarios dentro del sistema.

## Clases Identificadas

### Rol

Representa los diferentes perfiles que pueden existir dentro del sistema, permitiendo asignar permisos y responsabilidades a los usuarios.

### Usuario

Almacena la información de las personas que utilizan el sistema y permite registrar las incidencias correspondientes.

### Categoría

Organiza las incidencias según el tipo de problema reportado, facilitando su clasificación.

### Prioridad

Define el nivel de importancia de una incidencia, permitiendo establecer el orden de atención.

### Estado

Representa la situación actual de una incidencia durante su ciclo de vida, por ejemplo: abierta, en proceso o cerrada.

### Incidencia

Es la entidad principal del sistema. Contiene toda la información relacionada con los problemas reportados por los usuarios y mantiene la relación con las demás clases.

### Comentario

Permite registrar observaciones, avances o anotaciones realizadas durante la atención de una incidencia.

## Relaciones entre las Clases

Las clases del SGSI mantienen relaciones que permiten representar el funcionamiento del sistema de manera organizada.

Un rol puede ser asignado a varios usuarios. Cada usuario puede registrar múltiples incidencias. Las incidencias se clasifican mediante una categoría, una prioridad y un estado. Además, cada incidencia puede contener diferentes comentarios asociados al proceso de atención.

Estas relaciones permiten mantener organizada la información y facilitan el seguimiento de cada incidencia desde su creación hasta su cierre.
