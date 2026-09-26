# Flujo de Negocio

## Propósito

El Flujo de Negocio describe cómo la **Capa Business** compone Cliente, Plan, Suscripción y Facturación sobre las capas Administrativa y Operativa.

Su función es expresar la secuencia y relaciones comerciales documentadas actualmente, sin introducir reglas de cobro, renovación, enforcement o provisión que todavía no estén definidas por los modelos.

## Composición documental

Lease:

- [[architecture/layers/business/index|Capa Business]]
- [[architecture/layers/business/client/index|Modelo de Cliente]]
- [[architecture/layers/business/plan/index|Modelo de Plan]]
- [[architecture/layers/business/subscription/index|Modelo de Suscripción]]
- [[architecture/layers/business/billing/index|Modelo de Facturación]]
- [Modelo de Usuario](../architecture/layers/administrative/index.md)
- [Modelo de Asignación](../architecture/layers/administrative/index.md)
- [Modelo de Conocimiento](../architecture/layers/operative/index.md)
- [Arquitectura de Atlas](../applications/atlas.md)
- [Responsabilidades de Atlas](../applications/atlas.md)
- [Responsabilidades de Codex](../applications/codex.md)
- [Flujo Administrativo](administrative-flow.md)

## Dependencia de capas

```text
Operative
   ↑
Administrative
   ↑
Business
```

Desde la perspectiva de dependencia:

```text
Business
   ↓ depende de
Administrative
   ↓ depende de
Operative
```

La existencia del Flujo de Negocio no es requisito para que la Capa Operativa funcione.

## Objetos principales

### Cliente

El Cliente posee:

```text
Identidad lógica
Nombre
Propietario vinculado
Documentación
Credenciales
Medios de contacto
```

Por tanto, el Cliente se vincula a un Propietario definido por el Modelo de Usuario.

### Plan

El Plan posee:

```text
Identidad lógica
Nombre
Costo
Límite de Usuarios
Límite de Asignaciones
```

### Suscripción

La Suscripción vincula:

```text
Cliente
+
Plan
```

y posee:

```text
Estado
Fecha de inicio
Fecha de expiración
Última facturación
Siguiente facturación
```

Estados actuales:

```text
ACTIVO
INACTIVO
```

### Facturación

Un registro de Facturación referencia una Suscripción y posee:

```text
Identidad lógica
Fecha de facturación
Monto
Estado
```

Estados actuales:

```text
PENDIENTE
LIQUIDADA
```

## Flujo principal

### 1. Existencia del Propietario

El Cliente referencia un Propietario.

Por tanto, antes de poder establecer el vínculo documentado:

```text
Propietario
    ↓
Cliente
```

debe existir una identidad de Propietario válida dentro del conocimiento administrativo.

Este flujo no redefine Propietario; lo consume desde el Modelo de Usuario.

### 2. Alta o registro de Cliente

Conceptualmente:

```text
Propietario existente
        ↓
crear Cliente
        ↓
Identidad lógica
Nombre
Propietario vinculado
Documentación
Credenciales
Contacto
        ↓
persistir en Fuente de Conocimiento
```

Codex controla las operaciones efectuadas sobre los datos y su persistencia.

### 3. Disponibilidad de Plan

Un Plan constituye una oferta/estructura comercial documentada mediante:

```text
Identidad
Nombre
Costo
Límites
```

Conceptualmente:

```text
Plan
  ├── costo
  ├── límite de Usuarios
  └── límite de Asignaciones
```

La documentación actual no define lifecycle, versionado ni reglas de modificación de Plan; el flujo no las inventa.

### 4. Creación de Suscripción

La Suscripción enlaza Cliente y Plan:

```text
Cliente
   +
Plan
   ↓
Suscripción
```

Se registran:

```text
identidad
cliente vinculado
plan vinculado
estado
inicio
expiración
última facturación
siguiente facturación
```

El estado declarado puede ser:

```text
ACTIVO
INACTIVO
```

### 5. Estado comercial

El estado de Suscripción representa el estado comercial declarado disponible actualmente.

```text
Suscripción
   |
   +-- ACTIVO
   |
   +-- INACTIVO
```

