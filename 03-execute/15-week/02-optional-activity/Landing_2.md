# Sistema de Gestión y Soporte a Incidencias (SGSI)

Integrantes

Jaminton Peña

Julian Camargo

Juan Horta

## Arquitectura del Proyecto

El Sistema de Gestión y Soporte a Incidencias (SGSI) se desarrolla bajo una arquitectura cliente-servidor, permitiendo separar la interfaz de usuario de la lógica de negocio y del almacenamiento de la información. Esta organización facilita el mantenimiento, la escalabilidad y la administración del sistema.

La comunicación entre el cliente y el servidor se realiza mediante solicitudes HTTP, permitiendo que los usuarios interactúen con el sistema desde un navegador web.

## Tecnologías Utilizadas

Durante el desarrollo del proyecto se emplean diferentes tecnologías para cada componente del sistema.

Frontend

Responsable de la interfaz gráfica y de la interacción con el usuario.

Backend

Encargado de procesar las solicitudes, aplicar las reglas del negocio y gestionar la información del sistema.

Base de Datos

Almacena la información relacionada con usuarios, incidencias, categorías, prioridades, estados, comentarios y reportes.

Herramientas de Desarrollo

Git y GitHub para el control de versiones.

Visual Studio Code como editor de código.

Draw.io para la elaboración de diagramas UML.

## Componentes del Sistema

Durante el análisis del proyecto se identificaron los siguientes componentes principales:

Gestión de Usuarios.

Gestión de Incidencias.

Gestión de Categorías.

Gestión de Prioridades.

Gestión de Estados.

Gestión de Comentarios.

Generación de Reportes.

Administración del Sistema.

## Organización del Proyecto

El proyecto se encuentra dividido en módulos independientes que permiten mantener una estructura organizada y facilitar el desarrollo de nuevas funcionalidades sin afectar el funcionamiento general del sistema.

Cada módulo cumple una responsabilidad específica y mantiene comunicación con los demás componentes cuando es necesario.

## Observaciones

La arquitectura propuesta permite que el SGSI sea un sistema modular, organizado y escalable.

La separación entre la interfaz, la lógica de negocio y la base de datos facilita el mantenimiento del software y la incorporación de nuevas funcionalidades durante futuras etapas del proyecto.
