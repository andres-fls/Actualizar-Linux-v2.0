# Guía de Desarrollo

## Configuración del Entorno

### Prerrequisitos

- Python 3.12+
- Git
- Entorno Linux (Debian/Ubuntu)

### Clonar y crear entorno

````bash
git clone https://github.com/usuario/actualizar-linux.git
cd actualizar-linux
python -m venv .venv
source .venv/bin/activate
pip install -e .[dev]
Desarrollo
Estructura de Código
src/actualizar_linux/presentation/ → GUI

src/actualizar_linux/application/ → Casos de uso

src/actualizar_linux/core/ → Entidades e interfaces

src/actualizar_linux/infrastructure/ → Implementaciones concretas

src/actualizar_linux/main.py → Punto de entrada

Convenciones
PEP 8

Type hints en funciones públicas

Nombres descriptivos

Funciones pequeñas y con una responsabilidad

Comandos
bash
# Ejecutar aplicación
python -m actualizar_linux.main

# Tests
pytest

# Lint y formato
ruff check .
ruff format .

# Type checking
mypy src
Testing
Unit Tests
Prueban lógica de negocio sin dependencias externas (usando fakes/mocks).

Integration Tests
Prueban interacción con componentes reales controlados (no ejecutan apt-get upgrade real).

Estrategia
Mock APTService para probar casos de uso.

Mock CommandRunner para probar APTService.

Pruebas de integración para infraestructura.

Pruebas manuales en entorno controlado para operaciones reales.

CI/CD (GitHub Actions)
Workflow básico:

yaml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: '3.12'
      - run: pip install -e .[dev]
      - run: ruff check .
      - run: mypy src
      - run: pytest
Construcción del Paquete .deb
(Se definirá más adelante; inicialmente se usará packaging/deb/)

Estándares de Commit
feat: nueva funcionalidad

fix: corrección de error

docs: documentación

test: pruebas

refactor: refactorización

text

---

### `docs/CHANGELOG.md`
```markdown
# Changelog

## v2.0.0 (Planeada)

### Características Principales
- 🎯 Migración completa de Bash a Python
- 🖥️ Interfaz gráfica con PySide6
- 📦 Distribución mediante paquete .deb
- 🔄 Operaciones de mantenimiento independientes
- 📝 Sistema de logging integrado
- 🧪 Testing automatizado con pytest
- 🔒 Elevación de privilegios con pkexec

### Operaciones Soportadas
- `apt-get update`
- `apt-get upgrade`
- `apt-get autoremove`
- `apt-get clean`

### Mejoras Técnicas
- Arquitectura por capas
- Abstracción de APT
- Separación GUI/Lógica
- No bloqueo de interfaz
- Manejo controlado de errores

### Cambios con v1.0
- ✅ Script Bash → Aplicación Python
- ✅ CLI → GUI
- ✅ Código monolítico → Arquitectura modular
- ✅ Sin testing → Testing automatizado
- ✅ Sin logging → Logging estructurado
````
