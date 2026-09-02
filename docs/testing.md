Testing — Actualizar-Linux v2.0

1. Propósito

Este documento define la estrategia de pruebas para Actualizar-Linux v2.0.

El objetivo es verificar que:

- Las operaciones funcionen correctamente.
- Las reglas de negocio se cumplan.
- Los errores sean gestionados correctamente.
- La aplicación maneje adecuadamente la cancelación de privilegios.
- La GUI permanezca responsiva durante operaciones largas.
- Los componentes puedan probarse de forma aislada.
- Los cambios importantes puedan validarse automáticamente.

La estrategia de testing será proporcional al tamaño y complejidad del proyecto.

---

2. Herramientas

La herramienta principal para las pruebas automatizadas será:

pytest

También se utilizarán las herramientas definidas en las decisiones técnicas:

Ruff
mypy
GitHub Actions

Estas herramientas complementan las pruebas, pero no sustituyen las pruebas funcionales y de integración.

---

3. Tipos de pruebas

El proyecto utilizará principalmente:

Pruebas unitarias
Pruebas de integración
Pruebas funcionales
Pruebas manuales

No se pretende implementar desde el inicio una estrategia excesivamente compleja.

---

4. Pruebas unitarias

Las pruebas unitarias comprobarán componentes de forma aislada.

Se utilizarán principalmente para comprobar:

- Reglas de negocio.
- Casos de uso.
- Estados de las operaciones.
- Resultados.
- Manejo de errores.
- Construcción controlada de comandos.
- Decisiones de flujo.

Cuando un componente dependa de APT, Linux o privilegios administrativos, se podrán utilizar mocks, stubs o fakes para aislar la lógica.

Ejemplo

Para una actualización completa:

apt-get update → SUCCESS
apt-get upgrade → debe ejecutarse

Mientras que:

apt-get update → FAILED
apt-get upgrade → no debe ejecutarse

Este comportamiento debe poder comprobarse sin realizar una actualización real del sistema.

---

5. Pruebas de integración

Las pruebas de integración comprobarán la interacción entre componentes reales.

Podrán incluir:

- Ejecución de procesos.
- Integración con APT.
- Obtención de códigos de salida.
- Captura de salida estándar y errores.
- Integración con el mecanismo de privilegios.
- Integración entre Application e Infrastructure.

Estas pruebas se ejecutarán en un entorno Linux controlado.

No todas las pruebas de integración deberán ejecutarse sobre el sistema principal del usuario.

---

6. Pruebas funcionales

Las pruebas funcionales comprobarán el comportamiento de la aplicación desde la perspectiva del usuario.

Se verificará, entre otras cosas:

- La aplicación inicia correctamente.
- La GUI muestra las operaciones disponibles.
- Una operación puede iniciarse.
- El estado de la operación se muestra correctamente.
- La salida relevante de APT se muestra al usuario.
- Los errores se muestran correctamente.
- La cancelación de privilegios se comunica correctamente.
- La aplicación permanece utilizable durante operaciones largas.

---

7. Pruebas manuales

Las pruebas manuales serán necesarias para aspectos difíciles de validar completamente mediante pruebas automatizadas.

Especialmente:

- Interfaz gráfica.
- Apariencia y usabilidad.
- Solicitud de privilegios.
- Cancelación de autenticación.
- Ejecución real de APT.
- Instalación del ".deb".
- Lanzador de la aplicación.
- Desinstalación.

Las pruebas manuales no sustituyen las pruebas automatizadas.

---

8. Pruebas de las operaciones APT

Cada operación principal deberá verificarse individualmente.

Actualizar repositorios

Comando esperado:

apt-get update

Debe comprobarse:

- Ejecución correcta.
- Resultado exitoso.
- Captura de salida.
- Manejo de errores.
- Código de salida.

Actualizar paquetes

Comando esperado:

apt-get upgrade

Debe comprobarse:

- Ejecución correcta.
- Resultado exitoso.
- Manejo de errores.
- Código de salida.

Eliminar paquetes innecesarios

Comando esperado:

apt-get autoremove

Debe comprobarse:

- Ejecución correcta.
- Resultado.
- Manejo de errores.

Limpiar caché

Comando esperado:

apt-get clean

Debe comprobarse:

- Ejecución correcta.
- Resultado.
- Manejo de errores.

---

9. Pruebas de actualización completa

La actualización completa tiene una dependencia entre operaciones.

Flujo esperado:

apt-get update
      │
      ├── SUCCESS ──► apt-get upgrade
      │
      └── FAILED ──► detener

Se deberán comprobar como mínimo estos escenarios:

Escenario 1 — Todo correcto

