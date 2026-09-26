# Flujo de Aplicaciones

## Propósito

El Flujo de Aplicaciones describe cómo las aplicaciones actualmente documentadas de T-Shell convergen para materializar responsabilidades del sistema.

Las aplicaciones formalmente documentadas en el corpus actual son:

```text
Atlas
Echo
Codex
```

La Manufactura y la Distribución existen actualmente como responsabilidades/modelos operativos, pero **no existe todavía una aplicación Manufacturer Server formalmente especificada**. Por ello este flujo no la introduce como aplicación.

## Composición documental

Lease:

- [Arquitectura de Atlas](../applications/atlas.md)
- [Responsabilidades de Atlas](../applications/atlas.md)
- [Responsabilidades de Echo](../applications/echo.md)
- [Arquitectura de Codex](../applications/codex.md)
- [Responsabilidades de Codex](../applications/codex.md)
- [Implementación de Codex](../applications/codex.md)
- [Modelo de Conocimiento](../architecture/layers/operative/index.md)
- [Modelo de Entidad](../architecture/layers/operative/index.md)
- [Modelo de Autoridad](../architecture/layers/operative/index.md)
- [Plataforma de Autenticación](../platform/authentication.md)
- [Plataforma de Enlace](../platform/linkage.md)
- [Plataforma de Transporte](../platform/transport.md)
- [Plataforma de Módulos](../platform/module.md)
- [Adaptador de Plataforma del Sistema](../platform/system-adapter.md)
- [Flujo Operativo](operative-flow.md)
- [Flujo Administrativo](administrative-flow.md)
- [Flujo de Negocio](business-flow.md)

## Posición de las aplicaciones

### Codex

Codex materializa la Fuente de Conocimiento.

Responsabilidades documentadas:

```text
persistencia
operaciones sobre datos
coherencia
integridad
fiabilidad
seguridad
disponibilidad
backups
API
```

### Atlas

Atlas desempeña el rol de Controlador/Ordenante y concentra:

```text
orquestación
inventario
supervisión de Echo
interfaces administrativas
operación remota
administración de módulos
acceso/control de Fuente de Conocimiento
```

### Echo

Echo desempeña el rol de Nodo/Subordinante y concentra:

```text
presencia remota
ciclo de vida local
identidad
conectividad saliente
plataformas internas
adaptación al sistema anfitrión
módulos
ejecución de acciones locales
recuperación
```

## Relación general

```text
             Usuario / Interfaces
                     |
                     v
                   Atlas
                  /     \
                 /       \
                v         v
             Codex       Echo
              |           |
              v           v
       Fuente de       Sistema
       Conocimiento    anfitrión
```

Para múltiples Echo:

```text
                   Atlas
                /    |    \
               v     v     v
            Echo1  Echo2  EchoN
```

Codex representa la Fuente de Conocimiento utilizada por el sistema; Atlas la consume para operar y administrar.

## Flujo de inicialización

### 1. Inicialización de Codex

Antes de que una aplicación consumidora pueda depender de conocimiento autoritativo, Codex debe poder proporcionar sus contratos y almacenamiento correspondientes.

Conceptualmente:

```text
Codex
  ↓
Almacenamiento persistente / efímero
  ↓
Contratos
  ↓
Funciones
  ↓
Fuente de Conocimiento disponible
```

La Implementation actual documenta Python 3 + PostgreSQL, pero esas tecnologías pertenecen a Implementation y no al flujo abstracto.

### 2. Inicialización de Atlas

Atlas:

```text
inicia
  ↓
valida dependencias
  ↓
accede a Codex / Fuente de Conocimiento
  ↓
inicia plataformas
  ↓
inicia interfaces/listeners
  ↓
OPERATIVO
```

Atlas debe continuar aceptando conexiones independientes cuando una conexión individual falle.

### 3. Inicialización de Echo

Echo:

```text
inicia
  ↓
recupera identidad/configuración
  ↓
valida dependencias
  ↓
inicia plataformas
  ↓
descubre capacidades
  ↓
intenta conectar a Atlas
```

