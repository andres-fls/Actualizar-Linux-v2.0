# Casos de uso de Actualizar-Linux v2.0

## 1. Propósito

Este documento describe las principales interacciones entre los actores y Actualizar-Linux v2.0.

Los casos de uso representan funcionalidades que proporcionan un resultado observable para un actor.

Los errores, cancelaciones y situaciones excepcionales se documentan como flujos alternativos o de excepción dentro de los casos de uso correspondientes.

---

# 2. Actores

## 2.1 Usuario

Persona que utiliza Actualizar-Linux para realizar operaciones de mantenimiento del sistema.

---

## 2.2 Sistema operativo

Sistema Linux sobre el que se ejecuta Actualizar-Linux.

Proporciona recursos como:

- Procesos del sistema.
- Sistema de archivos.
- Gestión de privilegios.
- Entorno de escritorio.

---

## 2.3 APT

Sistema de gestión de paquetes utilizado por la distribución Linux.

APT ejecuta las operaciones relacionadas con:

- Actualización de índices.
- Actualización de paquetes.
- Eliminación de paquetes innecesarios.
- Limpieza de caché.

---

## 2.4 Mecanismo de privilegios

Componente del sistema operativo encargado de solicitar y conceder privilegios administrativos cuando una operación los requiere.

La implementación concreta se define en las decisiones técnicas del proyecto.

---

# 3. Clasificación de casos de uso

Los casos de uso se agrupan en:

### Operaciones de mantenimiento

- CU-01 — Actualizar repositorios.
- CU-02 — Actualizar paquetes.
- CU-03 — Realizar actualización completa.
- CU-04 — Eliminar paquetes innecesarios.
- CU-05 — Limpiar caché.

### Consulta de operaciones

- CU-06 — Consultar estado de una operación.
- CU-07 — Consultar resultado de una operación.

### Instalación y ciclo de vida

- CU-08 — Instalar aplicación.
- CU-09 — Ejecutar aplicación instalada.
- CU-10 — Desinstalar aplicación.

---

# 4. CU-01 — Actualizar repositorios

## Objetivo

Actualizar los índices locales de paquetes mediante APT.

## Actor principal

Usuario.

## Actores secundarios

- APT.
- Sistema operativo.
- Mecanismo de privilegios, si es necesario.

## Precondiciones

- La aplicación está ejecutándose.
- El sistema utiliza APT.
- APT está disponible.

## Flujo principal

1. El usuario selecciona la opción para actualizar los repositorios.
2. El sistema inicia la operación.
3. El sistema determina si necesita privilegios administrativos.
4. Si son necesarios, solicita autorización.
5. El sistema ejecuta la operación correspondiente mediante APT.
6. El sistema recibe el resultado de la ejecución.
7. El sistema registra el resultado.
8. La aplicación informa al usuario del resultado.

## Flujos alternativos y de excepción

### A1 — El usuario cancela la autorización

1. El mecanismo de privilegios solicita autorización.
2. El usuario rechaza o cancela la solicitud.
3. La operación no se ejecuta.
4. La operación finaliza con estado `CANCELLED`.
5. El sistema informa al usuario.

### A2 — Fallo al obtener privilegios

1. El sistema intenta obtener privilegios.
2. El mecanismo de privilegios devuelve un error.
3. La operación no se ejecuta.
4. La operación finaliza con estado `FAILED`.
5. El sistema registra el error.
6. El sistema informa al usuario.

### A3 — APT no está disponible

1. El sistema detecta que APT no está disponible.
2. La operación no se ejecuta.
3. La operación finaliza con estado `FAILED`.
4. El sistema informa al usuario.

### A4 — APT devuelve un error

1. APT ejecuta la operación.
2. APT devuelve un resultado de error.
3. El sistema interpreta el resultado.
4. La operación finaliza con estado `FAILED`.
5. El sistema muestra información relevante al usuario.

### A5 — Error inesperado

1. Se produce un error no previsto durante la operación.
2. El sistema registra información suficiente para diagnosticarlo.
3. La operación finaliza con estado `FAILED`.
4. La aplicación continúa funcionando siempre que sea posible.

## Postcondiciones

La operación queda registrada con su estado y resultado correspondiente.

---

# 5. CU-02 — Actualizar paquetes

## Objetivo

Actualizar los paquetes instalados mediante APT.

## Actor principal

Usuario.

## Actores secundarios

- APT.
- Sistema operativo.
- Mecanismo de privilegios.

## Precondiciones

- La aplicación está ejecutándose.
- APT está disponible.
- El sistema permite realizar la operación.

## Flujo principal

1. El usuario selecciona la opción para actualizar paquetes.
2. El sistema inicia la operación.
3. El sistema solicita privilegios administrativos si son necesarios.
4. El sistema ejecuta la operación mediante APT.
5. El sistema recibe el resultado.
6. El sistema registra el resultado.
7. La aplicación informa al usuario.

## Flujos alternativos y de excepción

### A1 — Autorización cancelada

La operación no se ejecuta y finaliza como `CANCELLED`.

