# Requisitos de Actualizar-Linux v2.0

## 1. Propósito

Este documento define los requisitos funcionales y no funcionales de Actualizar-Linux v2.0.

Los requisitos describen **qué debe proporcionar el sistema**, sin definir necesariamente la tecnología o implementación utilizada para conseguirlo.

---

## 2. Alcance

Actualizar-Linux v2.0 es una aplicación de escritorio orientada al mantenimiento básico de sistemas Linux basados en Debian y Ubuntu mediante el sistema de gestión de paquetes APT.

La versión inicial contempla las siguientes operaciones:

- Actualización de los índices de paquetes.
- Actualización de paquetes instalados.
- Eliminación de paquetes que ya no son necesarios.
- Limpieza de la caché de paquetes.
- Visualización del resultado de las operaciones.
- Gestión de privilegios administrativos cuando sean necesarios.
- Registro de eventos relevantes.
- Instalación y distribución como aplicación de escritorio.

---

## 3. Sistemas objetivo

La primera versión estará orientada a sistemas que utilicen APT.

Los sistemas objetivo iniciales son:

- Debian.
- Ubuntu.
- Linux Mint.

También podrá funcionar en otras distribuciones compatibles con APT, siempre que sean validadas mediante pruebas.

La compatibilidad con una distribución no se considerará garantizada únicamente por utilizar APT.

---

# 4. Requisitos funcionales

## RF-01 — Actualización de repositorios

El sistema deberá permitir actualizar los índices locales de paquetes utilizando el mecanismo de gestión de paquetes del sistema.

La operación deberá informar al usuario si finalizó correctamente o si se produjo un error.

---

## RF-02 — Actualización de paquetes

El sistema deberá permitir actualizar los paquetes instalados mediante el mecanismo de gestión de paquetes del sistema.

La operación deberá informar al usuario del resultado de la ejecución.

---

## RF-03 — Actualización completa

El sistema deberá permitir ejecutar una actualización completa del sistema mediante una secuencia de operaciones definida por las reglas de negocio.

La actualización completa deberá respetar las dependencias entre sus operaciones.

---

## RF-04 — Eliminación de paquetes innecesarios

El sistema deberá permitir ejecutar una operación equivalente a `autoremove` para que APT determine los paquetes que ya no son necesarios.

La aplicación no deberá determinar por sí misma qué paquetes deben eliminarse.

---

## RF-05 — Limpieza de caché

El sistema deberá permitir ejecutar una operación equivalente a `clean` para limpiar la caché de paquetes gestionada por APT.

---

## RF-06 — Operaciones independientes

Las operaciones individuales de mantenimiento deberán poder ejecutarse de forma independiente cuando su naturaleza lo permita.

Como mínimo, deberán poder ejecutarse de forma independiente:

- Actualización de repositorios.
- Actualización de paquetes.
- Eliminación de paquetes innecesarios.
- Limpieza de caché.

---

## RF-07 — Interfaz gráfica

El sistema deberá proporcionar una interfaz gráfica de escritorio que permita al usuario:

- Iniciar las operaciones disponibles.
- Visualizar el estado de una operación.
- Visualizar información relevante de la ejecución.
- Identificar si una operación terminó correctamente, falló o fue cancelada.

---

## RF-08 — Gestión de privilegios

El sistema deberá solicitar privilegios administrativos únicamente cuando una operación los requiera.

El usuario deberá poder identificar cuándo el sistema requiere autorización para continuar.

---

## RF-09 — Cancelación de autorización

Si el usuario rechaza o cancela una solicitud de autorización administrativa, el sistema deberá informar que la operación no fue ejecutada.

La cancelación de una solicitud de autorización deberá distinguirse de un error interno de la aplicación.

---

## RF-10 — Manejo de errores

El sistema deberá detectar y comunicar, como mínimo, las siguientes situaciones:

- APT no disponible.
- Error durante la ejecución de una operación.
- Fallo al obtener privilegios administrativos.
- Cancelación de la autorización.
- Error inesperado durante la ejecución.

Los errores deberán comunicarse sin provocar el cierre inesperado de la aplicación.

---

## RF-11 — Estado de las operaciones

El sistema deberá proporcionar información sobre el estado de las operaciones en ejecución.

Como mínimo, deberá distinguir entre:

- Pendiente.
- En ejecución.
- Completada correctamente.
- Fallida.
- Cancelada.

---

## RF-12 — Visualización de salida

El sistema deberá permitir al usuario visualizar información relevante producida durante la ejecución de las operaciones de mantenimiento.

La salida deberá utilizarse principalmente para proporcionar transparencia sobre lo que está realizando el sistema.

---

## RF-13 — Ejecución no bloqueante

Las operaciones que puedan tardar un tiempo significativo deberán ejecutarse sin bloquear la interfaz gráfica.

El usuario deberá poder observar el estado de la operación mientras esta se encuentra en ejecución.

---

## RF-14 — Registro de eventos

El sistema deberá registrar eventos relevantes de la ejecución, incluyendo:

- Inicio de operaciones.
- Finalización de operaciones.
- Resultados.
- Errores.
- Cancelaciones.