> Los documentos actuales no establecen todavía qué capacidades técnicas se conceden o revocan automáticamente al cambiar este estado. Por tanto, este flujo no convierte `ACTIVO` en autorización operacional ni `INACTIVO` en revocación técnica automática.

### 6. Generación o registro de Facturación

Una Facturación pertenece a una Suscripción concreta.

```text
Suscripción
    ↓
Facturación
    ↓
Fecha
Monto
Estado
```

Estado inicial/derivado documentado:

```text
PENDIENTE
```

Estado declarado documentado:

```text
LIQUIDADA
```

La transición conceptual disponible es:

```text
PENDIENTE
   ↓
LIQUIDADA
```

La documentación actual no define procesador de pagos, conciliación externa, moneda, impuestos ni método de cobro.

### 7. Actualización de fechas de Suscripción

La Suscripción contiene:

```text
Última facturación
Siguiente facturación
```

Por tanto el conocimiento comercial debe poder reflejar los hitos de facturación correspondientes.

Sin embargo, los modelos actuales no congelan:

- algoritmo de calendario;
- periodicidad;
- renovación automática;
- gracia;
- suspensión;
- reintentos;
- prorrateo.

Este flujo mantiene esas decisiones fuera de alcance.

### 8. Consulta administrativa/comercial

Atlas documenta, dentro de su Plano Administrativo, control sobre:

```text
Modelo de Negocio
  ├── Clientes
  └── Facturación
```

Por tanto la visualización/administración del conocimiento business puede fluir:

```text
Usuario administrativo
        ↓
Atlas
        ↓
Fuente de Conocimiento
        ↓
Cliente / Plan / Suscripción / Facturación
```

siempre bajo las reglas administrativas aplicables.

### 9. Persistencia

Los objetos business forman parte de la sección de Negocio de la Fuente de Conocimiento.

```text
Fuente de Conocimiento
        ↓
Business
  ├── Client
  ├── Plan
  ├── Subscription
  └── Billing
```

Codex es responsable de gestionar persistencia, coherencia e integridad del conocimiento.

## Relación con el Flujo Administrativo

La Capa Business extiende a la Administrativa.

Ejemplo conceptual:

```text
Cliente
  ↓ vincula
Propietario
  ↓ posee
Usuarios
  ↓ reciben
Asignaciones
  ↓ administran
Dominios / Entidades
```

No obstante, la documentación actual **no define todavía** una regla normativa que derive automáticamente Usuarios o Asignaciones a partir de límites de Plan o estado de Suscripción.

Por ello:

```text
Plan.limit.users
Plan.limit.assignments
```

son datos definidos, pero su mecanismo de enforcement todavía no está especificado en los modelos actuales.

## Resultado exitoso

El flujo comercial básico queda representado cuando existe una cadena coherente:

```text
Propietario
   ↓
Cliente
   ↓
Plan
   ↓
Suscripción
   ↓
Facturación
```

más exactamente:

```text
Cliente ------> Propietario
   |
   +-------> Suscripción <------- Plan
                   |
                   v
              Facturación
```

y todos los objetos correspondientes se encuentran registrados de manera coherente en la Fuente de Conocimiento.

## Fallos o inconsistencias detectables

El flujo debe poder distinguir conceptualmente:

```text
Propietario inexistente
Cliente inexistente
Plan inexistente
Suscripción inexistente
referencia inconsistente
estado inválido
fecha inconsistente
Facturación sin Suscripción válida
operación de persistencia fallida
```

La definición exacta de todas las invariantes de integridad corresponde a los modelos y a Codex.

## Límites explícitos

Los documentos actuales no soportan todavía afirmar que este flujo define:

- procesamiento real de pagos;
- renovación automática;
- cancelación;
- suspensión;
- periodos de gracia;
- impuestos;
- monedas;
- descuentos;
- prorrateo;
- enforcement automático de Plan;
- enforcement automático de Suscripción;
- creación automática de Usuarios;
- creación automática de Asignaciones;
- fabricación automática de Echo;
- distribución automática de software.

Estas capacidades requieren documentación normativa adicional antes de incorporarse al flujo.