Echo debe poder permanecer ejecutándose en estado desconectado o degradado cuando las condiciones externas lo requieran y las dependencias esenciales sigan satisfechas.

## Flujo de establecimiento Atlas <-> Echo

La relación operacional entre aplicaciones se delega a las plataformas correspondientes.

```text
Echo
  ↓
TransportPlatform
  ↓
Atlas
  ↓
AuthenticationPlatform
  ↓
política superior aplicable
  ↓
LinkagePlatform
  ↓
OperationalBinding
  ↓
SystemPlatformAdapter
  ↓
ModulePlatform
```

Las aplicaciones orquestan; las plataformas conservan sus fronteras de responsabilidad.

Atlas y Echo no deben introducir semántica de módulo dentro de Transport, Authentication o Linkage.

## Registro y supervisión de presencia

Una vez establecida operación suficiente:

```text
Echo
  ↓
expone estado/capacidades
  ↓
Atlas
  ↓
mantiene inventario/presencia
  ↓
Codex
  ↓
conocimiento persistente cuando corresponda
```

Atlas puede distinguir:

```text
conocido
conectado
autenticado
operacional
desconectado
degradado
```

El estado efímero de conexión/enlace no debe confundirse con conocimiento persistente.

## Flujo de interfaz administrativa

Atlas proporciona las interfaces administrativas documentadas:

```text
API
WebGUI
CLI
SDK
integraciones externas
```

Conceptualmente:

```text
Consumidor administrativo
        ↓
Atlas
        ↓
Sesión / política administrativa
        ↓
Fuente de Conocimiento
        |
        +------ consulta/modificación de conocimiento
        |
        +------ operación remota
                       ↓
                      Echo
```

La superficie concreta de cada interfaz pertenece a la arquitectura/Implementation correspondiente.

## Flujo de una operación remota

### 1. Selección del objetivo

Atlas determina el Echo objetivo a partir del inventario y contexto administrativo.

```text
Usuario/consumidor
        ↓
Atlas
        ↓
Echo objetivo
```

### 2. Verificación de disponibilidad

Atlas consulta:

```text
estado de Echo
conectividad
autenticación
módulos disponibles
estado de módulos
capacidades
```

### 3. Selección de módulo/capacidad

```text
Echo objetivo
   ↓
módulos anunciados
   ↓
módulo requerido
   ↓
capability requerida
```

Atlas no debe asumir capacidades inexistentes.

### 4. Composición WebGUI cuando corresponda

Para módulos con WebGUI especializada:

```text
Atlas WebGUI
    ↓
Module WebGUI Host API
    ↓
Module WebGUI Guest
    ↓
Módulo
```

Atlas actúa como Host/compositor. La semántica visual y operacional específica pertenece al Guest propietario.

Ejemplos:

```text
SHELL             -> terminal
FTP               -> gestor de archivos
TERMUX            -> capacidades Android/Termux
Resources Monitor -> telemetría de recursos
```

### 5. Envío de solicitud

```text
Atlas
  ↓
ModulePlatform
  ↓
SystemPlatformAdapter
  ↓
TransportPlatform
  ↓
Echo
  ↓
SystemPlatformAdapter
  ↓
ModulePlatform
  ↓
Módulo
```

### 6. Ejecución local

Echo:

1. recibe la operación;
2. resuelve el módulo;
3. valida disponibilidad/capacidad local;
4. ejecuta o rechaza;
5. conserva aislamiento frente a módulos no relacionados;
6. produce resultado, evento o stream según el protocolo propietario.

### 7. Retorno

```text
Módulo en Echo
    ↓
ModulePlatform
    ↓
Transport
    ↓
Atlas
    ↓
módulo/Guest consumidor
    ↓
resultado presentado
```

El resultado debe conservar la semántica del módulo propietario.

## Flujo de conocimiento

Cuando Atlas requiere conocimiento persistente:

