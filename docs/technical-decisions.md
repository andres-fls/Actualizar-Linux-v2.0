# Decisiones Técnicas

## Stack Tecnológico

| Área          | Tecnología     |
| ------------- | -------------- |
| Lenguaje      | Python 3.12+   |
| GUI           | PySide6 (Qt 6) |
| Procesos      | subprocess     |
| Privilegios   | pkexec         |
| Testing       | pytest         |
| Lint/Format   | Ruff           |
| Type Checking | mypy           |
| Configuración | pyproject.toml |
| CI/CD         | GitHub Actions |
| Distribución  | .deb           |

## Justificaciones

### Python 3.12+

- Moderno y estable
- Excelente soporte de librerías
- Compatible con herramientas de desarrollo
- Permite arquitectura modular

### PySide6 vs Alternativas

**Elegido**: PySide6 por ser completo, profesional, con buena separación GUI/lógica y soporte nativo para threading.  
**Descartados**: Tkinter (limitado), GTK (complejidad de bindings).

### pkexec como Mecanismo de Privilegios

- Diseñado para aplicaciones gráficas
- Principio de mínimo privilegio (la GUI no corre como root)
- Nativo en entornos Linux modernos  
  _Fallback opcional: sudo._

### subprocess vs os.system

- Mayor control (stdout, stderr, código de salida)
- Previene inyección de comandos
- Argumentos estructurados

### Ruff vs Herramientas Separadas

- Linter + formatter en una sola herramienta
- Más rápido y con menos dependencias

### Logging Estándar

Suficiente para las necesidades; sin dependencias externas.

## Decisiones de Diseño

- **Separación de responsabilidades**: GUI no conoce APT.
- **Casos de uso explícitos**: Cada operación tiene su propio caso de uso.
- **Abstracción de APT**: Interfaz `APTProvider` permite implementación real y fake para tests.
- **No bloqueo**: Uso de hilos Qt para operaciones largas.

## Decisiones sobre Empaquetado

- Primero se desarrolla la aplicación Python funcional.
- Posteriormente se aborda la construcción del .deb (se usará la estructura en `packaging/deb/`).
- No se decide ahora si se incluirá el intérprete o se dependerá del sistema; se evaluará más adelante.

## Fuera de Alcance (técnico)

- Soporte para dnf, pacman, zypper
- Actualización automática de la aplicación
- Sistema de plugins
- Telemetría
- Administración remota
- Versión móvil
