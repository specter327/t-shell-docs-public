# Flujo Administrativo

## Propósito

El Flujo Administrativo describe cómo la **Capa Administrativa** extiende la Capa Operativa para permitir que un Usuario autenticado mantenga una Sesión y acceda, conforme a sus Asignaciones, a Dominios o Entidades administrables mediante Atlas.

La Capa Administrativa añade administración sobre la operación existente; no sustituye los modelos operativos.

## Composición documental

Lease:

- [[architecture/layers/administrative/index|Capa Administrativa]]
- [[architecture/layers/administrative/user/index|Modelo de Usuario]]
- [[architecture/layers/administrative/assignment/index|Modelo de Asignación]]
- [[architecture/layers/administrative/session/index|Modelo de Sesión]]
- [[architecture/layers/operative/domain/index|Modelo de Dominio]]
- [[architecture/layers/operative/entity/index|Modelo de Entidad]]
- [[architecture/layers/operative/knowledge/index|Modelo de Conocimiento]]
- [[applications/atlas/architecture|Arquitectura de Atlas]]
- [[applications/atlas/responsibilities|Responsabilidades de Atlas]]
- [[applications/echo/responsibilities|Responsabilidades de Echo]]
- [[flows/operative-flow|Flujo Operativo]]

## Dependencia de capa

```text
Capa Operativa
      ↑
Capa Administrativa
```

Interpretado desde dependencia:

```text
Capa Administrativa
        ↓ depende de
Capa Operativa
```

La Capa Operativa puede existir sin la Capa Administrativa. El Flujo Administrativo necesita que los objetos operativos que administra existan.

## Actores y objetos

### Propietario

El Modelo de Usuario define un Propietario con:

```text
Identidad lógica
Nombre
Usuarios pertenecientes
Dominios vinculados
Medios de contacto
```

### Usuario

Un Usuario posee:

```text
Identidad lógica
Nombre
Propietario padre
Credenciales
Medio de contacto
```

### Sesión de Usuario

Una Sesión representa la presencia administrativa temporal de un Usuario.

```text
Identificación lógica
Fecha de inicio
Fecha de expiración opcional
Fecha de finalización opcional
Identidad lógica de Usuario
Prueba de posesión
```

### Asignación

Una Asignación vincula:

```text
Usuario
Dominio
Entidad opcional
```

y expresa alcance administrativo.

## Precondiciones

Para ejecutar una acción administrativa sobre un objeto operativo:

1. el Usuario debe existir en la Fuente de Conocimiento;
2. debe poder establecer una Sesión válida según el Modelo de Sesión;
3. el Dominio o Entidad objetivo debe existir;
4. debe existir una Asignación que cubra el objetivo cuando la política administrativa lo requiera;
5. Atlas debe encontrarse disponible para recibir la acción administrativa;
6. si la acción requiere actuar sobre Echo, el Flujo Operativo debe proporcionar una ruta operacional válida hacia ese Echo;
7. Echo debe validar las condiciones locales y capacidades necesarias antes de materializar el cambio.

## Flujo principal

### 1. Existencia del conocimiento administrativo

La Fuente de Conocimiento conserva la sección administrativa.

Conceptualmente:

```text
Fuente de Conocimiento
        |
        +-- Propietarios
        +-- Usuarios
        +-- Asignaciones
        +-- Sesiones
```

Codex es la aplicación actualmente documentada como materialización de la Fuente de Conocimiento.

### 2. Identificación del Usuario

El consumidor administrativo presenta las credenciales definidas para el Usuario.

```text
Usuario
  ↓
credenciales / prueba de posesión
  ↓
Atlas
```

El Modelo de Usuario actualmente incluye credenciales, mientras el Modelo de Sesión establece autenticación basada en prueba de posesión.

> La documentación actual no congela todavía un protocolo administrativo completo de autenticación de Usuario. Por tanto este flujo no inventa endpoints, formatos de credenciales ni mecanismos adicionales.

### 3. Apertura de Sesión

Una autenticación administrativa satisfactoria permite crear una Sesión asociada al Usuario.

```text
Usuario válido
    ↓
crear Sesión
    ↓
Session Identity
    +
User Identity
    +
timestamps
    ↓
Sesión activa
```

La Sesión es temporal y debe poder:

```text
iniciarse
expirar
finalizarse
```

Atlas utiliza la Sesión para mantener contexto administrativo.

### 4. Selección del alcance administrativo

El Usuario intenta acceder a un Dominio o a una Entidad concreta.

La política actual de Asignación define dos formas:

#### Acceso completo al Dominio

```text
Usuario -> Dominio
```

El Usuario obtiene alcance sobre el Dominio completo.

#### Acceso particular

```text
Usuario -> Dominio -> Entidad
```

El Usuario obtiene alcance únicamente sobre una Entidad concreta dentro del Dominio indicado.

La propia documentación establece que, si el Usuario ya tiene acceso al Dominio completo, no tiene sentido añadir asignaciones particulares redundantes para Entidades de ese mismo Dominio.

### 5. Evaluación de acceso

Conceptualmente:

