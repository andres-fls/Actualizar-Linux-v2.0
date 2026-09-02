Decisiones Técnicas — Actualizar-Linux v2.0

1. Propósito

Este documento registra las principales decisiones técnicas del proyecto Actualizar-Linux v2.0.

Su objetivo es dejar constancia de:

- Qué tecnologías se utilizarán.
- Por qué fueron seleccionadas.
- Qué criterios técnicos deben respetarse.
- Qué decisiones quedan pendientes para etapas posteriores.

Las decisiones podrán revisarse si durante el desarrollo aparece una necesidad que justifique modificarlas.

---

2. Versión de Python

Decisión

Utilizar:

Python 3.12+

Justificación

Python será el lenguaje principal del proyecto debido a:

- Facilidad de desarrollo.
- Amplio ecosistema.
- Buen soporte para aplicaciones de escritorio.
- Disponibilidad de herramientas para testing, logging y automatización.
- Experiencia previa del proyecto con Python.

La versión mínima podrá revisarse posteriormente si las dependencias utilizadas requieren una versión diferente.

---

3. Interfaz gráfica

Decisión

Utilizar:

PySide6

basado en:

Qt 6

Justificación

PySide6 proporciona:

- Componentes completos para interfaces gráficas.
- Soporte multiplataforma.
- Integración con procesos en segundo plano.
- Señales y slots para comunicación entre componentes.
- Una base adecuada para una aplicación de escritorio.

La interfaz se mantendrá separada de la lógica de las operaciones del sistema.

---

4. Ejecución de comandos

Decisión

Utilizar el módulo estándar de Python:

subprocess

para ejecutar procesos externos.

Las operaciones de mantenimiento utilizarán principalmente:

apt-get

Justificación

"subprocess" permite controlar:

- Comando ejecutado.
- Argumentos.
- Código de salida.
- Salida estándar.
- Salida de error.
- Estado del proceso.

Los comandos se construirán preferiblemente como listas de argumentos:

["apt-get", "update"]

en lugar de construir comandos mediante concatenación de cadenas.

Seguridad

El uso de listas de argumentos y la ejecución sin shell cuando no sea necesario reduce riesgos asociados con la interpretación de comandos.

La aplicación no debe aceptar comandos arbitrarios proporcionados por el usuario.

---

5. Gestor de paquetes

Decisión

Utilizar:

APT

mediante:

apt-get

Operaciones iniciales

apt-get update
apt-get upgrade
apt-get autoremove
apt-get clean

Justificación

El proyecto está dirigido inicialmente a sistemas Debian/Ubuntu y derivados compatibles con APT.

Se utilizará "apt-get" para las operaciones automatizadas porque está diseñado para uso programático y permite controlar mejor la ejecución mediante parámetros y códigos de salida.

No se implementará inicialmente soporte para:

dnf
pacman
zypper

u otros gestores de paquetes.

---

6. Elevación de privilegios

Decisión

Utilizar:

pkexec

como mecanismo principal para ejecutar operaciones que requieran privilegios administrativos.

Justificación

Permite solicitar autorización al usuario para una operación administrativa sin ejecutar toda la aplicación como "root".

Principio de seguridad

La aplicación no debe ejecutarse completamente con privilegios administrativos.

Los privilegios deben solicitarse únicamente cuando sean necesarios.

Cancelación

Si el usuario cancela la solicitud de privilegios:

- No se ejecutará la operación administrativa.
- La operación se marcará como "CANCELLED".
- No se tratará como un error interno de la aplicación.

Fallback

El uso de otros mecanismos, como "sudo", no forma parte de la primera implementación.

Podrá evaluarse posteriormente si existe una necesidad real de compatibilidad.

---

7. Logging

Decisión

Utilizar el módulo estándar:

logging

Eventos importantes

Se registrarán eventos como:

- Inicio de una operación.
- Tipo de operación.
- Resultado.
- Código de salida.
- Errores relevantes.
- Cancelaciones.
- Problemas de ejecución.

Seguridad

Los logs no deben contener:

