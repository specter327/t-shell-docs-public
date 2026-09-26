# Flujos de T-Shell

Los flujos de T-Shell describen **cómo convergen** modelos, aplicaciones y plataformas ya definidos.

Un flujo:

- posee secuencia, composición, causalidad, precondiciones, resultados y fallos;
- puede consumir múltiples fuentes normativas mediante un `Lease`;
- no adquiere autoridad para redefinir los conceptos consumidos;
- no sustituye los modelos, aplicaciones ni plataformas que lo componen.

Relación conceptual:

```text
Flujo <- Modelos / Aplicaciones / Plataformas
```

Interpretado como autoridad:

```text
Fuentes de verdad
      ↓
    Flujo
```

Interpretado como dependencia:

```text
Flujo
  ↓ depende de
Fuentes de verdad
```

## Flujo Operativo

[Flujo Operativo](operative-flow.md)

Compone la relación operacional entre Atlas y Echo utilizando los modelos de la Capa Operativa y las plataformas de Autenticación, Enlace, Transporte, Módulos y Adaptación del Sistema.

## Flujo Administrativo

[Flujo Administrativo](administrative-flow.md)

Extiende la operación con Usuario, Sesión y Asignación para proporcionar administración controlada sobre Dominios y Entidades.

## Flujo de Negocio

[Flujo de Negocio](business-flow.md)

Extiende la capa administrativa con Cliente, Plan, Suscripción y Facturación.

## Flujo de Aplicaciones

[Flujo de Aplicaciones](applications-flow.md)

Describe cómo Atlas, Echo y Codex convergen para materializar actualmente las responsabilidades documentadas del sistema.
