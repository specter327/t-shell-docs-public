# Flujos de T-Shell

Los Flujos de T-Shell describen **cómo convergen** modelos, aplicaciones y plataformas ya definidos.

Un Flow:

- posee secuencia, composición, causalidad, precondiciones, resultados y fallos;
- puede consumir múltiples fuentes normativas mediante `Lease`;
- NO adquiere autoridad para redefinir los conceptos consumidos;
- NO sustituye los modelos, aplicaciones ni plataformas que lo componen.

Relación conceptual:

```text
Flow <- Models / Applications / Platforms
```

Interpretado como autoridad:

```text
Sources of Truth
      ↓
     Flow
```

Interpretado como dependencia:

```text
Flow
  ↓ depends on
Sources of Truth
```

## Flujo Operativo

Lease: [[operative-flow|Flujo Operativo]]

Compone la relación operacional entre Atlas y Echo utilizando los modelos de la Capa Operativa y las plataformas de Authentication, Linkage, Transport, Module y System Adapter.

## Flujo Administrativo

Lease: [[administrative-flow|Flujo Administrativo]]

Extiende la operación con Usuario, Sesión y Asignación para proporcionar administración controlada sobre Dominios y Entidades.

## Flujo de Negocio

Lease: [[business-flow|Flujo de Negocio]]

Extiende la capa administrativa con Cliente, Plan, Suscripción y Facturación.

## Flujo de Aplicaciones

Lease: [[applications-flow|Flujo de Aplicaciones]]

Describe cómo Atlas, Echo y Codex convergen para materializar actualmente las responsabilidades documentadas del sistema.