update → SUCCESS
upgrade → SUCCESS

Resultado final → SUCCESS

Escenario 2 — Update falla

update → FAILED
upgrade → NO EJECUTAR

Resultado final → FAILED

Escenario 3 — Upgrade falla

update → SUCCESS
upgrade → FAILED

Resultado final → FAILED

---

10. Pruebas de cancelación de privilegios

Debe comprobarse el comportamiento cuando el usuario cancela la solicitud de privilegios.

Resultado esperado:

Solicitud de privilegios
          │
          ▼
      CANCELADA
          │
          ▼
Operación no ejecutada
          │
          ▼
     CANCELLED

La aplicación no deberá interpretar esta situación como un fallo interno.

---

11. Pruebas de fallo de privilegios

También deberá comprobarse el caso en que el mecanismo de elevación falle.

Resultado esperado:

Solicitud de privilegios
          │
          ▼
       ERROR
          │
          ▼
       FAILED

La aplicación deberá informar al usuario de que la operación no pudo ejecutarse debido al problema de permisos.

---

12. Pruebas de APT no disponible

Debe comprobarse el comportamiento cuando "apt-get" no está disponible.

Resultado esperado:

APT no disponible
       │
       ▼
Operación no ejecutada
       │
       ▼
     FAILED

La aplicación deberá proporcionar información útil al usuario.

---

13. Pruebas de códigos de salida

El éxito de una operación no se determinará únicamente analizando el texto mostrado por APT.

Las pruebas deberán comprobar que:

- Código de salida exitoso → operación exitosa cuando corresponda.
- Código de salida diferente de cero → operación fallida.
- Fallos de ejecución del proceso → operación fallida.

La interpretación exacta dependerá del componente encargado de ejecutar el proceso.

---

14. Pruebas de estados

Los estados definidos para las operaciones deberán comportarse correctamente:

PENDING
RUNNING
SUCCESS
FAILED
CANCELLED

Deberán comprobarse las transiciones válidas.

Ejemplo:

PENDING
   ↓
RUNNING
   ↓
SUCCESS

O:

PENDING
   ↓
RUNNING
   ↓
FAILED

O:

PENDING
   ↓
CANCELLED

No deberán producirse estados inconsistentes.

---

15. Pruebas de comandos

Los comandos enviados al sistema deberán comprobarse para garantizar que:

- El comando sea el esperado.
- Los argumentos sean los esperados.
- No existan argumentos arbitrarios proporcionados por el usuario.
- No se utilice shell innecesariamente.
- Las operaciones no puedan convertirse en ejecución arbitraria de comandos.

Ejemplo esperado:

["apt-get", "update"]

No se deberá construir innecesariamente el comando mediante concatenación de texto.

---

16. Pruebas de la GUI

La GUI deberá comprobarse funcionalmente.

Como mínimo:

- La ventana inicia correctamente.
- Los controles principales aparecen.
- Las operaciones pueden iniciarse.
- Los controles cambian de estado cuando corresponde.
- Los resultados se muestran.
- Los errores se muestran.
- La interfaz no se bloquea durante operaciones largas.

Las pruebas de apariencia visual podrán mantenerse principalmente como pruebas manuales.

---

17. Pruebas de responsividad

Las operaciones largas no deberán bloquear el hilo principal de la GUI.

Debe comprobarse que mientras una operación está ejecutándose:

- La ventana continúa respondiendo.
- El usuario puede visualizar el estado.
- La interfaz no queda congelada.
- El resultado se comunica cuando termina el proceso.

La implementación concreta de esta funcionalidad dependerá del mecanismo utilizado por PySide6/Qt.

---

18. Pruebas de operaciones simultáneas

La aplicación deberá evitar operaciones incompatibles ejecutándose simultáneamente.

Por ejemplo, no debería ser posible iniciar una segunda actualización completa mientras otra actualización de APT continúa ejecutándose.

Debe comprobarse que:

Operación A → RUNNING
        │
        ▼
Operación B → rechazada/bloqueada

La respuesta concreta de la interfaz se definirá durante la implementación.

---

19. Pruebas de logging

Debe comprobarse que los eventos importantes sean registrados.

Como mínimo:

- Inicio de operación.
- Resultado.
- Error.
- Cancelación.
- Información relevante del proceso.

También debe comprobarse que los logs no contengan:

- Contraseñas.
- Credenciales.
- Información sensible innecesaria.

---

20. Pruebas de instalación

El paquete ".deb" deberá probarse en un entorno Linux compatible.

Se comprobará:

- Instalación correcta.
- Archivos instalados.
- Lanzador ".desktop".
- Icono.
- Inicio de la aplicación.
- Disponibilidad de las dependencias necesarias.

