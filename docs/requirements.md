# Requisitos de Actualizar-Linux v2.0

## Requisitos Funcionales

### RF-01: Actualización del Sistema

La aplicación debe ejecutar `apt-get update` seguido de `apt-get upgrade`. La segunda operación **no** debe ejecutarse si la actualización de repositorios falla.

### RF-02: Autoremove

Permitir ejecutar `apt-get autoremove` para eliminar dependencias innecesarias.

### RF-03: Clean

Permitir ejecutar `apt-get clean` para limpiar la caché local.

### RF-04: Operaciones Independientes

El usuario debe poder ejecutar cada operación (update, upgrade, autoremove, clean) de forma independiente.

### RF-05: Interfaz Gráfica

La GUI debe permitir:

- Iniciar actualizaciones
- Ejecutar autoremove y clean
- Visualizar estado y resultados de operaciones
- Mostrar mensajes de éxito o error

### RF-06: Ejecución Controlada

Las operaciones del sistema deben ejecutarse mediante argumentos estructurados, evitando concatenación insegura de comandos.

### RF-07: Permisos Administrativos

Utilizar pkexec como mecanismo principal de elevación de privilegios.

### RF-08: Cancelación de Permisos

Si el usuario cancela la solicitud de privilegios, la operación debe abortarse y notificarse como cancelada.

### RF-09: Manejo de Errores

Detectar y comunicar errores de:

- Ausencia de apt-get
- Fallo de elevación de privilegios
- Errores de APT
- Errores inesperados

## Requisitos de Interfaz

### RF-10: Estado de Operación

La GUI debe mostrar estados claros: "Listo", "Actualizando repositorios...", "Operación completada", "Operación fallida", etc.

### RF-11: Salida de APT

Permitir visualizar información relevante de APT mediante área expandible o log integrado.

### RF-12: No Bloquear la Interfaz

Las operaciones de APT deben ejecutarse en hilos separados sin congelar la GUI.

## Requisitos de Instalación

### RF-13: Paquete .deb

Distribución mediante paquete `actualizar-linux_2.0.0_amd64.deb`

### RF-14: Instalación Completa

El paquete debe instalar:

- Aplicación
- Archivos necesarios
- Icono
- Archivo .desktop
- Dependencias declaradas

### RF-15: Lanzador

La aplicación debe aparecer en el menú de aplicaciones del escritorio.

### RF-16: Desinstalación Limpia

Eliminar componentes de la aplicación sin afectar archivos personales.

## Requisitos de Calidad

### RQ-01: Código Mantenible

- PEP 8
- Nombres descriptivos
- Funciones pequeñas
- Type hints
- Documentación

### RQ-02: Testing Automatizado

- Unit tests
- Integration tests
- No ejecutar operaciones destructivas en tests

### RQ-03: CI/CD

GitHub Actions con:

- Linting
- Tests
- Build del paquete

### RQ-04: Logging

Uso del sistema `logging` de Python para diagnosticar problemas.

### RQ-05: Seguridad

- No ejecución arbitraria de comandos
- Validación de operaciones
- Privilegios mínimos necesarios
- No almacenar credenciales

## Requisitos No Funcionales

### RNF-01: Usabilidad

Usuario con conocimientos básicos de Linux debe poder instalar y usar la aplicación sin conocer Python.

### RNF-02: Rendimiento

No consumir recursos significativos en inactividad.

### RNF-03: Estabilidad

Un error en una operación no debe dejar la aplicación en estado inconsistente.

### RNF-04: Transparencia

El usuario debe saber qué operación se ejecuta y su resultado.

### RNF-05: Distribución

El usuario final no debe necesitar clonar el repositorio ni instalar dependencias manualmente.

## Compatibilidad Inicial

- Debian
- Ubuntu
- Linux Mint
- Otras derivadas con APT