- Contraseñas.
- Credenciales.
- Información sensible innecesaria.
- Datos que permitan reconstruir credenciales administrativas.

La información registrada debe ser suficiente para diagnosticar problemas sin almacenar información innecesaria.

---

8. Testing

Decisión

Utilizar:

pytest

para las pruebas automatizadas.

Justificación

pytest permite crear pruebas:

- Unitarias.
- De integración.
- De componentes.
- Automatizadas en CI.

La mayor parte de la lógica de aplicación deberá poder probarse sin ejecutar realmente operaciones destructivas sobre APT.

Pruebas de operaciones del sistema

Cuando sea necesario probar la integración con APT o Linux, se podrán utilizar mocks, fakes o entornos controlados.

No se debe depender exclusivamente de la ejecución real de:

apt-get upgrade
apt-get autoremove

para validar toda la lógica de la aplicación.

---

9. Formato y calidad del código

Decisión

Utilizar:

Ruff

para análisis y formateo del código.

Utilizar:

mypy

para comprobación de tipos cuando resulte apropiado.

Objetivo

Mantener:

- Código consistente.
- Imports organizados.
- Errores comunes detectables automáticamente.
- Tipado progresivo.
- Una base de código fácil de mantener.

La configuración se centralizará en:

pyproject.toml

cuando se configure el proyecto.

---

10. Entorno de desarrollo

Decisión

Utilizar el entorno virtual integrado de Python:

venv

Objetivo

Aislar las dependencias del proyecto del Python del sistema.

Ejemplo:

python3 -m venv .venv

Las instrucciones exactas de creación y uso del entorno estarán documentadas en "development.md".

---

11. Gestión del proyecto

Decisión

Utilizar:

Git
GitHub

para control de versiones y alojamiento del repositorio.

El código principal se desarrollará sobre:

main

Las decisiones de desarrollo, pruebas y contribución se documentarán por separado.

---

12. Configuración del proyecto

Decisión

Utilizar:

pyproject.toml

como archivo central de configuración del proyecto Python.

Podrá contener:

- Metadatos del proyecto.
- Dependencias.
- Configuración de herramientas.
- Configuración de testing.
- Configuración de linting y formato.

La estructura exacta se definirá cuando comience la implementación.

---

13. Integración continua

Decisión

Utilizar:

GitHub Actions

para automatización del proceso de integración continua.

Comprobaciones iniciales

El pipeline podrá ejecutar:

pytest
ruff
mypy

según la configuración final del proyecto.

El objetivo es detectar problemas antes de integrar cambios en "main".

---

14. Distribución

Decisión

La aplicación se distribuirá inicialmente mediante:

.deb

Objetivo

El usuario final debería poder instalar la aplicación como un programa normal de escritorio, sin necesitar:

- Clonar el repositorio.
- Instalar Python manualmente.
- Crear un entorno virtual.
- Instalar dependencias mediante "pip".

El paquete deberá incluir los elementos necesarios para ejecutar la aplicación instalada.

Decisión pendiente

La estrategia exacta para construir el ".deb" todavía no se fija.

Se evaluarán posteriormente alternativas como:

- Empaquetado nativo de Debian.
- Empaquetado de la aplicación Python junto con sus dependencias.
- Uso de herramientas específicas de packaging.

La elección se realizará cuando exista una implementación funcional que permita evaluar correctamente las opciones.

---

15. Instalación y desinstalación

Decisión

La aplicación deberá comportarse como un programa instalado en el sistema.

La instalación deberá proporcionar, como mínimo:

- Archivos necesarios de la aplicación.
- Acceso mediante el menú de aplicaciones.
- Lanzador ".desktop".
- Icono de la aplicación.

La desinstalación deberá eliminar los archivos instalados por la aplicación sin borrar archivos personales del usuario.

Los detalles concretos de rutas y empaquetado se definirán durante la implementación del ".deb".

---

16. Ejecución en segundo plano

Decisión

Las operaciones largas de APT no se ejecutarán directamente en el hilo principal de la interfaz gráfica.

Objetivo

