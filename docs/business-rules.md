# Reglas de negocio de Actualizar-Linux v2.0

## 1. Propósito

Este documento define las reglas que determinan el comportamiento de Actualizar-Linux v2.0.

Las reglas de negocio establecen **cómo debe comportarse el sistema ante determinadas situaciones**, independientemente de la tecnología utilizada para implementarlo.

---

# 2. Reglas de actualización

## RN-01 — Secuencia de actualización completa

Una actualización completa deberá ejecutar las operaciones de actualización de repositorios y actualización de paquetes en el siguiente orden:

1. Actualizar los índices de paquetes.
2. Actualizar los paquetes instalados.

La segunda operación no deberá ejecutarse si la primera falla.

---

## RN-02 — Resultado basado en la ejecución real

El resultado de una operación deberá determinarse a partir del resultado real del proceso ejecutado y no únicamente mediante el análisis textual de su salida.

El código de salida del proceso será un elemento fundamental para determinar el resultado.

---

## RN-03 — Independencia de operaciones de mantenimiento

Las operaciones de eliminación de paquetes innecesarios y limpieza de caché serán independientes entre sí.

La ejecución de una no deberá implicar automáticamente la ejecución de la otra.

---

## RN-04 — Dependencias entre operaciones

Cuando una operación dependa del resultado exitoso de una operación anterior, la operación dependiente no deberá ejecutarse si su predecesora falla.

Las operaciones independientes no deberán bloquearse innecesariamente por el resultado de otras operaciones.

---

# 3. Reglas de privilegios

## RN-05 — Principio de mínimo privilegio

El sistema deberá solicitar privilegios administrativos únicamente para las operaciones que los requieran.

La aplicación no deberá ejecutarse completamente con privilegios administrativos como mecanismo general de funcionamiento.

---

## RN-06 — Operación sin privilegios

Si una operación requiere privilegios administrativos y estos no están disponibles, la operación no deberá ejecutarse.

El sistema deberá comunicar al usuario la situación.

---

## RN-07 — Cancelación de autorización

Si el usuario cancela o rechaza la solicitud de privilegios:

- La operación no deberá ejecutarse.
- El resultado deberá identificarse como `CANCELLED`.
- La situación no deberá tratarse como un error interno de la aplicación.

---

## RN-08 — Fallo de elevación

Si el mecanismo de elevación de privilegios falla por una causa distinta a la cancelación explícita del usuario:

- La operación deberá finalizar como `FAILED`.
- El sistema deberá registrar el evento correspondiente.
- El usuario deberá recibir información sobre el fallo.

---

# 4. Reglas relacionadas con APT

## RN-09 — Disponibilidad de APT

Antes de ejecutar una operación que dependa de APT, el sistema deberá poder determinar si el ejecutable requerido está disponible.

Si APT no está disponible, la operación no deberá intentar ejecutarse.

---

## RN-10 — Operaciones APT controladas

Las operaciones ejecutadas por la aplicación deberán utilizar únicamente comandos y argumentos definidos por el propio sistema.

La aplicación no deberá permitir que el usuario introduzca arbitrariamente comandos del sistema para ejecutarlos mediante la aplicación.

---

## RN-11 — Evaluación del proceso

El resultado de una operación deberá considerar como mínimo:

- Código de salida.
- Estado del proceso.
- Salida estándar cuando sea relevante.
- Salida de error cuando sea relevante.

La interpretación concreta del resultado deberá mantenerse separada de la interfaz gráfica.

---

# 5. Reglas de la interfaz gráfica

## RN-12 — Separación de responsabilidades

La interfaz gráfica no deberá contener la lógica de negocio relacionada directamente con APT.

La GUI deberá interactuar con los casos de uso y presentar sus resultados.

---

## RN-13 — No bloqueo de la interfaz

Las operaciones de larga duración no deberán ejecutarse de forma que bloqueen el hilo principal de la interfaz gráfica.

---

## RN-14 — Estado coherente

El sistema deberá mantener un estado coherente durante la ejecución de las operaciones.

Mientras una operación incompatible se encuentre en ejecución, la interfaz deberá impedir o gestionar adecuadamente el inicio simultáneo de otra operación que pueda generar conflictos.

---

## RN-15 — Estados terminales

Toda operación deberá finalizar en uno de los siguientes estados terminales:

- `SUCCESS`
- `FAILED`
- `CANCELLED`

Los estados intermedios, como `PENDING` o `RUNNING`, representan estados de ejecución y no resultados finales.

---

# 6. Reglas de mantenimiento

## RN-16 — Selección de paquetes para autoremove

La selección de paquetes que pueden eliminarse mediante `autoremove` deberá ser responsabilidad de APT.

La aplicación no deberá implementar su propia lógica para determinar qué paquetes son innecesarios.

---

## RN-17 — Limpieza de caché

La limpieza de la caché de paquetes deberá delegarse a APT.

La aplicación no deberá eliminar manualmente archivos de la caché de APT para implementar la operación equivalente a `clean`.

---

# 7. Reglas de seguridad

## RN-18 — Sin ejecución arbitraria

La aplicación no deberá proporcionar una funcionalidad genérica para ejecutar comandos arbitrarios del sistema.

Las operaciones disponibles deberán estar definidas por la aplicación.

---

## RN-19 — Elevación limitada

La aplicación no deberá ejecutar toda su lógica con privilegios administrativos.

La elevación deberá limitarse a las operaciones que realmente necesiten privilegios.

---

## RN-20 — Protección de credenciales

La aplicación no deberá almacenar ni registrar credenciales de autenticación o información equivalente.

---

# 8. Reglas de logging

## RN-21 — Registro de eventos relevantes

El sistema deberá registrar eventos importantes relacionados con la ejecución de operaciones, incluyendo:

- Inicio.
- Finalización.
- Resultado.
- Error.
- Cancelación.

---

## RN-22 — Protección de información sensible

Los registros no deberán contener credenciales, secretos u otra información sensible innecesaria para el diagnóstico.

---

# 9. Reglas de instalación y distribución

## RN-23 — Formato de distribución

La distribución inicial de la aplicación deberá realizarse mediante paquetes `.deb`.

---

## RN-24 — Independencia del entorno de desarrollo

El usuario final no deberá necesitar:

- Python.
- `pip`.
- Entornos virtuales.
- El repositorio Git.
- El código fuente.

para utilizar la aplicación distribuida como paquete.

---

## RN-25 — Desinstalación limpia

La desinstalación deberá eliminar los componentes instalados por la aplicación sin eliminar archivos personales del usuario que no pertenezcan al software.

---

# 10. Relación con otros documentos

Las reglas de este documento se relacionan con:

- `requirements.md` — define qué debe hacer el sistema.
- `use-cases.md` — define las interacciones y flujos del sistema.
- `architecture.md` — define cómo se estructura el software.
- `technical-decisions.md` — documenta las decisiones técnicas y sus justificaciones.
- `testing.md` — define cómo se validará el comportamiento del sistema.

Las reglas de negocio no deberán duplicarse innecesariamente en los demás documentos.