El registro deberá respetar las reglas de seguridad definidas para el proyecto.

---

## RF-15 — Distribución mediante paquete

El sistema deberá poder distribuirse mediante un paquete compatible con sistemas Debian/Ubuntu.

El formato inicial de distribución será `.deb`.

---

## RF-16 — Instalación como aplicación

La instalación del paquete deberá proporcionar los elementos necesarios para utilizar Actualizar-Linux como una aplicación de escritorio.

Esto incluye, según corresponda:

- Archivos de aplicación.
- Dependencias necesarias.
- Lanzador de escritorio.
- Icono de aplicación.

---

## RF-17 — Lanzamiento desde el escritorio

Después de una instalación correcta, el usuario deberá poder iniciar la aplicación mediante el mecanismo de lanzamiento proporcionado por el entorno de escritorio.

---

## RF-18 — Desinstalación

El sistema deberá poder desinstalarse mediante los mecanismos habituales de gestión de paquetes del sistema.

La desinstalación no deberá eliminar archivos personales del usuario que no pertenezcan a la aplicación.

---

# 5. Requisitos no funcionales

## RNF-01 — Mantenibilidad

El código deberá estar organizado de manera modular, con responsabilidades claramente separadas.

La arquitectura deberá facilitar la modificación o sustitución de componentes sin afectar innecesariamente al resto del sistema.

---

## RNF-02 — Testabilidad

Los componentes principales deberán poder probarse de forma automatizada.

La arquitectura deberá permitir aislar dependencias externas como:

- APT.
- Procesos del sistema.
- Gestión de privilegios.
- Sistema de archivos cuando sea necesario.

---

## RNF-03 — Calidad del código

El proyecto deberá utilizar herramientas automatizadas para mantener estándares de calidad de código.

Como mínimo se contemplan:

- Linting.
- Formateo.
- Comprobación de tipos.

---

## RNF-04 — Estabilidad

Los errores durante una operación no deberán provocar el cierre inesperado de la aplicación.

La aplicación deberá mantener un estado coherente después de una operación exitosa, fallida o cancelada.

---

## RNF-05 — Rendimiento

La interfaz gráfica deberá mantenerse receptiva durante las operaciones de mantenimiento.

Las operaciones externas de larga duración no deberán ejecutarse directamente en el hilo principal de la interfaz.

---

## RNF-06 — Usabilidad

Las operaciones disponibles deberán ser comprensibles para un usuario con conocimientos básicos de administración de un sistema Linux.

Los estados, errores y resultados deberán presentarse mediante mensajes claros.

---

## RNF-07 — Transparencia

El sistema deberá proporcionar suficiente información para que el usuario pueda conocer:

- Qué operación se está ejecutando.
- Si requiere autorización.
- Si la operación está en progreso.
- Si terminó correctamente.
- Si falló.
- Si fue cancelada.

---

## RNF-08 — Seguridad

El sistema deberá minimizar la ejecución de operaciones con privilegios administrativos y evitar comportamientos que permitan ejecutar comandos arbitrarios desde la interfaz de usuario.

Los detalles concretos de implementación de estas medidas se definirán en las decisiones técnicas y reglas de negocio correspondientes.

---

## RNF-09 — Distribución

La aplicación deberá poder instalarse como software de escritorio sin exigir al usuario final conocimientos sobre:

- Python.
- `pip`.
- Entornos virtuales.
- Clonación del repositorio.
- Ejecución manual del código fuente.

Los detalles de empaquetado se definirán durante la fase de distribución.

---

## RNF-10 — Compatibilidad

El sistema deberá desarrollarse teniendo como objetivo los sistemas APT definidos en el alcance.

Las versiones y distribuciones concretas soportadas deberán validarse mediante pruebas antes de declararse oficialmente compatibles.

---

# 6. Restricciones

La versión inicial estará limitada a sistemas que utilicen APT.

No forman parte del alcance inicial:

- `dnf`.
- `pacman`.
- `zypper`.
- Administración remota.
- Servicios en la nube.
- API o servidor backend.
- Telemetría.
- Sistema de cuentas.
- Sistema de plugins.
- Actualización automática de la propia aplicación.
- Repositorio propio de paquetes.
- Funciones avanzadas de administración de paquetes.
- Funciones de ciberseguridad.

---

# 7. Criterios generales de aceptación

La versión inicial podrá considerarse funcionalmente completa cuando:

1. Las operaciones principales de mantenimiento puedan ejecutarse desde la interfaz gráfica.
2. Las operaciones que requieren privilegios puedan solicitar autorización correctamente.
3. La aplicación gestione correctamente ejecuciones exitosas, errores y cancelaciones.
4. La interfaz permanezca receptiva durante operaciones de larga duración.
5. La salida relevante de APT pueda ser consultada por el usuario.
6. Los eventos importantes queden registrados.
7. Los componentes principales dispongan de pruebas automatizadas.
8. La aplicación pueda distribuirse mediante un paquete `.deb`.
9. La instalación permita ejecutar la aplicación como software de escritorio.
10. La desinstalación pueda realizarse sin eliminar archivos personales del usuario.