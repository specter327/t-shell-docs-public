## Prueba extremo a extremo en LAN

**Fecha:** 03-09-2026  
**Tipo de prueba:** integración extremo a extremo sobre red LAN  
**Estado general:** PASS

### Entorno probado

Se realizaron múltiples pruebas utilizando dos dispositivos físicos conectados a la misma red LAN:

- **Computador portátil**
  - Sistema operativo: GNU/Linux Mint
  - Funciones probadas:
    - ejecución de **Atlas**
    - ejecución simultánea de uno o más **Echo**
    - orquestación remota de Echo desde Atlas

- **Celular**
  - Sistema operativo: Android
  - Entorno operativo: Termux
  - Funciones probadas:
    - ejecución de **Echo**
    - conexión de Echo hacia Atlas
    - operación remota desde Atlas
    - ejecución de capacidades propias del entorno Termux

### Capacidades verificadas

Durante las pruebas se validó satisfactoriamente:

- conexión Echo → Atlas;
- autenticación y asociación de la entidad con su conexión;
- detección y selección de Echo desde Atlas;
- ejecución remota de comandos;
- apertura y uso de sesiones PTY interactivas;
- operación simultánea de múltiples Echo;
- operación simultánea de múltiples PTY;
- comunicación bidireccional entre Atlas y Echo.

### Módulos probados

Se probaron los siguientes módulos:

- **SHELL**
- **FTP**

Ambos módulos funcionaron correctamente dentro del entorno probado.

### Alcance de la evidencia

Estas pruebas demuestran funcionamiento extremo a extremo dentro de una red LAN y sobre los dispositivos descritos.

No demuestran todavía:

- funcionamiento sobre WAN;
- operación a gran escala;
- alta disponibilidad;
- tolerancia prolongada a fallos;
- compatibilidad general con otras plataformas;
- madurez de producción.