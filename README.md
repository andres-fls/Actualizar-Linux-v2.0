# Actualizar-Linux v2.0

Aplicación de escritorio para realizar tareas de mantenimiento sobre sistemas Linux basados en Debian y Ubuntu utilizando APT.

Actualizar-Linux v2.0 es la evolución de la versión inicial desarrollada en Bash. Esta nueva versión se está desarrollando en Python con una arquitectura modular, interfaz gráfica, ejecución controlada de comandos, gestión de privilegios, logging, pruebas automatizadas y distribución mediante paquetes `.deb`.

> **Estado:** En desarrollo.

## Características

Actualmente el alcance del proyecto contempla:

- Actualización de los índices de paquetes.
- Actualización de paquetes instalados.
- Eliminación de paquetes innecesarios mediante `apt-get autoremove`.
- Limpieza de la caché de APT mediante `apt-get clean`.
- Interfaz gráfica de escritorio.
- Ejecución controlada de operaciones administrativas.
- Gestión de privilegios mediante `pkexec`.
- Visualización de la salida de APT.
- Gestión de estados y resultados de las operaciones.
- Logging de eventos relevantes.
- Pruebas automatizadas.
- Distribución mediante paquete `.deb`.

## Sistemas objetivo

La versión inicial está orientada a:

- Debian.
- Ubuntu.
- Linux Mint.
- Otras distribuciones compatibles con APT, siempre que sean validadas durante las pruebas del proyecto.

La compatibilidad no implica que todas las distribuciones derivadas de Debian o Ubuntu estén soportadas automáticamente.

## Requisitos de desarrollo

Para trabajar con el código fuente se requiere, como mínimo:

- Python 3.12 o superior.
- Git.
- Un sistema Linux compatible con APT.
- `apt-get`.
- `pkexec` para las operaciones que requieran privilegios administrativos.

Las dependencias de desarrollo se gestionarán mediante `pyproject.toml`.

## Instalación

La distribución final está prevista mediante un paquete `.deb`.

Una vez generado el paquete, la instalación tendrá una forma similar a:

```bash
sudo dpkg -i actualizar-linux_<version>_<arquitectura>.deb
