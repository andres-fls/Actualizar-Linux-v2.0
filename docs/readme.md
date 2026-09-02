# Actualizar-Linux v2.0

Aplicación de mantenimiento para sistemas Linux basados en Debian/Ubuntu con interfaz gráfica.

## Visión General

Actualizar-Linux es una herramienta que facilita tareas de mantenimiento del sistema mediante una interfaz gráfica intuitiva, utilizando APT como gestor de paquetes. Esta versión 2.0 representa una evolución completa del script Bash original, implementada en Python con arquitectura modular.

## Características Principales

- **Actualización del sistema**: Actualiza repositorios y paquetes instalados
- **Limpieza automática**: Elimina paquetes innecesarios (autoremove)
- **Limpieza de caché**: Elimina archivos de paquetes descargados (clean)
- **Interfaz gráfica**: GUI moderna con PySide6
- **Operaciones independientes**: Ejecuta cada tarea por separado
- **Logging integrado**: Registro completo de operaciones
- **Distribución nativa**: Paquete .deb para instalación sencilla

## Requisitos del Sistema

- Linux Debian/Ubuntu o derivados
- APT (apt-get)
- Python 3.12+
- pkexec (para elevación de privilegios)

## Instalación

```bash
sudo dpkg -i actualizar-linux_2.0.0_amd64.deb
Documentación
Requisitos

Casos de Uso

Arquitectura

Decisiones Técnicas

Guía de Desarrollo

Fuera de Alcance (v2.0)
Soporte para otros gestores (dnf, pacman, zypper)

Actualización automática de la aplicación

Sistema de plugins

Telemetría

Administración remota

Versión móvil

Licencia
[Especificar licencia]
```
