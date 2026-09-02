Desarrollo — Actualizar-Linux v2.0

1. Propósito

Este documento define las prácticas y el flujo de trabajo para desarrollar Actualizar-Linux v2.0.

El objetivo es mantener un proceso de desarrollo ordenado, reproducible y adecuado para el tamaño del proyecto.

---

2. Requisitos de desarrollo

Para trabajar en el proyecto se necesitará inicialmente:

- Python 3.12 o superior.
- Git.
- Un sistema Linux compatible con APT para las pruebas de integración.
- Editor o IDE.
- Acceso al repositorio de GitHub.

Herramientas adicionales del proyecto:

- pytest
- Ruff
- mypy

---

3. Clonar el repositorio

El repositorio del proyecto es:

https://github.com/andres-fls/Actualizar-Linux-v2.0

Para clonarlo:

git clone https://github.com/andres-fls/Actualizar-Linux-v2.0.git
cd Actualizar-Linux-v2.0

---

4. Crear el entorno virtual

Se utilizará "venv" para mantener aisladas las dependencias del proyecto.

Crear el entorno:

python3 -m venv .venv

Activarlo:

source .venv/bin/activate

Para comprobar que el entorno está activo:

which python
python --version

La terminal debería utilizar el Python ubicado dentro de ".venv".

---

5. Instalación de dependencias

Las dependencias del proyecto se gestionarán mediante "pyproject.toml".

Durante las primeras etapas, si el proyecto todavía no tiene configurado su sistema definitivo de instalación, las dependencias podrán instalarse manualmente dentro del entorno virtual.

Las dependencias principales previstas son:

PySide6
pytest
Ruff
mypy

Las dependencias definitivas y sus versiones se establecerán en "pyproject.toml".

---

6. Estructura del código

El código principal estará dentro de:

src/actualizar_linux/

La estructura inicial propuesta es:

src/
└── actualizar_linux/
    ├── presentation/
    │   └── gui/
    │
    ├── application/
    │   ├── update_system.py
    │   ├── autoremove.py
    │   └── clean.py
    │
    ├── core/
    │   ├── operation.py
    │   ├── result.py
    │   └── interfaces/
    │       ├── apt.py
    │       └── privileges.py
    │
    ├── infrastructure/
    │   ├── apt/
    │   ├── process/
    │   ├── privileges/
    │   └── logging/
    │
    └── main.py

Esta estructura es inicial y podrá modificarse durante el desarrollo si alguna separación resulta innecesaria.

---

7. Estructura de pruebas

Las pruebas estarán separadas del código de producción:

tests/
├── unit/
├── integration/
└── ...

Pruebas unitarias

Se utilizarán para comprobar componentes y lógica aislada.

Ejemplos:

- Estados de operaciones.
- Resultados.
- Reglas de actualización.
- Manejo de errores.
- Decisiones de flujo.

Pruebas de integración

Se utilizarán para comprobar la interacción entre componentes reales.

Cuando sea necesario podrán utilizar:

- Sistema Linux real.
- APT.
- Procesos reales.
- Mecanismos de privilegios.

No todas las pruebas deberán ejecutar operaciones administrativas reales.

---

8. Flujo de desarrollo

El desarrollo seguirá, de forma general, este proceso:

Analizar necesidad
       ↓
Definir comportamiento
       ↓
Revisar requisitos y reglas
       ↓
Diseñar solución
       ↓
Implementar
       ↓
Probar
       ↓
Revisar código
       ↓
Documentar si corresponde
       ↓
Commit

No será obligatorio realizar documentación adicional para cambios pequeños que no modifiquen decisiones o comportamiento relevante.

---

9. Desarrollo basado en casos de uso

Las funcionalidades principales se implementarán tomando como referencia los casos de uso definidos en:

docs/use-cases.md

Ejemplos:

CU-01 Actualizar repositorios
CU-02 Actualizar paquetes
CU-03 Realizar actualización completa
CU-04 Eliminar paquetes innecesarios
CU-05 Limpiar caché

Cada implementación deberá respetar los requisitos y reglas de negocio relacionados.

---

10. Reglas de implementación

Durante el desarrollo se deberán mantener las siguientes reglas:

Separación de responsabilidades

La GUI no debe contener lógica de APT.

Comandos controlados

Los comandos deben construirse mediante argumentos estructurados.

Ejemplo:

["apt-get", "update"]

Evitar la construcción innecesaria de comandos mediante concatenación de cadenas.

Sin comandos arbitrarios

La aplicación no debe permitir que el usuario introduzca un comando para ejecutarlo directamente.

Privilegios mínimos

No ejecutar toda la aplicación como "root".

Solicitar privilegios únicamente cuando sean necesarios.

Código mantenible

Priorizar código claro y comprensible sobre soluciones excesivamente complejas.

No sobreingeniería

No introducir patrones, clases, capas o abstracciones que no tengan una necesidad concreta.

---

11. Ejecución durante el desarrollo

La aplicación podrá ejecutarse desde el entorno de desarrollo mediante Python.

La forma definitiva de ejecución dependerá de la estructura final del paquete Python.

Como referencia, el punto de entrada será:

src/actualizar_linux/main.py

La ejecución podrá realizarse mediante el mecanismo definido en "pyproject.toml".

---

12. Verificaciones antes de realizar un commit

Antes de integrar cambios importantes se recomienda ejecutar:

pytest

ruff check .

ruff format --check .

Si mypy está configurado para el código correspondiente:

mypy .

También deberá realizarse una prueba manual cuando el cambio afecte directamente a:

- GUI.
- Ejecución de APT.
- Privilegios.
- Instalación.
- Desinstalación.
- Integración con Linux.