```text
Sesión
  ↓
Usuario
  ↓
Asignaciones
  ↓
¿Dominio permitido?
  |
  +-- NO -> rechazar
  |
  +-- SÍ
       ↓
¿acceso completo?
  |
  +-- SÍ -> objetivo permitido dentro del Dominio
  |
  +-- NO
       ↓
¿Entidad objetivo incluida?
  |
  +-- NO -> rechazar
  |
  +-- SÍ -> permitir continuar
```

La evaluación no debe confundir:

```text
Usuario autenticado
```

con:

```text
Usuario autorizado sobre el objetivo
```

### 6. Construcción de la vista administrativa

Atlas puede componer inventario e información administrativa a partir del conocimiento permitido al Usuario.

Según sus responsabilidades, Atlas puede presentar:

```text
Dominios
Echo conocidos
estado de Echo
módulos
capacidades
metadatos
etiquetas
notas
información administrativa
```

La vista debe respetar el alcance administrativo derivado de las Asignaciones.

### 7. Solicitud de acción administrativa

El Usuario solicita una acción sobre un objetivo autorizado.

Ejemplos de responsabilidades documentadas de Atlas:

```text
consultar estado
consultar diagnóstico
consultar módulos
administrar módulos
administrar software cuando esté permitido
gestionar sesiones
ejecutar acciones administrativas autorizadas
```

Flujo:

```text
Usuario
  ↓
Sesión
  ↓
Atlas
  ↓
validar alcance administrativo
  ↓
validar objetivo
  ↓
orquestar acción
```

### 8. Acción que sólo modifica conocimiento

Cuando la operación afecta exclusivamente conocimiento administrativo:

```text
Atlas
  ↓
contrato de Fuente de Conocimiento
  ↓
Codex
  ↓
validar operación
  ↓
persistir / rechazar
  ↓
resultado explícito
```

Codex conserva la responsabilidad de gestionar datos, coherencia, integridad y persistencia.

### 9. Acción que requiere operación remota

Cuando la acción debe materializarse sobre Echo:

```text
Usuario
  ↓
Sesión válida
  ↓
Asignación válida
  ↓
Atlas
  ↓
Flujo Operativo
  ↓
Echo
  ↓
validar capacidad / precondiciones locales
  ↓
ejecutar o rechazar
  ↓
resultado explícito
  ↓
Atlas
  ↓
Usuario
```

Echo no toma decisiones globales de usuarios; recibe una instrucción que Atlas ha decidido orquestar y conserva responsabilidad sobre las condiciones locales necesarias para ejecutarla.

### 10. Administración de módulos

Para operaciones de módulos:

```text
Usuario autorizado
  ↓
Atlas
  ↓
Echo objetivo
  ↓
ModulePlatform
```

Las operaciones documentadas incluyen:

```text
instalación
actualización
desinstalación
habilitación
deshabilitación
carga
descarga
inicio
detención
reinicio
```

`ModulePlatform` conserva las invariantes propias de instalación, verificación, lifecycle y contención de fallos.

### 11. Resultado

Toda acción administrativa solicitada debe producir resultado explícito.

```text
SOLICITUD
   ↓
VALIDADA
   ↓
EJECUTADA
   ↓
RESULTADO
```

o:

```text
SOLICITUD
   ↓
RECHAZADA / FALLIDA
   ↓
RAZÓN EXPLÍCITA
```

## Finalización de Sesión

La Sesión termina por:

```text
finalización explícita
expiración
```

según los campos actualmente definidos por el Modelo de Sesión.

Una Sesión finalizada o expirada no debe utilizarse como contexto administrativo válido.

## Fallos y rechazos

El flujo debe poder distinguir:

```text
Usuario inexistente
credenciales/prueba inválida
Sesión inexistente
Sesión expirada
Sesión finalizada
Dominio inexistente
Entidad inexistente
Asignación inexistente
objetivo fuera del alcance
Atlas no disponible
Echo desconectado
Echo degradado
capacidad remota ausente
operación local rechazada
fallo de persistencia
fallo de módulo
```

## Invariantes del flujo

1. Autenticación de Usuario no equivale a autorización sobre cualquier objetivo.
2. Una Asignación limita el alcance administrativo.
3. El acceso completo a un Dominio hace redundantes asignaciones particulares a Entidades de ese mismo Dominio.
4. Una acción remota depende del Flujo Operativo, pero el Flujo Operativo no depende de usuarios.
5. Echo no administra globalmente Usuarios ni Asignaciones.
6. Atlas orquesta; Echo valida y materializa las acciones locales.
7. Codex conserva la autoridad sobre las operaciones de persistencia de la Fuente de Conocimiento.
8. La Capa Administrativa puede desaparecer sin invalidar la existencia conceptual de la Capa Operativa.

## Límites

Este flujo no define:

- Cliente;
- Plan;
- Suscripción;
- Facturación;
- precios;
- renovación comercial;
- mecanismo concreto de login;
- endpoint HTTP;
- WebGUI exacta;
- tecnología de base de datos;
- protocolo de módulo concreto.

Esas responsabilidades pertenecen a otros modelos o a una Implementation.
