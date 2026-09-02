# Arquitectura

## Estilo Arquitectónico

Arquitectura por capas con separación de responsabilidades:
┌─────────────────────────────────────────────────────┐
│ Presentation │
│ GUI (PySide6) │
└────────────────────┬────────────────────────────────┘
│
▼
┌─────────────────────────────────────────────────────┐
│ Application │
│ Use Cases │
└────────────────────┬────────────────────────────────┘
│
▼
┌─────────────────────────────────────────────────────┐
│ Core │
│ Entities / Ports │
└────────────────────┬────────────────────────────────┘
│
▼
┌─────────────────────────────────────────────────────┐
│ Infrastructure │
│ APT / Process / Privileges │
└────────────────────┬────────────────────────────────┘
│
▼
┌─────────────────────────────────────────────────────┐
│ Linux / apt-get │
└─────────────────────────────────────────────────────┘

text

## Capas

### Presentation (GUI)

- Interfaz gráfica con PySide6
- Muestra información y estados
- Recibe acciones del usuario
- **No ejecuta** APT directamente

### Application

- Casos de uso (UpdateSystem, Autoremove, Clean)
- Coordina flujos de operaciones
- Implementa reglas de negocio

### Core

- Entidades: Operation, OperationResult
- Estados: PENDING, RUNNING, SUCCESS, FAILED, CANCELLED
- Interfaces/Ports: APTProvider, CommandRunner, PrivilegeProvider
- **Independiente** de frameworks y sistema operativo

### Infrastructure

- **APT**: Operaciones con apt-get
- **Process**: Ejecución de comandos con subprocess
- **Privileges**: Elevación mediante pkexec
- **Logging**: Sistema de logs

## Componentes Principales

### OperationResult

Representa el resultado de una operación:

- `status`: SUCCESS / FAILED / CANCELLED
- `exit_code`: int
- `stdout`: str
- `stderr`: str
- `message`: str

### APTService (implementa APTProvider)

Métodos:

- `update() → OperationResult`
- `upgrade() → OperationResult`
- `autoremove() → OperationResult`
- `clean() → OperationResult`

### CommandRunner

- `run(command: list[str]) → CommandResult`

### PrivilegeManager

- `execute_privileged(command: list[str]) → CommandResult`

## Flujo de Actualización (CU-03)

Usuario → GUI (botón "Actualizar")
→ UpdateSystemUseCase.execute()
→ PrivilegeManager.execute_privileged()
→ APTService.update()
→ CommandRunner.run(["apt-get", "update"])
→ Resultado
→ ¿Éxito? → APTService.upgrade()
→ Resultado final
→ GUI muestra resultado

text

## Flujo de Errores

APT → Error → Infrastructure detecta error
→ Application recibe OperationResult(FAILED)
→ GUI muestra mensaje de error

text

## Reglas de Negocio (RN)

### RN-01: Actualización Secuencial

`update` debe ejecutarse antes que `upgrade`. Si `update` falla, `upgrade` no se ejecuta.

### RN-02: Resultado Real

El éxito se determina por el código de salida, no por mensajes de texto.

### RN-03: Operaciones Independientes

`autoremove` y `clean` pueden ejecutarse sin `update`/`upgrade`.

### RN-04: Sin Operaciones Posteriores tras Fallo

Si una operación en secuencia falla, las dependientes no se ejecutan.

### RN-05: Privilegios Mínimos

Solicitar privilegios solo cuando la operación lo requiera.

### RN-06: Sin Privilegios, Sin Operación

No ejecutar operación administrativa si no se obtuvieron privilegios.

### RN-07: Cancelación

Si el usuario cancela autenticación, resultado = CANCELLED.

### RN-08: Fallo de Elevación

Informar fallo si el mecanismo de elevación falla.

### RN-09: Dependencia de APT

`apt-get` debe estar disponible.

### RN-10: Ejecución Controlada

Usar argumentos estructurados (no concatenación de strings).

### RN-11: Código de Salida

Evaluar código de salida y capturar stdout/stderr.

### RN-12: La GUI no contiene lógica de APT

La GUI solo pide acciones, el Core las ejecuta.

### RN-13: La GUI no debe bloquearse

Operaciones APT en hilo separado.

### RN-14: Estado Consistente

No permitir iniciar múltiples operaciones simultáneas.

### RN-15: Resultado Explícito

Cada operación termina en SUCCESS, FAILED o CANCELLED.

### RN-16: Autoremove

Delegar completamente en APT.

### RN-17: Clean

Delegar completamente en APT.

### RN-18: No Ejecución Arbitraria

El usuario no puede introducir comandos arbitrarios.

### RN-19: Separación de Privilegios

GUI y lógica general no se ejecutan como root.

### RN-20: Sin Credenciales

No almacenar ni registrar contraseñas.

### RN-21: Registrar Operaciones Importantes

Inicio, ejecución, resultado, error, cancelación.

### RN-22: No Registrar Información Sensible

Logs sin contraseñas, tokens ni credenciales.

### RN-23: Instalación Mediante Paquete

Distribución en .deb.

### RN-24: Aplicación Independiente del Entorno de Desarrollo

El usuario final no necesita Python, pip, clonar repositorio, etc.

### RN-25: Desinstalación Limpia

Eliminar componentes propios sin afectar archivos personales.

## Estructura de Directorios

actualizar-linux/
├── src/
│ └── actualizar_linux/
│ ├── presentation/
│ │ └── gui/
│ ├── application/
│ │ ├── update_system.py
│ │ ├── autoremove.py
│ │ └── clean.py
│ ├── core/
│ │ ├── operation.py
│ │ ├── result.py
│ │ └── interfaces/
│ │ ├── apt.py
│ │ ├── command_runner.py
│ │ └── privileges.py
│ ├── infrastructure/
│ │ ├── apt/
│ │ ├── process/
│ │ ├── privileges/
│ │ └── logging/
│ └── main.py
├── tests/
│ ├── unit/
│ └── integration/
├── packaging/
│ └── deb/
├── docs/
├── .github/
│ └── workflows/
├── pyproject.toml
├── README.md
└── .gitignore
