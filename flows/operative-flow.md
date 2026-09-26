# Flujo Operativo

## Propósito

El Flujo Operativo describe cómo las capacidades de la **Capa Operativa** convergen para permitir que una Entidad subordinada (`Echo`) localice, verifique y mantenga una relación operacional con la Entidad ordenante (`Atlas`), y cómo ambas intercambian operaciones de módulos sin trasladar responsabilidades entre plataformas.

Este flujo describe **composición y secuencia**. No redefine las primitivas de los modelos ni los contratos de las plataformas consumidas.

## Composición documental

Referencia:

- [Capa Operativa](../architecture/layers/operative/index.md)
- [Modelo de Identidad](../architecture/layers/operative/index.md)
- [Modelo de Entidad](../architecture/layers/operative/index.md)
- [Modelo de Autoridad](../architecture/layers/operative/index.md)
- [Modelo de Dominio](../architecture/layers/operative/index.md)
- [Modelo de Topología](../architecture/layers/operative/index.md)
- [Modelo de Red](../architecture/layers/operative/index.md)
- [Modelo de Infraestructura](../architecture/layers/operative/index.md)
- [Modelo de Conocimiento](../architecture/layers/operative/index.md)
- [Plataforma de Autenticación](../platform/authentication.md)
- [Plataforma de Enlace](../platform/linkage.md)
- [Plataforma de Transporte](../platform/transport.md)
- [Plataforma de Módulos](../platform/module.md)
- [Adaptador de Plataforma del Sistema](../platform/system-adapter.md)
- [Responsabilidades de Atlas](../applications/atlas.md)
- [Responsabilidades de Echo](../applications/echo.md)

Cuando el software todavía no está desplegado, el flujo puede ser precedido por:

- [Modelo de Manufactura](../architecture/layers/operative/index.md)
- [Modelo de Distribución](../architecture/layers/operative/index.md)

## Sujetos

Según los modelos actuales:

```text
Atlas
  desempeña: Controlador
  autoridad: Ordenante

Echo
  desempeña: Nodo
  autoridad: Subordinante
```

Topología:

```text
Atlas : Echo
  1   :   N
```

Conectividad:

```text
Echo -> Atlas
```

La conexión es iniciada por Echo. Atlas no inicia conexiones entrantes hacia Echo.

## Precondiciones

Para que pueda existir operación remota completa deben cumplirse, según corresponda:

1. Atlas se encuentra operativo y dispone de infraestructura localizable.
2. Echo se encuentra inicializado y posee una identidad persistente.
3. Echo dispone de configuración suficiente para localizar infraestructura conocida.
4. Las plataformas internas imprescindibles se encuentran disponibles.
5. Existe una conexión de transporte activa.
6. Existe una `AuthenticationSession` válida para la identidad participante.
7. La política superior aplicable —por ejemplo, integración y acceso a Dominio— ha sido satisfecha.
8. La conexión se encuentra vinculada operacionalmente a la identidad mediante `OperationalBinding`.
9. El módulo destino existe y se encuentra disponible para procesar la operación.

La Plataforma de Autenticación **no** decide membresía de dominio, validez de pasaporte, autorización de entidad, permisos de usuario ni política de negocio.

## Flujo principal

### 1. Inicialización de Atlas

Atlas:

1. inicializa su proceso y dependencias;
2. valida configuración y estado persistente;
3. inicializa las plataformas internas necesarias;
4. obtiene acceso a la Fuente de Conocimiento;
5. inicia listeners y superficies operacionales;
6. entra en estado operacional cuando las dependencias imprescindibles se encuentran disponibles.

Conceptualmente:

```text
Atlas
  ↓
Configuración / estado persistente
  ↓
Fuente de Conocimiento
  ↓
Plataformas internas
  ↓
Listeners
  ↓
OPERATIVO
```

El fallo de una conexión individual no debe impedir que Atlas continúe aceptando conexiones independientes.

### 2. Inicialización de Echo

Echo:

1. carga y valida configuración;
2. recupera su identidad persistente;
3. valida dependencias;
4. inicializa plataformas internas;
5. determina capacidades efectivamente disponibles en la plataforma anfitriona;
6. conserva módulos instalados y su estado;
7. entra en operación normal, degradada o desconectada según las capacidades y conectividad disponibles.

```text
Echo
  ↓
Configuración
  ↓
Identidad
  ↓
Dependencias
  ↓
Plataformas internas
  ↓
Capacidades efectivas
  ↓
Operación
```

La ausencia temporal de Atlas no constituye por sí misma un fallo fatal de Echo.

### 3. Resolución de infraestructura

Echo utiliza el conocimiento de infraestructura disponible.

Ruta preferente:

```text
Infraestructura principal actual
        ↓
localización
        ↓
Atlas operativo
```

Si la infraestructura principal deja de ser localizable, Echo puede intentar infraestructuras alternativas conforme al Modelo de Infraestructura y sus prioridades.

