# Documentación de Actualizar-Linux v2.0

Esta carpeta contiene la documentación técnica y de diseño de Actualizar-Linux v2.0.

La documentación se utiliza como referencia durante el desarrollo del proyecto y debe mantenerse alineada con la implementación y las pruebas.

## Documentos

### Requisitos

[`requirements.md`](requirements.md)

Define los requisitos funcionales y no funcionales del sistema, además de las restricciones y el alcance de la versión inicial.

### Reglas de negocio

[`business-rules.md`](business-rules.md)

Define las reglas que determinan el comportamiento del sistema durante las diferentes operaciones.

### Casos de uso

[`use-cases.md`](use-cases.md)

Describe las interacciones entre los actores y el sistema, incluyendo los flujos principales y alternativos.

### Arquitectura

[`architecture.md`](architecture.md)

Describe la estructura del sistema, sus capas, componentes, responsabilidades y dependencias.

### Decisiones técnicas

[`technical-decisions.md`](technical-decisions.md)

Documenta las principales decisiones técnicas del proyecto y las razones por las que fueron adoptadas.

### Desarrollo

[`development.md`](development.md)

Describe cómo preparar el entorno de desarrollo, instalar dependencias, ejecutar el proyecto y utilizar las herramientas de desarrollo.

### Pruebas

[`testing.md`](testing.md)

Define la estrategia de pruebas, los niveles de testing, los tipos de pruebas y los criterios utilizados para validar el sistema.

## Organización de la documentación

La documentación sigue una separación de responsabilidades:

```text
requirements.md
        │
        ├── ¿Qué debe hacer el sistema?
        │
        ▼
business-rules.md
        │
        ├── ¿Qué reglas debe respetar?
        │
        ▼
use-cases.md
        │
        ├── ¿Cómo interactúan los actores con el sistema?
        │
        ▼
architecture.md
        │
        ├── ¿Cómo se estructura técnicamente?
        │
        ▼
technical-decisions.md
        │
        ├── ¿Por qué se tomaron determinadas decisiones?
        │
        ▼
development.md / testing.md
        │
        └── ¿Cómo se desarrolla y valida?