El usuario final no debería necesitar configurar manualmente Python para utilizar la aplicación instalada.

---

21. Pruebas de desinstalación

La desinstalación deberá comprobar:

- Eliminación correcta de los archivos de la aplicación.
- Eliminación del lanzador instalado.
- Eliminación de los elementos propios del paquete.
- No eliminación de archivos personales del usuario.

También deberá comprobarse que la desinstalación finalice correctamente cuando sea ejecutada desde el sistema de paquetes.

---

22. Pruebas de reinstalación

Cuando el sistema de empaquetado esté implementado, deberá comprobarse:

Instalar
   ↓
Ejecutar
   ↓
Desinstalar
   ↓
Instalar nuevamente
   ↓
Ejecutar

La aplicación deberá continuar funcionando correctamente después de una reinstalación.

---

23. Pruebas de regresión

Cuando se corrija un error, deberá añadirse una prueba cuando sea razonable hacerlo.

Ejemplo:

Error encontrado
      ↓
Corrección
      ↓
Prueba que reproduce el error
      ↓
Corrección validada

Esto evita que el mismo problema vuelva a aparecer posteriormente.

---

24. Pruebas en CI

GitHub Actions ejecutará automáticamente las comprobaciones configuradas.

Como mínimo se pretende ejecutar:

pytest

y las herramientas de calidad configuradas para el proyecto:

ruff check .
ruff format --check .
mypy .

La configuración exacta podrá cambiar durante el desarrollo.

---

25. Entorno de pruebas

Las pruebas que interactúen directamente con Linux o APT deberán realizarse preferiblemente en un entorno controlado.

Se podrán utilizar:

- Máquina virtual.
- Instalación de pruebas.
- Sistema Linux dedicado.
- Contenedores cuando sean adecuados para la prueba concreta.

Las operaciones potencialmente modificadoras del sistema no deberán ejecutarse indiscriminadamente durante las pruebas automatizadas.

---

26. Criterios de aceptación

Una funcionalidad podrá considerarse suficientemente validada cuando:

1. Las pruebas unitarias relacionadas sean correctas.
2. Las pruebas de integración necesarias sean correctas.
3. Las pruebas funcionales relevantes sean correctas.
4. Los casos de error importantes hayan sido comprobados.
5. Las reglas de negocio relacionadas se cumplan.
6. No existan errores conocidos que bloqueen el funcionamiento.
7. Las comprobaciones automáticas del proyecto sean satisfactorias.

No todas las funcionalidades requieren exactamente el mismo nivel de pruebas.

---

27. Cobertura

El proyecto buscará una buena cobertura de la lógica importante, especialmente:

- Casos de uso.
- Reglas de negocio.
- Manejo de errores.
- Estados de operaciones.
- Ejecución controlada de comandos.

No se establecerá inicialmente un porcentaje obligatorio de cobertura.

La cobertura será utilizada como una métrica de apoyo y no como el único criterio para determinar la calidad de las pruebas.

---

28. Principios de testing

Las pruebas del proyecto seguirán estos principios:

Probar comportamiento

Las pruebas deben comprobar qué hace el sistema, no solamente cómo está implementado.

Aislar cuando sea posible

La lógica debe poder probarse sin depender constantemente de APT o de privilegios reales.

Probar escenarios de error

No se probará únicamente el camino exitoso.

Probar reglas importantes

Las reglas de negocio deberán tener pruebas que permitan detectar regresiones.

Mantener las pruebas comprensibles

Las pruebas deben ser fáciles de leer y mantener.

Evitar pruebas innecesariamente complejas

El sistema de pruebas debe ser proporcional al tamaño del proyecto.

---

29. Relación con otros documentos

Este documento utiliza como referencia:

- "requirements.md" — requisitos que deben verificarse.
- "business-rules.md" — reglas que deben cumplirse.
- "use-cases.md" — escenarios principales que deben probarse.
- "architecture.md" — componentes y responsabilidades.
- "technical-decisions.md" — herramientas y decisiones técnicas.
- "development.md" — flujo de desarrollo y verificaciones.

Las pruebas deben mantenerse alineadas con estos documentos cuando el comportamiento del sistema cambie.

---

30. Evolución de la estrategia

La estrategia de testing podrá evolucionar a medida que el proyecto crezca.

Inicialmente se priorizarán:

Pruebas unitarias
        ↓
Pruebas de integración esenciales
        ↓
Pruebas funcionales
        ↓
Pruebas manuales

Si el proyecto aumenta su complejidad, podrán incorporarse posteriormente otras técnicas de testing.

No se añadirá complejidad al sistema de pruebas sin una necesidad real.