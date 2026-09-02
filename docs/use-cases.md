# Casos de Uso

## CU-01: Actualizar Información de Repositorios

El usuario solicita actualizar la información de repositorios.
Usuario → Solicitar privilegios → apt-get update → Resultado

text

## CU-02: Actualizar Paquetes del Sistema

El usuario solicita actualizar los paquetes instalados.
Usuario → Solicitar privilegios → apt-get upgrade → Resultado

text

## CU-03: Actualizar Sistema Completo

Operación principal que combina CU-01 y CU-02 secuencialmente.
Usuario → apt-get update → ¿Éxito? → apt-get upgrade → Resultado

text

## CU-04: Eliminar Paquetes Innecesarios

Ejecutar `apt-get autoremove`.
Usuario → Solicitar privilegios → apt-get autoremove → Resultado

text

## CU-05: Limpiar Caché de Paquetes

Ejecutar `apt-get clean`.
Usuario → Solicitar privilegios → apt-get clean → Resultado

text

## CU-06: Solicitar Privilegios Administrativos

Elevación de privilegios mediante pkexec.
Aplicación → ¿Root? → No → pkexec → Autorización → Ejecutar

text

## CU-07: Cancelar Solicitud de Privilegios

El usuario rechaza la autenticación.
Cancelación → No ejecutar operación → Informar cancelación

text

## CU-08: Gestionar Error de APT

Detectar y manejar errores de APT.
APT → Exit code != 0 → Registrar error → Informar fallo

text

## CU-09: Gestionar APT no Disponible

Si no existe apt-get, informar incompatibilidad.

## CU-10: Gestionar Error Inesperado

Capturar, registrar y mostrar mensaje comprensible.

## CU-11: Consultar Estado de Operación

El usuario puede ver el progreso (ej. barra de progreso, mensajes).

## CU-12: Consultar Resultado

Al finalizar, mostrar éxito, error o cancelación.

## CU-13: Consultar Detalles de Ejecución

Permitir ver salida de APT, errores, código de salida (área expandible o log).

## CU-14: Registrar Operaciones

Logging de eventos: inicio, ejecución, resultado, error, cancelación.

## CU-15: Instalar Aplicación

Mediante paquete .deb, debe instalar todos los componentes.

## CU-16: Ejecutar Aplicación Instalada

Desde el menú del escritorio, sin necesidad de línea de comandos.

## CU-17: Desinstalar Aplicación

Eliminar componentes sin afectar archivos personales.

## Casos de Uso Futuros (no implementados en v2.0)

- CU-18: Consultar información del sistema (distribución, versión, etc.)
- CU-19: Comprobar actualizaciones disponibles sin ejecutar upgrade
- CU-20: Configurar comportamiento (autoremove automático, nivel de logging, etc.)
