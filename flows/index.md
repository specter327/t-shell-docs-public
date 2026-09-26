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

[Flujo Operativo](operative-flow.md)

Compone la relación operacional entre Atlas y Echo utilizando los modelos de la Capa Operativa y las plataformas de Authentication, Linkage, Transport, Module y System Adapter.

## Flujo Administrativo

Lease: [Flujo Administrativo](administrative-flow.md)

Extiende la operación con Usuario, Sesión y Asignación para proporcionar administración controlada sobre Dominios y Entidades.

## Flujo de Negocio

Lease: [Flujo de Negocio](business-flow.md)

Extiende la capa administrativa con Cliente, Plan, Suscripción y Facturación.

## Flujo de Aplicaciones

Lease: [Flujo de Aplicaciones](applications-flow.md)

Describe cómo Atlas, Echo y Codex convergen para materializar actualmente las responsabilidades documentadas del sistema.
