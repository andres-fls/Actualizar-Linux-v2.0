Arquitectura — Actualizar-Linux v2.0

1. Propósito

Actualizar-Linux v2.0 utilizará una arquitectura modular y sencilla, adecuada para el tamaño y objetivo del proyecto.

La arquitectura busca:

- Separar la interfaz gráfica de la lógica de las operaciones.
- Evitar que la GUI ejecute directamente comandos del sistema.
- Facilitar las pruebas.
- Mantener el código organizado.
- Permitir cambiar o ampliar componentes sin modificar todo el proyecto.
- Evitar abstracciones innecesarias.

La arquitectura podrá simplificarse durante la implementación si alguna separación demuestra no aportar valor real al proyecto.

---

2. Estructura general

La aplicación se organizará conceptualmente en cuatro partes:

┌──────────────────────────────┐
│       Presentation           │
│          PySide6             │
│        Interfaz GUI          │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│        Application           │
│          Casos de uso        │
│     Lógica de operaciones    │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│            Core              │
│     Reglas y contratos       │
│   Modelos e interfaces       │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│       Infrastructure         │
│ APT / procesos / privilegios │
│           logging            │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│        Linux / APT           │
│       apt-get / pkexec       │
└──────────────────────────────┘

Esta división es conceptual. No se pretende implementar una arquitectura excesivamente compleja.

---

3. Presentation

La capa "Presentation" contiene la interfaz gráfica de usuario.

Responsabilidades

- Mostrar la ventana principal.
- Mostrar las operaciones disponibles.
- Permitir al usuario iniciar una operación.
- Mostrar el estado de la operación.
- Mostrar el resultado y la salida relevante.
- Informar errores y cancelaciones.
- Mantener la interfaz responsiva durante operaciones largas.

Tecnología

- PySide6
- Qt 6

Restricciones

La interfaz gráfica no debe:

- Ejecutar directamente comandos "apt-get".
- Ejecutar directamente "pkexec".
- Contener las reglas de actualización.
- Determinar por sí misma si una operación tuvo éxito.
- Contener lógica específica de APT.

La GUI solicita una operación y presenta su resultado.

---

4. Application

La capa "Application" contiene los casos de uso de la aplicación.

Es la encargada de coordinar las operaciones.

Responsabilidades

- Implementar los casos de uso.
- Coordinar las operaciones necesarias.
- Controlar el orden de ejecución.
- Determinar qué operación debe ejecutarse.
- Gestionar los resultados recibidos de Infrastructure.
- Mantener separada la lógica de operación de la interfaz gráfica.

Ejemplo

La actualización completa requiere:

apt-get update
       │
       ├── éxito ──► apt-get upgrade
       │
       └── fallo ──► detener operación

La capa "Application" controla esta secuencia.

---

5. Core

La capa "Core" contiene los elementos básicos utilizados por la aplicación.

Debe mantenerse pequeña.

Puede contener

- Estados de una operación.
- Resultados.
- Modelos simples.
- Interfaces necesarias para desacoplar la lógica de las implementaciones concretas.

Ejemplo:

core/
├── operation.py
├── result.py
└── interfaces/
    ├── apt.py
    └── privileges.py

Estados de operación

Las operaciones podrán utilizar estados como:

PENDING
RUNNING
SUCCESS
FAILED
CANCELLED

Estos estados permiten representar claramente el ciclo de vida de una operación.

Resultados

Un resultado de operación podrá contener información como:

- Estado.
- Código de salida.
- Mensaje.
- Salida estándar.
- Error o salida de error.

El modelo exacto se definirá durante la implementación.

---

6. Infrastructure

La capa "Infrastructure" contiene las implementaciones que interactúan directamente con el sistema operativo.

Componentes principales

infrastructure/
├── apt/
├── process/
├── privileges/
└── logging/

APT

Será responsable de ejecutar las operaciones relacionadas con APT:

apt-get update
apt-get upgrade
apt-get autoremove
apt-get clean

Process

Será responsable de la ejecución controlada de procesos mediante "subprocess".

Privileges

Será responsable de solicitar privilegios administrativos cuando una operación los necesite.

El mecanismo principal será:

pkexec

No se ejecutará toda la aplicación como "root".

Logging

Será responsable del registro de eventos importantes utilizando el módulo estándar:

logging

---

7. Flujo general

El flujo normal de una operación será:

Usuario
   │
   ▼
GUI
   │
   ▼
Caso de uso
   │
   ▼
Infrastructure
   │
   ├──► privilegios
   │
   └──► apt-get
          │
          ▼
        Linux
          │
          ▼
       Resultado
          │
          ▼
      Caso de uso
          │
          ▼
          GUI
          │
          ▼
       Usuario

La GUI no necesita conocer los detalles internos de cómo se ejecuta APT.

---

8. Flujo de privilegios

Las operaciones que requieren permisos administrativos solicitarán privilegios únicamente cuando sea necesario.

Conceptualmente:

Usuario inicia operación
          │
          ▼
¿Necesita privilegios?
       /       \
     No         Sí
     │           │
     ▼           ▼
Ejecutar      Solicitar
directamente  privilegios
                  │
             ┌────┴────┐
             │         │
          Acepta    Cancela/falla
             │         │
             ▼         ▼
          Ejecutar   CANCELLED
          operación  o FAILED

La aplicación no almacenará ni registrará contraseñas.

---

9. Operaciones largas

Las operaciones de APT pueden tardar varios minutos.

Por esta razón, las operaciones largas no deben ejecutarse directamente en el hilo principal de la GUI.

El objetivo es:

GUI
 │
 ├── permanece responsiva
 │
 └── operación en segundo plano
          │
          ▼
       apt-get

PySide6/Qt proporcionará los mecanismos necesarios para ejecutar estas tareas sin bloquear la interfaz.

La implementación concreta se decidirá durante el desarrollo.

---

10. Dependencias entre componentes

La dirección conceptual de las dependencias será:

Presentation
      ↓
Application
      ↓
Core
      ↑
Infrastructure

La idea principal es que:

- "Presentation" depende de "Application".
- "Application" utiliza los modelos y contratos necesarios de "Core".
- "Infrastructure" implementa las interfaces que necesite "Core".
- "Infrastructure" es la parte que conoce detalles concretos de Linux, APT, "subprocess", "pkexec" y logging.

La GUI no debe depender directamente de los detalles de infraestructura.

---

11. Estructura inicial del proyecto

La estructura inicial propuesta será:

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

Esta estructura es una propuesta inicial y podrá modificarse durante la implementación si alguna parte resulta innecesaria.

No se crearán módulos o clases únicamente para cumplir una estructura teórica.

---

12. Principios arquitectónicos

Actualizar-Linux v2.0 seguirá estos principios:

1. Simplicidad
   La arquitectura debe ser proporcional al tamaño del proyecto.

2. Separación de responsabilidades
   La GUI, los casos de uso y la interacción con el sistema no deben mezclarse innecesariamente.

3. Seguridad
   Los comandos deben ejecutarse de forma controlada y con los privilegios mínimos necesarios.

4. Testabilidad
   La lógica importante debe poder probarse sin depender constantemente de una instalación real de Linux.

5. Mantenibilidad
   El código debe ser comprensible y fácil de modificar.

6. No sobreingeniería
   No se crearán capas, patrones o abstracciones que no aporten una necesidad concreta.

7. Evolución gradual
   La arquitectura podrá cambiar si las necesidades reales del proyecto lo requieren.

La arquitectura es una guía para organizar el código, no una restricción que obligue a mantener estructuras innecesarias.