```text
Atlas
  ↓
contrato de Codex
  ↓
Codex
  ↓
validación/coherencia
  ↓
persistencia
  ↓
resultado
```

Atlas no debe sustituir la responsabilidad de Codex sobre coherencia e integridad de conocimiento.

## Flujo de desconexión

Cuando Echo pierde su conexión:

```text
Echo
  X
Atlas
```

ocurre:

```text
Transport desconecta
  ↓
Linkage elimina binding
  ↓
Atlas actualiza presencia
  ↓
Echo entra en estado desconectado
  ↓
Echo conserva identidad/configuración/módulos
  ↓
Echo intenta recuperar conectividad
```

Codex conserva el conocimiento persistente que corresponda aunque Echo esté desconectado.

## Flujo de recuperación

```text
Echo desconectado
  ↓
localiza infraestructura
  ↓
reconecta
  ↓
autentica
  ↓
satisface política aplicable
  ↓
crea nuevo binding
  ↓
restablece presencia operacional
  ↓
Atlas actualiza estado
```

La recuperación no revive implícitamente un `OperationalBinding` anterior.

## Fallos parciales

### Fallo de módulo

```text
Módulo FAILED
```

no debe terminar:

```text
ModulePlatform
TransportPlatform
otros módulos
proceso completo
```

cuando la contención sea posible.

### Fallo de una conexión

No debe impedir que Atlas mantenga conexiones independientes ni acepte nuevas.

### Indisponibilidad de Atlas

Echo permanece ejecutándose y conserva estado local persistente, intentando recuperar conectividad.

### Indisponibilidad de Codex

Las responsabilidades de Atlas exigen recuperación y tolerancia a fallos de dependencias, pero la documentación actual no congela todavía un modo operacional completo de Atlas sin Fuente de Conocimiento. Por tanto este flujo no afirma que Atlas pueda continuar operación autoritativa normal sin Codex.

## Relación con Manufactura y Distribución

Actualmente existen:

```text
Modelo de Manufactura
Modelo de Distribución
```

que pueden preceder al ciclo de vida de Echo:

```text
Fabricar software Echo
        ↓
Distribuir artefacto
        ↓
Instalar/ejecutar Echo
        ↓
Flujo de Aplicaciones
```

Sin embargo, no existe aún documentación normativa suficiente para afirmar:

```text
Manufacturer Server
```

como aplicación formal del sistema.

## Resultado exitoso

El flujo de aplicaciones se considera coherente cuando:

```text
Codex
  proporciona conocimiento coherente
        +
Atlas
  orquesta y administra
        +
Echo
  mantiene presencia y materializa operación local
        +
Plataformas
  conservan sus fronteras
```

sin que una aplicación absorba responsabilidades pertenecientes a otra.

## Invariantes del flujo

1. Atlas orquesta; no materializa directamente cambios locales en Echo.
2. Echo materializa operación local; no administra globalmente usuarios o inventario.
3. Codex gestiona conocimiento; no sustituye las responsabilidades operacionales de Atlas/Echo.
4. Transport no conoce Entity/Domain/User/Module semantics.
5. Authentication prueba posesión según su contrato estrecho; no equivale a autorización.
6. Linkage mantiene relaciones efímeras; no constituye conocimiento persistente.
7. ModulePlatform enruta módulos; no interpreta su protocolo interno.
8. Un módulo posee su semántica especializada.
9. Las capacidades reales de Echo dependen de la plataforma anfitriona.
10. El fallo de una pieza aislable no debe propagarse innecesariamente a piezas independientes.

## Límites

Este flujo no define:

- arquitectura interna final de Echo, todavía no documentada;
- Manufacturer Server;
- protocolo completo de autenticación de Usuario;
- reglas completas de negocio;
- tecnología concreta de despliegue;
- endpoints;
- clases Python;
- servicios concretos;
- IPC concreto;
- base de datos concreta;
- mecanismo exacto de actualización.

Esas decisiones deben derivarse posteriormente de la documentación normativa correspondiente.