```text
IS-P no localizable
        ↓
IS-S-1
        ↓
verificaciones
        ↓
nueva IS-P válida
```

Localizar una infraestructura **no demuestra su legitimidad**.

La actualización periódica de conocimiento de infraestructura permite que Echo conserve alternativas para reconexiones posteriores.

### 4. Establecimiento de transporte

Echo inicia una conexión saliente hacia Atlas.

```text
Echo
  │
  │ conectar()
  ▼
Plataforma de Transporte
  │
  ▼
ConnectionUUID
  │
  ▼
Atlas
```

`Plataforma de Transporte` administra la conexión concreta y permanece ajena a:

- identidad de entidad;
- dominio;
- autenticación;
- usuario;
- negocio;
- semántica de módulos.

Una conexión de transporte no equivale a una entidad autenticada ni autorizada.

### 5. Integración y política de Dominio

La integración inicial de Echo a un Dominio está normativamente definida por:

> Referencia: [Modelo de Integración Operativa](../architecture/layers/operative/index.md)

El flujo completo ya posee orden protocolario estable. En términos resumidos:

```text
Echo conecta hacia Atlas
  ↓
declara Entity e Identidad
  ↓
Atlas consulta logical_uid en Fuente de Conocimiento
  ↓
si ya existe: deniega o bloquea según conflicto de identidad
  ↓
si es nueva: verifica prueba criptográfica
  ↓
Atlas resuelve Domain
  ↓
Echo verifica Identidad del Domain esperado
  ↓
Echo presenta Passport
  ↓
Atlas valida Passport
  ↓
commit transaccional:
Entity + Membership + consumo de Passport
```

Una Identidad lógica ya conocida NO puede volver a integrarse mediante esta operación. Si además presenta una Identidad criptográfica distinta de la conocida, la integración termina como incidente de seguridad y no modifica conocimiento autoritativo.

La integración exitosa materializa de forma atómica la nueva Entity, su Membership y el consumo del Passport. La conexión, autenticación criptográfica o presentación de Passport por sí solas no equivalen a integración.

Echo verifica que el Domain de destino corresponda al esperado mediante la Identidad lógica y criptográfica del Domain.

Los estados y resultados canónicos pertenecen a:

- [Estados de Integración](../architecture/layers/operative/index.md)
- [Resultados de Integración](../architecture/layers/operative/index.md)

### 6. Autenticación

La Plataforma de Autenticación recibe:

```text
entity_uuid
entity_public_key
challenge
signature
```

y verifica la prueba criptográfica de posesión correspondiente a la clave pública presentada.

Resultado:

```text
AuthenticationSession
        |
        +-- auth_token
        +-- entity_uuid
        +-- entity_public_key
        +-- status
```

Una sesión válida demuestra exclusivamente la prueba criptográfica definida por la Plataforma de Autenticación.

No demuestra por sí sola:

```text
membresía de Dominio
validez de Pasaporte
autorización
legitimidad EntityUUID <-> public_key
protección contra replay
```

### 7. Enlace operacional

Una vez cumplidas las verificaciones de autenticación y política aplicables:

```text
ConnectionUUID
    +
AuthToken válido
    ↓
Adaptador del Sistema
    ↓
BIND
    ↓
OperationalBinding
```

El `Adaptador del Sistema`:

1. obtiene el `ConnectionUUID` desde metadata local;
2. valida la `AuthenticationSession` asociada al `AuthToken`;
3. resuelve la identidad de la sesión;
4. verifica que la conexión siga activa;
5. solicita a `Plataforma de Enlace` el enlace entre conexión e identidad.

El enlace resultante contiene:

```text
connection_uuid
entity_uuid
auth_token
created_at
state
```

El enlace es efímero y no constituye conocimiento persistente.

### 8. Presencia operacional de Echo

Una vez que Echo dispone de conectividad y condiciones de confianza suficientes, Atlas puede conocer su presencia operacional.

Echo expone, conforme a sus responsabilidades:

```text
Identidad
Estado de aplicación
Plataforma
Arquitectura
Versión
Estado de componentes
Módulos disponibles
Estado de módulos
Capacidades efectivas
```

Atlas mantiene conocimiento de:

```text
Echo conocido
Echo conectado
Echo autenticado
Echo operacional
Echo desconectado
Echo degradado
```

La información administrativa puede persistir aunque Echo pierda conectividad.

### 9. Recepción de DATA

Pipeline operacional de recepción:

```text
Plataforma de Transporte RX
        ↓
Adaptador del Sistema
        ↓
validar ConnectionUUID
        ↓
resolver OperationalBinding
        ↓
validar AuthenticationSession exacta
        ↓
ConnectionUUID -> EntityUUID
        ↓
Plataforma de Módulos RX
        ↓
module_uuid
        ↓
Módulo
```

`Adaptador del Sistema` sustituye la dirección física/lógica correspondiente mediante metadata local:

```text
_source_connection_identification
        ↓
_entity_source_identification
```

Los campos locales `_...` no deben serializarse sobre el medio.