### A2 — Fallo de privilegios

La operación no se ejecuta y finaliza como `FAILED`.

### A3 — Error de APT

La operación finaliza como `FAILED` y se informa al usuario.

### A4 — Error inesperado

La operación finaliza como `FAILED` y se registra el error.

## Postcondiciones

La operación queda registrada con su estado y resultado.

---

# 6. CU-03 — Realizar actualización completa

## Objetivo

Actualizar el sistema mediante una secuencia completa de mantenimiento.

## Actor principal

Usuario.

## Actores secundarios

- APT.
- Sistema operativo.
- Mecanismo de privilegios.

## Precondiciones

- La aplicación está ejecutándose.
- APT está disponible.
- El sistema permite realizar operaciones administrativas.

## Flujo principal

1. El usuario selecciona la actualización completa.
2. El sistema inicia la operación.
3. El sistema solicita privilegios administrativos cuando sean necesarios.
4. El sistema actualiza los índices de paquetes.
5. El sistema verifica el resultado de la actualización de índices.
6. Si la primera operación finaliza correctamente, el sistema inicia la actualización de paquetes.
7. El sistema verifica el resultado de la actualización.
8. El sistema registra el resultado final.
9. La aplicación informa al usuario.

## Flujos alternativos y de excepción

### A1 — Autorización cancelada

1. El usuario cancela la solicitud de autorización.
2. La actualización no se ejecuta.
3. La operación finaliza como `CANCELLED`.

### A2 — Fallo durante la actualización de repositorios

1. La actualización de índices falla.
2. El sistema no inicia la actualización de paquetes.
3. La operación finaliza como `FAILED`.
4. El sistema informa al usuario.

### A3 — Fallo durante la actualización de paquetes

1. La actualización de índices finaliza correctamente.
2. La actualización de paquetes falla.
3. La operación finaliza como `FAILED`.
4. El sistema informa al usuario.

### A4 — APT no disponible

La operación no comienza y finaliza como `FAILED`.

### A5 — Error inesperado

La operación finaliza como `FAILED` y el error se registra.

## Postcondiciones

El resultado de cada etapa queda disponible para el sistema y el resultado final queda registrado.

---

# 7. CU-04 — Eliminar paquetes innecesarios

## Objetivo

Ejecutar una operación equivalente a `apt-get autoremove`.

## Actor principal

Usuario.

## Actores secundarios

- APT.
- Sistema operativo.
- Mecanismo de privilegios.

## Precondiciones

- La aplicación está ejecutándose.
- APT está disponible.

## Flujo principal

1. El usuario selecciona la opción para eliminar paquetes innecesarios.
2. El sistema inicia la operación.
3. El sistema solicita privilegios si son necesarios.
4. APT determina los paquetes que pueden eliminarse.
5. APT ejecuta la operación.
6. El sistema recibe el resultado.
7. El sistema registra la operación.
8. La aplicación informa al usuario.

## Flujos alternativos y de excepción

### A1 — Autorización cancelada

La operación no se ejecuta y finaliza como `CANCELLED`.

### A2 — Error de APT

La operación finaliza como `FAILED`.

### A3 — APT no disponible

La operación no se ejecuta y finaliza como `FAILED`.

### A4 — Error inesperado

La operación finaliza como `FAILED`.

## Postcondiciones

El resultado de la operación queda registrado.

---

# 8. CU-05 — Limpiar caché

## Objetivo

Limpiar la caché de paquetes mediante APT.

## Actor principal

Usuario.

## Actores secundarios

- APT.
- Sistema operativo.
- Mecanismo de privilegios.

## Precondiciones

- La aplicación está ejecutándose.
- APT está disponible.

## Flujo principal

1. El usuario selecciona la opción para limpiar la caché.
2. El sistema inicia la operación.
3. El sistema solicita privilegios si son necesarios.
4. APT ejecuta la operación de limpieza.
5. El sistema recibe el resultado.
6. El sistema registra la operación.
7. La aplicación informa al usuario.

## Flujos alternativos y de excepción

### A1 — Autorización cancelada

La operación no se ejecuta y finaliza como `CANCELLED`.

### A2 — Error de APT

La operación finaliza como `FAILED`.

### A3 — APT no disponible

La operación no se ejecuta y finaliza como `FAILED`.

### A4 — Error inesperado

La operación finaliza como `FAILED`.

## Postcondiciones

El resultado de la operación queda registrado.

---

# 9. CU-06 — Consultar estado de una operación

## Objetivo

Permitir al usuario conocer el estado actual de una operación.

## Actor principal

Usuario.

## Precondiciones

- Existe una operación iniciada.

## Flujo principal

1. El usuario consulta la operación.
2. El sistema obtiene su estado.
3. La aplicación muestra el estado actual.

## Estados posibles

Una operación podrá encontrarse en uno de los siguientes estados:

- `PENDING` — pendiente de ejecución.
- `RUNNING` — en ejecución.
- `SUCCESS` — finalizada correctamente.
- `FAILED` — finalizada con error.
- `CANCELLED` — cancelada.