---

13. Git

Se utilizará Git para controlar los cambios del proyecto.

El flujo básico será:

git status
git add .
git commit -m "Descripción del cambio"
git push

Los commits deberán describir de forma clara qué se modificó.

Ejemplos:

feat: agregar operación de actualización
fix: corregir manejo de cancelación
test: agregar pruebas para autoremove
docs: actualizar decisiones técnicas
refactor: separar ejecución de procesos

No es obligatorio utilizar exactamente estos prefijos si el proyecto adopta posteriormente una convención diferente.

---

14. Rama principal

La rama principal del proyecto será:

main

Los cambios importantes deberán validarse antes de integrarse en "main".

El uso de ramas adicionales podrá adoptarse cuando el proyecto lo necesite, pero no es obligatorio para cambios pequeños o individuales.

---

15. GitHub Actions

El proyecto utilizará GitHub Actions para automatizar comprobaciones.

El pipeline deberá comprobar progresivamente:

Instalación de dependencias
        ↓
Lint
        ↓
Type checking
        ↓
Tests

La configuración definitiva estará dentro de:

.github/workflows/

La CI no sustituye las pruebas manuales necesarias para comprobar la integración real con Linux.

---

16. Desarrollo de la GUI

La interfaz gráfica deberá mantenerse separada de la lógica de las operaciones.

La GUI deberá:

- Solicitar operaciones.
- Mostrar estados.
- Mostrar resultados.
- Mostrar información relevante de APT.
- Informar errores.
- Mantenerse responsiva.

La GUI no deberá:

- Construir directamente comandos APT.
- Ejecutar directamente "apt-get".
- Gestionar directamente privilegios administrativos.
- Implementar reglas de negocio.

---

17. Desarrollo de operaciones APT

Las operaciones principales serán implementadas de forma independiente.

Inicialmente:

Actualizar repositorios
Actualizar paquetes
Eliminar paquetes innecesarios
Limpiar caché

La actualización completa tendrá una secuencia controlada:

apt-get update
      │
      ├── éxito ──► apt-get upgrade
      │
      └── fallo ──► detener

Las reglas completas están definidas en:

docs/business-rules.md

---

18. Manejo de errores

Los errores deberán gestionarse en el nivel correspondiente.

Ejemplos:

- APT no disponible.
- Error al iniciar un proceso.
- Fallo de APT.
- Falta de privilegios.
- Cancelación de la autorización.
- Error inesperado.

La aplicación deberá diferenciar entre:

SUCCESS
FAILED
CANCELLED

La cancelación de una solicitud de privilegios no debe tratarse automáticamente como un fallo interno.

---

19. Logging durante el desarrollo

Los eventos importantes deberán registrarse mediante "logging".

Como mínimo podrán registrarse:

Inicio de operación
Operación ejecutada
Resultado
Código de salida
Error
Cancelación

No deberán registrarse contraseñas, credenciales u otra información sensible.

---

20. Cambios en la arquitectura

La arquitectura definida en "docs/architecture.md" es una guía inicial.

Durante la implementación podrá modificarse si:

- Una capa resulta innecesaria.
- Una responsabilidad está ubicada incorrectamente.
- Aparece una necesidad técnica real.
- Se encuentra una solución más sencilla.
- Una decisión previa dificulta innecesariamente el desarrollo.

Los cambios importantes deberán reflejarse en la documentación correspondiente.

---

21. Cambios en decisiones técnicas

Cuando se modifique una decisión técnica importante, deberá revisarse:

docs/technical-decisions.md

Ejemplos:

- Cambio de framework GUI.
- Cambio del mecanismo de privilegios.
- Cambio de estrategia de empaquetado.
- Cambio de herramienta de testing.
- Cambio de estrategia de distribución.

No es necesario documentar como decisión formal cada cambio menor de implementación.

---

22. Documentación

La documentación principal del proyecto estará organizada en:

docs/
├── README.md
├── requirements.md
├── business-rules.md
├── use-cases.md
├── architecture.md
├── technical-decisions.md
├── development.md
└── testing.md

Cada documento tendrá una responsabilidad específica y se evitará duplicar información innecesariamente.

---

23. Criterios de desarrollo

Durante el desarrollo se priorizará:

1. Funcionamiento correcto.
2. Seguridad.
3. Código comprensible.
4. Testabilidad.
5. Mantenibilidad.
6. Simplicidad.

No se priorizará una arquitectura sofisticada por sí misma.

El objetivo es construir una aplicación funcional y bien estructurada, aprendiendo ingeniería de software mediante un proyecto real.

---

24. Evolución del proyecto

El desarrollo será incremental.

La primera versión funcional deberá concentrarse en las operaciones principales y la GUI.

Después podrán incorporarse progresivamente:

- Mejoras de interfaz.
- Mejor manejo de resultados.
- Más pruebas.
- Instalación mediante ".deb".
- Mejoras de compatibilidad.
- Información del sistema.
- Comprobación de actualizaciones.
- Configuración de la aplicación.

Las funcionalidades futuras no deberán introducirse antes de que las funcionalidades actuales estén suficientemente estables.

---

25. Resumen del flujo de trabajo

El flujo general de desarrollo será:

1. Revisar documentación
2. Seleccionar funcionalidad
3. Analizar requisitos y reglas
4. Diseñar la solución
5. Implementar
6. Crear/actualizar pruebas
7. Ejecutar verificaciones
8. Probar manualmente cuando corresponda
9. Revisar el código
10. Actualizar documentación si es necesario
11. Crear commit
12. Integrar cambios

Este proceso podrá adaptarse según el tamaño y complejidad de cada cambio.