Evitar que la ventana:

- Se congele.
- Deje de responder.
- Parezca bloqueada mientras APT trabaja.

La implementación utilizará los mecanismos proporcionados por Qt/PySide6 para ejecutar y supervisar las tareas en segundo plano.

La elección concreta entre "QThread", "QProcess" u otro mecanismo apropiado se realizará durante la implementación según las necesidades reales.

---

17. Compatibilidad inicial

Decisión

El objetivo inicial será:

Debian
Ubuntu
Linux Mint
Otros derivados compatibles con APT

La compatibilidad real dependerá de:

- Disponibilidad de "apt-get".
- Disponibilidad del mecanismo de privilegios requerido.
- Estructura del sistema.
- Compatibilidad con las dependencias de la aplicación.

No se afirmará compatibilidad universal con todas las distribuciones Linux.

---

18. Arquitectura y dependencias

La aplicación mantendrá una separación básica entre:

Presentation
Application
Core
Infrastructure

La GUI no ejecutará directamente comandos del sistema.

Las operaciones de APT y la interacción con Linux estarán concentradas en Infrastructure.

Las reglas y coordinación de las operaciones estarán en Application.

Core se mantendrá pequeño y solamente contendrá modelos o contratos que aporten valor real.

No se crearán abstracciones únicamente por seguir un patrón arquitectónico.

---

19. Principios de seguridad

El proyecto seguirá estos principios:

Mínimo privilegio

Solicitar permisos administrativos solamente cuando sean necesarios.

Sin ejecución arbitraria

El usuario no podrá introducir comandos arbitrarios para que la aplicación los ejecute.

Argumentos controlados

Los comandos se construirán utilizando argumentos controlados y estructurados.

Sin credenciales almacenadas

La aplicación no almacenará contraseñas administrativas.

Sin ejecución completa como root

La GUI y la lógica general no deberán ejecutarse como "root" innecesariamente.

Transparencia

La aplicación deberá informar al usuario qué operación está ejecutando y cuál fue su resultado.

---

20. Decisiones pendientes

Las siguientes decisiones no necesitan resolverse antes de comenzar la implementación:

- Estrategia definitiva de empaquetado ".deb".
- Estructura definitiva del paquete instalado.
- Mecanismo concreto de ejecución en segundo plano en Qt.
- Nivel exacto de cobertura de tests de integración con APT.
- Configuración definitiva de "mypy".
- Compatibilidad detallada con diferentes versiones de Debian/Ubuntu/Linux Mint.

Estas decisiones se tomarán cuando exista suficiente información técnica para elegirlas correctamente.

---

21. Criterio para modificar decisiones

Una decisión técnica podrá cambiarse cuando:

1. Aparezca una necesidad real.
2. La solución actual genere un problema.
3. Una alternativa proporcione una mejora significativa.
4. La nueva solución sea compatible con los requisitos del proyecto.
5. El cambio quede documentado.

El objetivo no es mantener decisiones por obligación, sino utilizar las soluciones más adecuadas para el proyecto.

---

22. Resumen de tecnologías

Área| Tecnología
Lenguaje| Python 3.12+
GUI| PySide6 / Qt 6
Procesos| "subprocess"
Gestor de paquetes| APT / "apt-get"
Privilegios| "pkexec"
Logging| "logging"
Testing| pytest
Lint / formato| Ruff
Type checking| mypy
Entorno| "venv"
Configuración| "pyproject.toml"
Control de versiones| Git
Repositorio| GitHub
CI| GitHub Actions
Distribución| ".deb"

---

23. Relación con otros documentos

Este documento complementa:

- "requirements.md" — define qué debe hacer el sistema.
- "business-rules.md" — define las reglas que debe respetar.
- "use-cases.md" — describe las principales interacciones y operaciones.
- "architecture.md" — define cómo se organiza la aplicación.
- "development.md" — definirá cómo desarrollar el proyecto.
- "testing.md" — definirá cómo verificar el comportamiento del sistema.

Este documento se centra específicamente en las decisiones tecnológicas y criterios técnicos.