### 10. Ejecución de una operación de módulo

`Plataforma de Módulos` enruta por:

```text
module_uuid
```

sin interpretar el protocolo interno del módulo.

```text
Entidad origen
   ↓
Plataforma de Módulos
   ↓
Módulo destino
   ↓
protocolo propio del módulo
   ↓
resultado / eventos / stream
```

Cada módulo conserva su frontera semántica.

Ejemplos actuales:

```text
SHELL
    ejecución interactiva y atómica

FTP
    sistema de archivos y transferencias

TERMUX
    capacidades Android/Termux

Resources Monitor
    observabilidad de recursos
```

La identidad confiable del origen proviene de la Plataforma de Módulos/Adaptador del Sistema, no de campos declarados dentro del payload del módulo.

### 11. Envío de DATA

Pipeline de transmisión:

```text
Plataforma de Módulos TX
        ↓
_entity_destination_identification
        ↓
Adaptador del Sistema
        ↓
resolver OperationalBinding
        ↓
validar AuthenticationSession exacta
        ↓
resolver ConnectionUUID
        ↓
Plataforma de Transporte TX
        ↓
Entidad destino
```

La capa de módulos no administra conexiones de transporte.

### 12. Reautenticación

Una reautenticación válida puede mantener el mismo `AuthToken` únicamente bajo las reglas de `AuthenticationSession` existentes.

Una **nueva** `AuthenticationSession` para el mismo `EntityUUID` no hereda el enlace anterior.

```text
AuthenticationSession A -> OperationalBinding A

nueva AuthenticationSession B
        ↓
sin OperationalBinding
        ↓
nuevo BIND requerido
```

### 13. Desconexión

Cuando una conexión se pierde:

```text
TransportConnection
        ↓ DISCONNECTED
Plataforma de Transporte
        ↓
purge_connection(ConnectionUUID)
        ↓
Plataforma de Enlace
        ↓
OperationalBinding eliminado
```

Atlas actualiza el estado observado de Echo.

Echo permanece ejecutándose cuando sea posible y entra en estado desconectado:

```text
Echo
  ├── conserva identidad
  ├── conserva configuración
  ├── conserva módulos
  └── intenta recuperar conectividad
```

### 14. Reconexión

La reconexión no pertenece a `TransportConnection`.

Echo orquesta:

```text
pérdida
  ↓
backoff / nueva localización
  ↓
conexión
  ↓
autenticación
  ↓
política aplicable
  ↓
BIND
  ↓
operación recuperada
```

La reconexión crea una nueva relación operacional; no debe asumir que el enlace efímero previo sobrevivió.

## Repliegue de infraestructura

Ante riesgo o pérdida de la infraestructura principal:

### Preventivo

```text
IS-P actual
  ↓ riesgo
repliegue preventivo
  ↓
infraestructura alternativa
  ↓
nueva IS-P
```

### Reactivo

```text
IS-P actual
  ↓ pérdida
repliegue reactivo
  ↓
infraestructura alternativa
  ↓
nueva IS-P
```

Cambiar de infraestructura principal no modifica:

```text
Identidad
Dominio
Membresía
conocimiento autoritativo restante
```

## Backpressure y contención de fallos

El flujo operativo conserva los invariantes de plataforma:

1. las colas de runtime son finitas;
2. no existe crecimiento ilimitado como estrategia de sobrecarga;
3. el tráfico fiable no se descarta silenciosamente;
4. el fallo de un módulo no debe terminar plataformas ni módulos no relacionados;
5. una conexión defectuosa no debe invalidar conexiones independientes;
6. una operación fallida no debe invalidar operaciones independientes;
7. una capacidad opcional ausente puede producir operación degradada, no necesariamente fallo fatal.

## Resultado exitoso

El flujo operativo se considera establecido cuando:

```text
Echo ejecutándose
+
Atlas operativo
+
transporte activo
+
condiciones de autenticación/política satisfechas
+
OperationalBinding activo
+
módulo/capacidad disponible
```

y una operación autorizada puede viajar desde el origen al módulo destino y devolver un resultado explícito.

## Resultados de fallo o degradación

El flujo debe distinguir al menos:

```text
Atlas no localizable
Echo desconectado
transporte perdido
autenticación inválida/expirada
política de Dominio no satisfecha
BIND inexistente/inválido
módulo inexistente
módulo deshabilitado
módulo no cargado
módulo fallido
capacidad no disponible
backpressure / timeout
operación rechazada
```

Estos estados no son equivalentes y no deben colapsarse en un único estado genérico de error.

## Límites

Este flujo no define:

- autenticación de usuarios;
- sesiones administrativas;
- asignaciones;
- clientes;
- planes;
- suscripciones;
- facturación;
- protocolo interno de cada módulo;
- tecnología concreta de transporte;
- implementación concreta de Atlas, Echo o Codex.

Esas responsabilidades pertenecen a sus modelos, plataformas, aplicaciones o implementaciones correspondientes.