## Postcondiciones

El usuario conoce el estado actual de la operación.

---

# 10. CU-07 — Consultar resultado de una operación

## Objetivo

Permitir al usuario consultar el resultado de una operación finalizada.

## Actor principal

Usuario.

## Precondiciones

- Existe una operación registrada.
- La operación ha finalizado.

## Flujo principal

1. El usuario selecciona una operación.
2. El sistema obtiene su resultado.
3. La aplicación muestra la información disponible.
4. El usuario puede consultar información relevante de la ejecución.

## Información posible

Dependiendo de la operación y del resultado, se podrá mostrar:

- Estado final.
- Código de salida.
- Mensaje de resultado.
- Salida de APT.
- Información de error.

## Postcondiciones

El usuario puede conocer el resultado de la operación.

---

# 11. CU-08 — Instalar aplicación

## Objetivo

Instalar Actualizar-Linux como aplicación de escritorio mediante el paquete de distribución proporcionado.

## Actor principal

Usuario.

## Actores secundarios

- Sistema operativo.
- Gestor de paquetes.

## Precondiciones

- El usuario dispone de un paquete `.deb` compatible.
- El sistema es compatible con el paquete.

## Flujo principal

1. El usuario inicia la instalación del paquete.
2. El sistema de paquetes procesa el paquete.
3. Se instalan los archivos necesarios.
4. Se registran las dependencias requeridas.
5. Se instala el lanzador de escritorio.
6. Se instala el icono de la aplicación.
7. La instalación finaliza correctamente.

## Flujos alternativos y de excepción

### A1 — Arquitectura incompatible

La instalación es rechazada.

### A2 — Dependencia no disponible

La instalación no puede completarse hasta resolver la dependencia.

### A3 — Error de instalación

El gestor de paquetes informa del error y la aplicación no se considera correctamente instalada.

## Postcondiciones

Actualizar-Linux queda instalado como aplicación de escritorio si la instalación finalizó correctamente.

---

# 12. CU-09 — Ejecutar aplicación instalada

## Objetivo

Permitir al usuario iniciar Actualizar-Linux desde el entorno de escritorio.

## Actor principal

Usuario.

## Actor secundario

Sistema operativo.

## Precondiciones

- Actualizar-Linux está instalado.
- El lanzador de la aplicación está disponible.

## Flujo principal

1. El usuario selecciona Actualizar-Linux desde el entorno de escritorio.
2. El sistema operativo inicia la aplicación.
3. La aplicación inicializa sus componentes.
4. Se muestra la interfaz gráfica.
5. La aplicación queda disponible para realizar operaciones.

## Flujos alternativos y de excepción

### A1 — Error durante el inicio

1. Se produce un error durante la inicialización.
2. La aplicación registra información relevante.
3. La aplicación informa del error cuando sea posible.

## Postcondiciones

La aplicación queda ejecutándose o informa que no pudo iniciarse.

---

# 13. CU-10 — Desinstalar aplicación

## Objetivo

Eliminar Actualizar-Linux del sistema.

## Actor principal

Usuario.

## Actores secundarios

- Sistema operativo.
- Gestor de paquetes.

## Precondiciones

- Actualizar-Linux está instalado.

## Flujo principal

1. El usuario inicia la desinstalación.
2. El gestor de paquetes elimina los componentes instalados por la aplicación.
3. Se eliminan los archivos de aplicación correspondientes.
4. Se elimina el lanzador instalado.
5. Se eliminan los componentes de distribución correspondientes.
6. La aplicación queda desinstalada.

## Reglas

La desinstalación no deberá eliminar archivos personales del usuario que no pertenezcan a la aplicación.

## Flujos alternativos y de excepción

### A1 — Error durante la desinstalación

1. El gestor de paquetes informa de un error.
2. La desinstalación puede quedar incompleta.
3. El sistema informa al usuario.

## Postcondiciones

Actualizar-Linux queda desinstalado cuando la operación finaliza correctamente.

---

# 14. Casos de uso futuros

Los siguientes casos de uso son posibles extensiones del proyecto y no forman parte de la versión inicial:

- CU-F01 — Consultar información del sistema.
- CU-F02 — Consultar actualizaciones disponibles.
- CU-F03 — Configurar preferencias de la aplicación.

Estos casos no deberán considerarse requisitos de la versión 2.0 mientras no sean incorporados formalmente al alcance.

---

# 15. Relación con otros documentos

Los casos de uso deben interpretarse junto con:

- `requirements.md` — define los requisitos del sistema.
- `business-rules.md` — define las reglas que deben respetarse durante la ejecución.
- `architecture.md` — define la estructura técnica del sistema.
- `technical-decisions.md` — documenta las decisiones técnicas.
- `testing.md` — define cómo se validan los casos de uso.

Los casos de uso no deben utilizarse para documentar detalles internos de implementación.

Por ejemplo, una implementación concreta mediante `subprocess`, `pkexec`, hilos de Qt o clases específicas pertenece a la arquitectura o a las decisiones técnicas, no al caso de uso.