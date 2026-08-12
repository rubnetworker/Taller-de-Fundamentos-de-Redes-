---LAB_START---
LAB_ID: 07-00-01
---MARKDOWN---
# Práctica 7 — Elaborar Plan de Mantenimiento y Checklist Operativo

## 1. Metadatos

| Campo             | Detalle                                      |
|-------------------|----------------------------------------------|
| **Duración**      | 47 minutos                                   |
| **Complejidad**   | Alta (Hard)                                  |
| **Nivel Bloom**   | Crear (Create)                               |
| **Módulo**        | 7 — Tendencias: IoT, Redes Inteligentes y Gestión Operativa |
| **Modalidad**     | Individual — entregable documental           |

---

## 2. Descripción General

En esta práctica integradora, el estudiante asumirá el rol de **administrador de red** de *TechLogística S.A.*, una empresa mediana de 50 empleados con infraestructura de red heterogénea que incluye dispositivos IoT, equipos Cisco y servicios críticos. A partir de la descripción del entorno proporcionada, el estudiante creará un conjunto completo de documentos operativos: un plan de mantenimiento preventivo estructurado por frecuencia, un checklist operativo en formato tabla, un análisis de impacto IoT y una plantilla de documentación de red. Esta práctica consolida e integra los conocimientos adquiridos en todos los módulos anteriores del curso, con énfasis especial en los conceptos de IoT y redes inteligentes del módulo 7.

---

## 3. Objetivos de Aprendizaje

Al finalizar esta práctica, el estudiante será capaz de:

- [ ] **Diseñar** un plan de mantenimiento preventivo completo con actividades clasificadas por frecuencia (diaria, semanal, mensual, trimestral y anual), incluyendo procedimientos, herramientas, tiempo estimado y criterios de éxito.
- [ ] **Elaborar** un checklist operativo en formato tabla que sirva como guía de verificación rutinaria para el administrador de red.
- [ ] **Analizar** el impacto de los 10 dispositivos IoT de la organización sobre la segmentación, el ancho de banda, la seguridad y los requerimientos de monitoreo, proponiendo ajustes concretos al plan de mantenimiento.
- [ ] **Crear** una plantilla de documentación de red con inventario de dispositivos, tabla de direccionamiento IP, descripción de topología y registro de cambios, aplicando buenas prácticas de gestión.

---

## 4. Prerrequisitos

### Conocimiento previo requerido

| Área                              | Prácticas relacionadas |
|-----------------------------------|------------------------|
| Diagnóstico y comandos de red (ping, traceroute, ipconfig) | Práctica 1 |
| Modelo OSI / TCP-IP y análisis de tráfico con Wireshark    | Práctica 1 y 2 |
| Configuración de switches y VLANs en Packet Tracer         | Práctica 3 |
| Configuración de routers, DHCP, NAT y DNS                  | Práctica 4 |
| Ingeniería social y estrategias de mitigación              | Práctica 5 |
| Direccionamiento IPv4/IPv6 y subredes                      | Práctica 6 |
| Conceptos de IoT y redes inteligentes (SDN, NFV, Edge Computing) | Módulo 7.1 |

### Acceso y herramientas

| Requisito                          | Obligatorio |
|------------------------------------|-------------|
| Editor de texto o procesador de documentos (Word, LibreOffice, VS Code, Notepad++) | ✅ Sí |
| Navegador web (para consultar referencias)                 | ✅ Sí |
| Calculadora de subredes (ipcalc, subnet-calculator.com)    | ✅ Sí |
| Cisco Packet Tracer (para verificar topología de referencia) | ⬜ Opcional |

> **Nota:** Esta práctica es predominantemente documental. No requiere conexión a equipos físicos ni simulador activo, aunque se recomienda tener Packet Tracer disponible para revisar la topología construida en las Prácticas 3 y 4.

---

## 5. Entorno de Laboratorio

### Descripción de la organización ficticia: *TechLogística S.A.*

Lee cuidadosamente la siguiente descripción. Todos los entregables de esta práctica se basarán en este escenario.

**TechLogística S.A.** es una empresa de logística y distribución con sede en una sola oficina de dos pisos. Cuenta con 50 empleados distribuidos en cuatro departamentos: Administración (10 personas), Operaciones (20 personas), TI (5 personas) y Almacén (15 personas). La empresa opera 24/7, por lo que la disponibilidad de la red es crítica.

#### Inventario de infraestructura de red (punto de partida)

| # | Dispositivo              | Modelo/Tipo         | Ubicación        | Rol                          |
|---|--------------------------|---------------------|------------------|------------------------------|
| 1 | Router-WAN               | Cisco ISR 4321      | Rack Piso 1      | Gateway a Internet / NAT     |
| 2 | Router-LAN               | Cisco ISR 4321      | Rack Piso 2      | Enrutamiento inter-VLAN      |
| 3 | SW-Core                  | Cisco Catalyst 2960 | Rack Piso 1      | Switch core / troncal        |
| 4 | SW-Admin                 | Cisco Catalyst 2960 | Piso 1 - Admin   | Acceso VLAN 10 (Admón.)      |
| 5 | SW-Ops                   | Cisco Catalyst 2960 | Piso 1 - Ops     | Acceso VLAN 20 (Operaciones) |
| 6 | SW-Almacen               | Cisco Catalyst 2960 | Piso 2 - Almacén | Acceso VLAN 30 (Almacén)     |
| 7 | SRV-01                   | Servidor Dell R340  | Rack Piso 1      | DHCP, DNS, File Server       |
| 8 | AP-Piso1                 | Cisco WAP571        | Piso 1           | Wi-Fi empleados (SSID: Corp) |
| 9 | AP-Piso2                 | Cisco WAP571        | Piso 2           | Wi-Fi empleados (SSID: Corp) |
| 10 | FW-01                   | Cisco ASA 5506-X    | Rack Piso 1      | Firewall perimetral          |

#### Dispositivos IoT (10 unidades)

| # | Dispositivo IoT          | Tecnología  | Función                          | VLAN propuesta |
|---|--------------------------|-------------|----------------------------------|----------------|
| 1 | Cámara IP Lobby          | Wi-Fi       | Videovigilancia entrada          | VLAN 40 (IoT)  |
| 2 | Cámara IP Almacén        | Wi-Fi       | Videovigilancia almacén          | VLAN 40 (IoT)  |
| 3 | Cámara IP Parking        | Wi-Fi       | Videovigilancia exterior         | VLAN 40 (IoT)  |
| 4 | Termostato inteligente   | Zigbee/Wi-Fi| Control climatización Piso 1     | VLAN 40 (IoT)  |
| 5 | Termostato inteligente   | Zigbee/Wi-Fi| Control climatización Piso 2     | VLAN 40 (IoT)  |
| 6 | Sensor de acceso Piso 1  | BLE/Wi-Fi   | Control de acceso puerta principal| VLAN 40 (IoT) |
| 7 | Sensor de acceso Piso 2  | BLE/Wi-Fi   | Control de acceso sala servidores| VLAN 40 (IoT)  |
| 8 | Impresora inteligente    | Wi-Fi       | Impresión red corporativa        | VLAN 40 (IoT)  |
| 9 | UPS inteligente          | Ethernet    | Monitoreo energía rack Piso 1    | VLAN 40 (IoT)  |
| 10| Sensor ambiental rack    | Ethernet    | Temperatura/humedad sala servidores| VLAN 40 (IoT)|

#### Esquema de direccionamiento IP de referencia

| VLAN | Nombre        | Red             | Gateway         | Rango DHCP          |
|------|---------------|-----------------|-----------------|---------------------|
| 10   | Administración| 192.168.10.0/24 | 192.168.10.1    | .100 – .150         |
| 20   | Operaciones   | 192.168.20.0/24 | 192.168.20.1    | .100 – .200         |
| 30   | Almacén       | 192.168.30.0/24 | 192.168.30.1    | .100 – .200         |
| 40   | IoT           | 192.168.40.0/24 | 192.168.40.1    | .50 – .100          |
| 99   | Gestión       | 192.168.99.0/24 | 192.168.99.1    | Estático (manual)   |

---

## 6. Pasos del Laboratorio

> **Instrucciones generales:** Crea un documento único en tu procesador de textos con el nombre `Lab07_TuNombre_TechLogistica.docx` (o `.odt` / `.md`). Cada paso corresponde a una sección del documento final. Sigue el orden indicado y usa las plantillas y tablas proporcionadas como punto de partida.

---

### Paso 1 — Crear la Plantilla de Documentación de Red

**Objetivo:** Establecer la base documental del entorno de red de TechLogística S.A. mediante un inventario estructurado, tabla de direccionamiento y registro de cambios.

#### Instrucciones

1. Abre tu editor de texto y crea el documento `Lab07_TuNombre_TechLogistica`.

2. Crea una portada con los siguientes campos:

```
DOCUMENTACIÓN DE RED — TechLogística S.A.
Versión: 1.0
Fecha de creación: [fecha actual]
Elaborado por: [tu nombre]
Revisado por: [pendiente]
Clasificación: Confidencial — Uso interno
```

3. Crea la **Sección 1: Inventario de Dispositivos**. Copia la tabla de inventario del escenario (Paso 5) y amplíala con dos columnas adicionales: **Dirección IP de gestión** y **Versión de firmware/SO**. Completa la columna de dirección IP de gestión usando la VLAN 99 (192.168.99.0/24), asignando direcciones estáticas desde la .10 en adelante. Para la versión de firmware, usa el valor `[Por verificar]` como marcador de posición.

   Ejemplo de las primeras dos filas:

   | # | Dispositivo | Modelo | Ubicación | Rol | IP Gestión | Firmware |
   |---|-------------|--------|-----------|-----|------------|----------|
   | 1 | Router-WAN  | Cisco ISR 4321 | Rack Piso 1 | Gateway/NAT | 192.168.99.10 | [Por verificar] |
   | 2 | Router-LAN  | Cisco ISR 4321 | Rack Piso 2 | Inter-VLAN  | 192.168.99.11 | [Por verificar] |

4. Crea la **Sección 2: Tabla de Direccionamiento IP**. Copia la tabla de esquema de direccionamiento del escenario y añade una columna **Notas** donde indiques observaciones relevantes (por ejemplo: *"VLAN IoT: sin acceso a VLAN 10, 20, 30 por ACL"*).

5. Crea la **Sección 3: Descripción de Topología**. Escribe un párrafo de 100–150 palabras describiendo la topología lógica de la red: cómo se conectan los routers, switches, el servidor y los dispositivos IoT. Incluye la mención de las VLANs, el firewall perimetral y los puntos de acceso Wi-Fi. Describe el flujo de tráfico desde un empleado de Operaciones hasta Internet.

6. Crea la **Sección 4: Registro de Cambios**. Usa la siguiente tabla como plantilla:

   | # | Fecha | Descripción del cambio | Realizado por | Aprobado por | Impacto | Estado |
   |---|-------|------------------------|---------------|--------------|---------|--------|
   | 001 | [fecha actual] | Creación del documento inicial de red | [tu nombre] | Pendiente | Ninguno | Completado |
   | 002 | | | | | | |

   > **Criterio de éxito:** El documento contiene las cuatro secciones con datos completos y coherentes. El inventario incluye todos los 10 dispositivos de red + 10 dispositivos IoT. Las IPs de gestión no se solapan y pertenecen a la VLAN 99.

#### Salida esperada

Un documento con cuatro secciones claramente definidas: inventario completo (20 dispositivos), tabla de direccionamiento con 5 VLANs, párrafo descriptivo de topología coherente con el escenario, y registro de cambios con al menos la entrada inicial.

#### Verificación

- [ ] ¿El inventario incluye los 10 dispositivos de infraestructura y los 10 dispositivos IoT?
- [ ] ¿Todas las IPs de gestión pertenecen a 192.168.99.0/24 y son únicas?
- [ ] ¿La descripción de topología menciona VLANs, firewall y flujo hasta Internet?
- [ ] ¿El registro de cambios tiene al menos una entrada con todos los campos completos?

---

### Paso 2 — Elaborar el Plan de Mantenimiento Preventivo

**Objetivo:** Diseñar un plan de mantenimiento preventivo estructurado por frecuencia, especificando para cada actividad el procedimiento, las herramientas necesarias, el tiempo estimado y el criterio de éxito.

#### Instrucciones

1. Crea la **Sección 5: Plan de Mantenimiento Preventivo** en tu documento.

2. Incluye una **introducción de una página** que explique la filosofía del mantenimiento preventivo en redes: por qué es preferible prevenir fallos que reaccionar ante ellos, y cómo este plan se alinea con principios básicos de ITIL (gestión de cambios, gestión de disponibilidad).

3. Para cada frecuencia indicada a continuación, crea una subsección con una tabla de actividades. Cada tabla debe tener las columnas: **ID**, **Actividad**, **Procedimiento resumido**, **Herramienta(s)**, **Tiempo estimado**, **Responsable** y **Criterio de éxito**.

   Usa la siguiente estructura base y **completa al menos el número mínimo de actividades indicado para cada frecuencia**. Se proporcionan ejemplos de partida que debes expandir y adaptar:

---

#### 2a. Actividades Diarias (mínimo 5 actividades)

| ID    | Actividad                          | Procedimiento resumido | Herramienta(s) | Tiempo est. | Responsable | Criterio de éxito |
|-------|------------------------------------|------------------------|----------------|-------------|-------------|-------------------|
| D-01  | Verificar conectividad a Internet  | Ejecutar `ping 8.8.8.8` desde SRV-01 y desde una PC de cada VLAN. Registrar latencia. | Terminal / CMD | 5 min | Técnico TI | Ping exitoso (<100 ms) desde todas las VLANs |
| D-02  | Revisar logs del firewall (FW-01)  | Acceder a la consola del ASA o ASDM, revisar eventos de las últimas 24 h, identificar intentos de acceso no autorizado. | Cisco ASDM / SSH | 10 min | Admin. TI | Sin alertas críticas sin atender |
| D-03  | Verificar disponibilidad de servicios DHCP y DNS | Desde una PC cliente, ejecutar `ipconfig /release` + `ipconfig /renew` (Windows) o `dhclient -r && dhclient` (Linux). Verificar resolución DNS con `nslookup techlogistica.local`. | CMD / Terminal | 5 min | Técnico TI | IP asignada correctamente; DNS resuelve nombre interno |
| D-04  | Monitorear dispositivos IoT activos | Revisar dashboard de gestión IoT (o hacer ping a cada IP de la VLAN 40). Verificar que los 10 dispositivos respondan. | Dashboard IoT / Terminal | 5 min | Técnico TI | Los 10 dispositivos IoT responden al ping |
| D-05  | [Actividad a completar por el estudiante] | [Describir procedimiento] | [Herramientas] | [Tiempo] | [Responsable] | [Criterio] |

> **Instrucción para el estudiante:** Completa D-05 y agrega al menos una actividad adicional (D-06) relacionada con la verificación del servidor SRV-01 (espacio en disco, servicios activos). Usa los comandos relevantes del sistema operativo del servidor.

**Comandos de referencia para actividades diarias:**

```bash
# Windows — Verificar conectividad y DNS
ping 8.8.8.8
ping 192.168.99.10
nslookup techlogistica.local 192.168.10.1
ipconfig /all

# Linux/macOS — Equivalentes
ping -c 4 8.8.8.8
ping -c 4 192.168.99.10
dig techlogistica.local @192.168.10.1
ip addr show

# Linux — Verificar espacio en disco del servidor
df -h
# Verificar servicios activos (systemd)
systemctl status isc-dhcp-server
systemctl status bind9
```

---

#### 2b. Actividades Semanales (mínimo 5 actividades)

| ID    | Actividad                              | Procedimiento resumido | Herramienta(s) | Tiempo est. | Responsable | Criterio de éxito |
|-------|----------------------------------------|------------------------|----------------|-------------|-------------|-------------------|
| S-01  | Revisar utilización de ancho de banda  | Consultar estadísticas de interfaces en routers mediante SNMP o acceso SSH. Identificar VLANs con tráfico inusualmente alto. | SSH / SNMP / Wireshark | 15 min | Admin. TI | Ninguna VLAN supera el 80% de su capacidad sostenida |
| S-02  | Verificar estado de puertos de switches | Acceder a cada switch por SSH, ejecutar `show interfaces status` y `show interfaces counters errors`. Documentar puertos con errores. | SSH (Cisco IOS) | 20 min | Técnico TI | Tasa de errores en interfaces < 0.1% |
| S-03  | Revisar tabla ARP y MAC de switches    | Ejecutar `show arp` en routers y `show mac address-table` en switches para detectar MACs desconocidas o duplicadas. | SSH (Cisco IOS) | 10 min | Admin. TI | Sin MACs no autorizadas en tablas |
| S-04  | Verificar actualizaciones de firmware de dispositivos IoT | Acceder a la consola de gestión de cada dispositivo IoT y verificar si hay actualizaciones de firmware disponibles. Documentar versiones actuales. | Navegador web / consola dispositivo | 20 min | Técnico TI | Todos los dispositivos con firmware actualizado o plan de actualización documentado |
| S-05  | [Actividad a completar por el estudiante] | [Describir procedimiento] | [Herramientas] | [Tiempo] | [Responsable] | [Criterio] |

**Comandos de referencia para actividades semanales:**

```
! Cisco IOS — Verificar estado de interfaces en switch
show interfaces status
show interfaces counters errors
show mac address-table
show arp

! Cisco IOS — Verificar utilización de interfaz
show interfaces GigabitEthernet0/1
show interfaces GigabitEthernet0/1 | include rate

! Verificar vecinos CDP (para confirmar topología física)
show cdp neighbors detail
```

> **Instrucción para el estudiante:** Completa S-05 con una actividad de revisión de la tabla de enrutamiento en ambos routers. Incluye el comando IOS correspondiente.

---

#### 2c. Actividades Mensuales (mínimo 5 actividades)

| ID    | Actividad                               | Procedimiento resumido | Herramienta(s) | Tiempo est. | Responsable | Criterio de éxito |
|-------|-----------------------------------------|------------------------|----------------|-------------|-------------|-------------------|
| M-01  | Prueba de restauración de backup de configuraciones | Restaurar la configuración de un router o switch en un entorno de prueba (Packet Tracer) desde el último backup. Verificar que la configuración es funcional. | Packet Tracer / TFTP | 30 min | Admin. TI | Configuración restaurada sin errores; dispositivo opera correctamente |
| M-02  | Auditoría de cuentas de usuario en dispositivos de red | Revisar usuarios locales en routers, switches y servidor. Eliminar cuentas inactivas. Verificar que no existan cuentas con contraseña predeterminada. | SSH / Consola | 20 min | Admin. TI | Sin cuentas inactivas; todas las contraseñas cumplen política de seguridad |
| M-03  | Revisión de reglas ACL y políticas de firewall | Revisar todas las ACLs en routers y reglas del ASA. Identificar reglas obsoletas o demasiado permisivas. Documentar cambios recomendados. | SSH / ASDM | 45 min | Admin. TI | ACLs documentadas; reglas obsoletas marcadas para eliminación en próximo cambio |
| M-04  | Verificar rendimiento de la red Wi-Fi  | Realizar prueba de velocidad desde distintas ubicaciones del piso 1 y piso 2. Verificar cobertura y throughput de ambos APs. | iPerf / Speedtest | 30 min | Técnico TI | Throughput > 50 Mbps a 5 metros del AP; cobertura sin zonas muertas |
| M-05  | Revisión de capacidad de almacenamiento del servidor | Verificar espacio en disco, uso de memoria RAM y CPU promedio de SRV-01 durante el último mes. Proyectar crecimiento. | SSH / Consola servidor | 15 min | Admin. TI | Disco < 75% de uso; RAM < 80%; sin alertas de rendimiento |

> **Instrucción para el estudiante:** Agrega al menos dos actividades mensuales adicionales (M-06, M-07) relacionadas con: (a) revisión de logs de Syslog del último mes y (b) prueba de funcionamiento del sistema UPS inteligente (dispositivo IoT #9).

**Comandos de referencia para actividades mensuales:**

```bash
# Linux — Verificar uso de disco y memoria en SRV-01
df -h
free -h
top -bn1 | head -20

# Verificar logs del sistema (Linux)
journalctl --since "30 days ago" --priority=err
grep -i "error\|critical\|fail" /var/log/syslog | tail -50

# Backup de configuración Cisco IOS via TFTP
! En el router/switch:
copy running-config tftp://192.168.99.100/backup-router-wan-$(date).cfg
```

```
! Cisco IOS — Verificar y auditar usuarios locales
show running-config | include username
show users

! Revisar ACLs
show ip access-lists
show running-config | section access-list
```

---

#### 2d. Actividades Trimestrales (mínimo 4 actividades)

| ID    | Actividad                               | Procedimiento resumido | Herramienta(s) | Tiempo est. | Responsable | Criterio de éxito |
|-------|-----------------------------------------|------------------------|----------------|-------------|-------------|-------------------|
| T-01  | Actualización de firmware de dispositivos de red | Planificar ventana de mantenimiento. Descargar firmware desde Cisco.com. Actualizar Router-WAN, Router-LAN y switches en orden (primero switches de acceso, luego core, finalmente routers). | SSH / TFTP / Cisco.com | 3 horas | Admin. TI | Todos los dispositivos con firmware actualizado; sin degradación de servicio post-actualización |
| T-02  | Prueba de plan de continuidad (DR drill) | Simular la caída del Router-WAN. Verificar que el tráfico falle o que el equipo de TI pueda restablecer conectividad en < 30 minutos. Documentar tiempo de recuperación (RTO). | Simulación controlada | 60 min | Admin. TI + Gerencia TI | RTO documentado; procedimiento de recuperación validado |
| T-03  | Revisión del inventario físico de la red | Verificar físicamente que todos los dispositivos del inventario estén presentes, etiquetados y en buen estado. Actualizar el documento de inventario. | Inventario físico | 60 min | Técnico TI | Inventario 100% coincidente con documentación; etiquetas legibles |
| T-04  | [Actividad a completar por el estudiante] | [Describir procedimiento relacionado con revisión de seguridad de la VLAN IoT y prueba de aislamiento] | [Herramientas] | [Tiempo] | [Responsable] | [Criterio] |

> **Instrucción para el estudiante:** Completa T-04 con una actividad de prueba de aislamiento de la VLAN 40 (IoT). Describe cómo verificarías que los dispositivos IoT no pueden acceder a la VLAN 10, 20 o 30. Incluye el comando de prueba relevante.

---

#### 2e. Actividades Anuales (mínimo 3 actividades)

| ID    | Actividad                               | Procedimiento resumido | Herramienta(s) | Tiempo est. | Responsable | Criterio de éxito |
|-------|-----------------------------------------|------------------------|----------------|-------------|-------------|-------------------|
| A-01  | Revisión y actualización del plan de mantenimiento | Evaluar la efectividad del plan del año anterior. Incorporar nuevos dispositivos, tecnologías (IoT, SDN) y lecciones aprendidas de incidentes. | Documentación / Reunión | 4 horas | Admin. TI + Gerencia | Plan actualizado aprobado por gerencia; cambios documentados en registro |
| A-02  | Auditoría de seguridad completa         | Contratar o ejecutar internamente una auditoría de seguridad que incluya: escaneo de vulnerabilidades, revisión de contraseñas, prueba de penetración básica en VLAN IoT, revisión de políticas. | Nessus / OpenVAS / Nmap | 1 día | Admin. TI + Externo | Informe de auditoría generado; vulnerabilidades críticas remediadas en < 30 días |
| A-03  | Revisión de capacidad y planificación de crecimiento | Analizar tendencias de tráfico del año. Evaluar si la infraestructura actual soportará el crecimiento proyectado (nuevos empleados, más dispositivos IoT). Presentar recomendaciones de inversión. | SNMP / Grafana / Excel | 4 horas | Admin. TI + Gerencia | Informe de capacidad presentado; recomendaciones de inversión documentadas |

> **Criterio de éxito del Paso 2:** El plan de mantenimiento contiene las cinco frecuencias, cada una con el mínimo de actividades requerido. Todas las actividades tienen los siete campos completos. Los comandos de referencia son correctos y coherentes con el entorno de TechLogística.

---

### Paso 3 — Elaborar el Checklist Operativo

**Objetivo:** Crear un checklist operativo práctico en formato tabla que el administrador de red pueda usar como guía de verificación rutinaria, con casillas de verificación y espacio para observaciones.

#### Instrucciones

1. Crea la **Sección 6: Checklist Operativo** en tu documento.

2. Diseña **tres checklists separados** (diario, semanal y mensual) con el siguiente formato. Cada ítem debe tener: número, tarea concisa, indicador de verificación (✅ / ❌ / ⚠️) y columna de observaciones.

3. Copia las plantillas siguientes y **completa los ítems marcados como `[COMPLETAR]`**:

---

#### Checklist Diario — TechLogística S.A.

**Fecha:** _____________ | **Turno:** _____________ | **Técnico:** _____________

| # | Tarea de Verificación | ✅/❌/⚠️ | Observaciones |
|---|-----------------------|----------|---------------|
| 1 | Ping exitoso a 8.8.8.8 desde SRV-01 (latencia < 100 ms) | | |
| 2 | Ping exitoso desde PC-VLAN10 a gateway 192.168.10.1 | | |
| 3 | Ping exitoso desde PC-VLAN20 a gateway 192.168.20.1 | | |
| 4 | Ping exitoso desde PC-VLAN30 a gateway 192.168.30.1 | | |
| 5 | Servicio DHCP activo en SRV-01 (verificar con `systemctl status`) | | |
| 6 | Servicio DNS activo en SRV-01 — resolución de `techlogistica.local` exitosa | | |
| 7 | Sin alertas críticas en logs del firewall FW-01 (últimas 24 h) | | |
| 8 | Los 10 dispositivos IoT (VLAN 40) responden al ping | | |
| 9 | Cámaras IP (3 unidades) con imagen activa y grabación funcionando | | |
| 10 | [COMPLETAR: verificación relacionada con el UPS inteligente] | | |
| 11 | [COMPLETAR: verificación del sensor ambiental del rack (temperatura < umbral)] | | |
| 12 | [COMPLETAR: verificación de espacio en disco de SRV-01] | | |

**Firma del técnico:** _________________ | **Hora de finalización:** _________________

---

#### Checklist Semanal — TechLogística S.A.

**Semana del:** _____________ **al** _____________ | **Responsable:** _____________

| # | Tarea de Verificación | ✅/❌/⚠️ | Observaciones |
|---|-----------------------|----------|---------------|
| 1 | Revisión de estadísticas de interfaces en SW-Core (`show interfaces status`) | | |
| 2 | Verificación de errores en interfaces de todos los switches (tasa < 0.1%) | | |
| 3 | Revisión de tabla ARP en Router-LAN (`show arp`) — sin entradas sospechosas | | |
| 4 | Revisión de tabla MAC en SW-Core (`show mac address-table`) — sin MACs no autorizadas | | |
| 5 | Verificación de utilización de ancho de banda en enlace WAN (< 80%) | | |
| 6 | Revisión de actualizaciones de firmware disponibles para dispositivos IoT | | |
| 7 | Verificación de cobertura Wi-Fi en puntos críticos de piso 1 y piso 2 | | |
| 8 | [COMPLETAR: verificación de la tabla de enrutamiento en Router-WAN y Router-LAN] | | |
| 9 | [COMPLETAR: revisión de logs de Syslog de la semana — identificar patrones] | | |
| 10 | Backup semanal de configuraciones de routers y switches completado y verificado | | |

**Firma del responsable:** _________________ | **Fecha de cierre:** _________________

---

#### Checklist Mensual — TechLogística S.A.

**Mes:** _____________ | **Año:** _____________ | **Admin. de Red:** _____________

| # | Tarea de Verificación | ✅/❌/⚠️ | Fecha realización | Observaciones |
|---|-----------------------|----------|-------------------|---------------|
| 1 | Prueba de restauración de backup en Packet Tracer completada exitosamente | | | |
| 2 | Auditoría de cuentas de usuario — sin cuentas inactivas ni con contraseña por defecto | | | |
| 3 | Revisión de ACLs en routers — reglas obsoletas identificadas y documentadas | | | |
| 4 | Prueba de velocidad Wi-Fi (throughput > 50 Mbps a 5 m del AP) | | | |
| 5 | Uso de disco SRV-01 < 75% — proyección de crecimiento documentada | | | |
| 6 | Revisión de logs del mes — errores críticos atendidos y cerrados | | | |
| 7 | Prueba de funcionamiento del UPS inteligente — autonomía verificada | | | |
| 8 | [COMPLETAR: verificación de aislamiento VLAN IoT — dispositivos IoT no alcanzan VLANs 10/20/30] | | | |
| 9 | [COMPLETAR: revisión de política de contraseñas de dispositivos IoT] | | | |
| 10 | Registro de cambios del mes actualizado y firmado por el administrador | | | |

**Firma del administrador:** _________________ | **Aprobado por:** _________________

> **Criterio de éxito del Paso 3:** Los tres checklists están completos con todos los ítems `[COMPLETAR]` resueltos. El formato es consistente y cada checklist incluye campos de firma y fecha. Los ítems son accionables (describen una tarea verificable, no una intención).

#### Verificación

- [ ] ¿Cada checklist tiene al menos 10 ítems completos?
- [ ] ¿Los ítems `[COMPLETAR]` han sido rellenados con tareas coherentes con el escenario?
- [ ] ¿Los checklists incluyen campos de firma, fecha y responsable?
- [ ] ¿Los ítems son verificables (tienen un criterio implícito de éxito/fallo)?

---

### Paso 4 — Análisis de Impacto IoT

**Objetivo:** Analizar el impacto de los 10 dispositivos IoT de TechLogística sobre cuatro dimensiones críticas de la red, y proponer ajustes concretos al plan de mantenimiento.

#### Instrucciones

1. Crea la **Sección 7: Análisis de Impacto IoT** en tu documento.

2. Desarrolla el análisis en cuatro subsecciones, respondiendo las preguntas guía con base en los conceptos del módulo 7.1:

---

#### 4a. Impacto en la Segmentación de Red

Responde las siguientes preguntas en formato de párrafo (mínimo 100 palabras):

- ¿Por qué es necesario aislar los dispositivos IoT en una VLAN separada (VLAN 40)?
- ¿Qué riesgos específicos existirían si las cámaras IP o los termostatos estuvieran en la misma VLAN que los equipos de Administración (VLAN 10)?
- Basándote en el concepto de segmentación de red del módulo 7.1, ¿qué reglas de ACL básicas deberían aplicarse entre la VLAN 40 y las VLANs corporativas?

**Ejemplo de ACL de referencia (completa y adapta según el análisis):**

```
! Cisco IOS — ACL básica para aislar VLAN IoT (VLAN 40)
! Aplicar en Router-LAN, interfaz hacia VLAN 40 (dirección: in)

ip access-list extended ACL_IOT_RESTRICT
 ! Permitir IoT hacia Internet (via NAT en Router-WAN)
 permit ip 192.168.40.0 0.0.0.255 any
 ! Denegar acceso de IoT a VLANs corporativas
 deny   ip 192.168.40.0 0.0.0.255 192.168.10.0 0.0.0.255
 deny   ip 192.168.40.0 0.0.0.255 192.168.20.0 0.0.0.255
 deny   ip 192.168.40.0 0.0.0.255 192.168.30.0 0.0.0.255
 deny   ip 192.168.40.0 0.0.0.255 192.168.99.0 0.0.0.255
 ! Permitir acceso IoT al servidor DNS únicamente (puerto 53)
 permit udp 192.168.40.0 0.0.0.255 host 192.168.10.5 eq 53

! Aplicar la ACL a la subinterfaz de VLAN 40
interface GigabitEthernet0/0.40
 ip access-group ACL_IOT_RESTRICT in
```

> **Instrucción:** Ajusta la ACL de ejemplo según tu análisis. ¿Debería la impresora inteligente (IoT #8) tener alguna excepción para comunicarse con las VLANs corporativas? Justifica tu respuesta.

---

#### 4b. Impacto en el Ancho de Banda

Completa la siguiente tabla de estimación de consumo de ancho de banda de los dispositivos IoT:

| # | Dispositivo IoT | Protocolo | Tráfico estimado | Frecuencia | Impacto en red |
|---|-----------------|-----------|------------------|------------|----------------|
| 1 | Cámara IP Lobby | Wi-Fi / RTSP | ~2 Mbps (video HD continuo) | Continuo 24/7 | Alto — requiere QoS |
| 2 | Cámara IP Almacén | Wi-Fi / RTSP | ~2 Mbps | Continuo 24/7 | Alto |
| 3 | Cámara IP Parking | Wi-Fi / RTSP | ~2 Mbps | Continuo 24/7 | Alto |
| 4 | Termostato P1 | Zigbee/MQTT | ~1 Kbps | Cada 30 seg | Muy bajo |
| 5 | Termostato P2 | Zigbee/MQTT | ~1 Kbps | Cada 30 seg | Muy bajo |
| 6 | Sensor acceso P1 | BLE/MQTT | ~5 Kbps | Por evento | Bajo |
| 7 | Sensor acceso P2 | BLE/MQTT | ~5 Kbps | Por evento | Bajo |
| 8 | Impresora inteligente | Wi-Fi | ~[COMPLETAR] | Por trabajo | [COMPLETAR] |
| 9 | UPS inteligente | Ethernet/SNMP | ~[COMPLETAR] | Cada 60 seg | [COMPLETAR] |
| 10 | Sensor ambiental rack | Ethernet/SNMP | ~[COMPLETAR] | Cada 60 seg | [COMPLETAR] |

Luego de completar la tabla, escribe un párrafo (mínimo 80 palabras) respondiendo:
- ¿Cuál es el consumo total estimado de los dispositivos IoT en Mbps?
- ¿Qué dispositivos justifican la implementación de **QoS (Quality of Service)** en la red?
- ¿Cómo el concepto de **Edge Computing** (del módulo 7.1) podría reducir el tráfico de las cámaras IP hacia la red central?

---

#### 4c. Impacto en la Seguridad

Escribe un análisis de al menos 150 palabras que aborde:

- Los tres principales riesgos de seguridad que representan los dispositivos IoT en la red de TechLogística (basándote en los desafíos descritos en el módulo 7.1, incluyendo el caso Mirai).
- Para **cada riesgo**, propón una medida de mitigación concreta aplicable al escenario.
- Explica por qué la **VLAN 40 con ACLs restrictivas** no es suficiente por sí sola y qué medidas adicionales son necesarias.

Usa la siguiente tabla como parte del análisis:

| Riesgo IoT | Dispositivos afectados | Impacto potencial | Medida de mitigación |
|------------|------------------------|-------------------|----------------------|
| Contraseñas predeterminadas sin cambiar | Cámaras IP, termostatos, sensores de acceso | Acceso no autorizado; incorporación a botnet | Política de cambio obligatorio de credenciales en instalación; auditoría mensual |
| Firmware sin actualizar con vulnerabilidades conocidas | Todos los dispositivos IoT | Explotación de CVEs públicos | Proceso de actualización trimestral documentado (actividad T-01 adaptada) |
| Comunicación sin cifrado (HTTP/MQTT sin TLS) | [COMPLETAR: identificar dispositivos específicos] | [COMPLETAR] | [COMPLETAR] |
| [COMPLETAR: cuarto riesgo] | [COMPLETAR] | [COMPLETAR] | [COMPLETAR] |

---

#### 4d. Impacto en los Requerimientos de Monitoreo

Escribe un análisis de al menos 120 palabras que describa:

- ¿Qué herramientas o protocolos de monitoreo (SNMP, Syslog, MQTT, dashboards) son más adecuados para supervisar los 10 dispositivos IoT de TechLogística?
- ¿Cómo las **redes inteligentes** (concepto del módulo 7.1) podrían automatizar la detección de anomalías en los dispositivos IoT?
- Propón **tres alertas automáticas** que el sistema de monitoreo debería generar para los dispositivos IoT. Usa el siguiente formato:

| # | Alerta | Condición que la dispara | Acción recomendada |
|---|--------|--------------------------|--------------------|
| 1 | Dispositivo IoT sin respuesta | Ping fallido por más de 5 minutos consecutivos | Notificar al técnico TI; verificar físicamente el dispositivo |
| 2 | Temperatura del rack fuera de rango | Sensor ambiental reporta T > 30°C o T < 15°C | Notificar de inmediato al administrador; verificar sistema de climatización |
| 3 | [COMPLETAR: alerta relacionada con consumo de ancho de banda de cámaras IP] | [COMPLETAR] | [COMPLETAR] |

#### 4e. Ajustes al Plan de Mantenimiento por IoT

Como resultado del análisis anterior, **propón al menos 3 ajustes o adiciones concretas** al plan de mantenimiento preventivo del Paso 2, motivadas específicamente por la presencia de los dispositivos IoT. Usa el formato:

| Ajuste | Frecuencia | Justificación basada en análisis IoT |
|--------|------------|--------------------------------------|
| Agregar actividad D-06: verificar que los 10 dispositivos IoT respondan al ping y que el sensor ambiental del rack reporte temperatura dentro del rango 18–27°C | Diaria | Los dispositivos IoT tienen mayor tasa de fallo que equipos de red tradicionales; el sensor de rack es crítico para prevenir sobrecalentamiento |
| [COMPLETAR: ajuste 2] | [Frecuencia] | [Justificación] |
| [COMPLETAR: ajuste 3] | [Frecuencia] | [Justificación] |

> **Criterio de éxito del Paso 4:** Las cuatro subsecciones están completas. Todos los `[COMPLETAR]` han sido resueltos. El análisis demuestra comprensión de los conceptos del módulo 7.1 (IoT, Edge Computing, segmentación, riesgos de seguridad, monitoreo). Los ajustes al plan de mantenimiento son coherentes con los riesgos identificados.

---

### Paso 5 — Revisión Final e Integración del Documento

**Objetivo:** Consolidar todos los entregables en un documento coherente, verificar la consistencia interna y preparar el entregable final.

#### Instrucciones

1. Verifica que tu documento tenga la siguiente estructura completa:

```
Lab07_TuNombre_TechLogistica
├── Portada
├── Tabla de contenidos (generada automáticamente)
├── Sección 1: Inventario de dispositivos
├── Sección 2: Tabla de direccionamiento IP
├── Sección 3: Descripción de topología
├── Sección 4: Registro de cambios
├── Sección 5: Plan de mantenimiento preventivo
│   ├── 5.1 Introducción
│   ├── 5.2 Actividades diarias
│   ├── 5.3 Actividades semanales
│   ├── 5.4 Actividades mensuales
│   ├── 5.5 Actividades trimestrales
│   └── 5.6 Actividades anuales
├── Sección 6: Checklist operativo
│   ├── 6.1 Checklist diario
│   ├── 6.2 Checklist semanal
│   └── 6.3 Checklist mensual
└── Sección 7: Análisis de impacto IoT
    ├── 7.1 Segmentación de red
    ├── 7.2 Ancho de banda
    ├── 7.3 Seguridad
    ├── 7.4 Monitoreo
    └── 7.5 Ajustes al plan de mantenimiento
```

2. Realiza una revisión de **consistencia interna**. Verifica los siguientes puntos:

   - [ ] Las IPs mencionadas en el plan de mantenimiento coinciden con la tabla de direccionamiento (Sección 2).
   - [ ] Los dispositivos mencionados en los checklists están todos en el inventario (Sección 1).
   - [ ] Los ajustes IoT del Paso 4e están reflejados (o marcados para incorporar) en el plan de mantenimiento del Paso 2.
   - [ ] La ACL propuesta en el análisis IoT es coherente con el esquema de VLANs (Sección 2).

3. Agrega una **entrada final al Registro de Cambios** (Sección 4) documentando la finalización del documento:

   | # | Fecha | Descripción | Realizado por | Aprobado por | Impacto | Estado |
   |---|-------|-------------|---------------|--------------|---------|--------|
   | 002 | [fecha actual] | Completado plan de mantenimiento, checklists y análisis IoT — versión 1.0 finalizada | [tu nombre] | [instructor] | Ninguno | Completado |

4. Genera la tabla de contenidos automática en tu procesador de textos (en Word: Referencias → Tabla de contenido; en LibreOffice: Insertar → Índice y tablas).

5. Guarda el documento en formato `.docx`, `.odt` o `.pdf` según las instrucciones de entrega de tu instructor.

---

## 7. Validación y Pruebas

Antes de entregar el documento, aplica la siguiente rúbrica de autoevaluación. Asigna honestamente el puntaje a cada criterio:

| Criterio | Puntaje máximo | Tu puntaje | Descripción del nivel máximo |
|----------|---------------|------------|------------------------------|
| **Plantilla de documentación** (Paso 1) | 20 pts | | Inventario completo (20 dispositivos), IPs de gestión únicas en VLAN 99, descripción de topología coherente, registro de cambios con al menos 2 entradas |
| **Plan de mantenimiento** (Paso 2) | 30 pts | | 5 frecuencias completas, mínimo de actividades por frecuencia cumplido, todos los campos de cada actividad completos, comandos de referencia correctos |
| **Checklist operativo** (Paso 3) | 20 pts | | 3 checklists completos, todos los `[COMPLETAR]` resueltos, ítems accionables y verificables, campos de firma y fecha presentes |
| **Análisis de impacto IoT** (Paso 4) | 25 pts | | 4 subsecciones completas, todos los `[COMPLETAR]` resueltos, análisis demuestra comprensión de conceptos del módulo 7.1, al menos 3 ajustes al plan justificados |
| **Consistencia e integración** (Paso 5) | 5 pts | | IPs consistentes, dispositivos consistentes, ACL coherente con VLANs, tabla de contenidos generada |
| **TOTAL** | **100 pts** | | |

**Escala de calificación:**
- 90–100 pts: Excelente — documento listo para uso operativo real
- 75–89 pts: Bueno — cubre todos los requisitos con algunas áreas de mejora
- 60–74 pts: Suficiente — cubre los requisitos mínimos
- < 60 pts: Insuficiente — revisar y resubmitir

---

## 8. Resolución de Problemas

### Problema 1: Inconsistencia entre el plan de mantenimiento y la documentación de red

**Síntoma:** Al revisar el documento, se detecta que el plan de mantenimiento menciona la dirección IP `192.168.1.1` como gateway de la VLAN de Administración, pero la tabla de direccionamiento (Sección 2) indica `192.168.10.1`. De manera similar, un checklist menciona un dispositivo llamado "SW-IT" que no aparece en el inventario.

**Causa:** El estudiante completó las secciones en orden no secuencial o copió fragmentos de ejemplos genéricos de internet sin adaptarlos al escenario específico de TechLogística S.A. La falta de revisión de consistencia interna al finalizar genera referencias cruzadas rotas.

**Solución:**
1. Usa la función de búsqueda (`Ctrl+F`) de tu procesador de textos para buscar todas las ocurrencias de IPs y nombres de dispositivos en el documento.
2. Crea una lista de referencia rápida con los datos canónicos del escenario (IPs de gateways, nombres exactos de dispositivos) y pégala como nota temporal al inicio del documento mientras trabajas.
3. Aplica la lista de verificación de consistencia del Paso 5, ítem por ítem, antes de entregar.
4. Si encontraste inconsistencias, corrígelas y actualiza el Registro de Cambios con una entrada que documente la corrección.

---

### Problema 2: Dificultad para completar el análisis de impacto IoT por falta de datos técnicos

**Síntoma:** El estudiante no puede completar la tabla de consumo de ancho de banda de los dispositivos IoT (Paso 4b) porque no conoce los valores exactos de tráfico de los dispositivos. De manera similar, no sabe qué escribir en los campos de riesgo y mitigación del Paso 4c porque siente que "no tiene suficiente información".

**Causa:** El análisis IoT no requiere datos exactos de dispositivos reales, sino **estimaciones razonadas** basadas en los conceptos del módulo 7.1. El estudiante está buscando precisión técnica donde se espera razonamiento analítico aplicado al escenario ficticio. Adicionalmente, puede no haber revisado suficientemente el material del módulo 7.1 antes de comenzar la práctica.

**Solución:**
1. Vuelve a leer la sección "Desafíos y Consideraciones para la Adopción" del módulo 7.1, específicamente los puntos sobre seguridad IoT, el caso Mirai y las recomendaciones de segmentación.
2. Para el ancho de banda, usa órdenes de magnitud razonables:
   - Video HD continuo: 1–4 Mbps por cámara.
   - Sensores MQTT con datos pequeños: 1–10 Kbps.
   - Impresoras en uso activo: 1–10 Mbps; en espera: < 10 Kbps.
   - Dispositivos SNMP (UPS, sensor ambiental): < 50 Kbps.
3. Para los riesgos, aplica directamente los tres riesgos mencionados en el módulo 7.1 (contraseñas predeterminadas, ausencia de actualizaciones, protocolos sin cifrado) y añade un cuarto de tu propia reflexión.
4. Recuerda: en un documento operativo real, los valores exactos se obtendrían de las hojas de especificaciones del fabricante. En esta práctica, la estimación razonada y justificada es suficiente y es precisamente lo que se evalúa.

---

## 9. Limpieza

Al finalizar la práctica, realiza las siguientes acciones:

1. **Guarda el documento final** en la ubicación designada por tu instructor (carpeta del curso, LMS, correo electrónico, etc.).

2. **Verifica el nombre del archivo:** Debe seguir la convención `Lab07_[TuApellido][TuNombre]_TechLogistica.[extensión]`. Ejemplo: `Lab07_GarciaCarlos_TechLogistica.docx`.

3. **Crea una copia de respaldo** del documento en una ubicación diferente (nube, USB, correo electrónico a ti mismo).

4. **Cierra todos los documentos auxiliares** que hayas abierto durante la práctica (calculadora de subredes, material del módulo 7.1, etc.).

5. **No hay configuraciones de red que revertir** en esta práctica, ya que es enteramente documental. Sin embargo, si abriste Packet Tracer para revisar la topología de prácticas anteriores, ciérralo sin guardar cambios a menos que hayas realizado modificaciones intencionales.

---

## 10. Resumen

En esta práctica integradora has aplicado, de forma simultánea, conocimientos de todos los módulos del curso para producir un conjunto de documentos operativos profesionales para la empresa ficticia *TechLogística S.A.* Los entregables que has creado representan los artefactos fundamentales de la gestión operativa de redes:

| Entregable | Propósito operativo real |
|------------|--------------------------|
| Plantilla de documentación de red | Base de conocimiento que permite a cualquier técnico entender la infraestructura sin depender del conocimiento tácito de una sola persona |
| Plan de mantenimiento preventivo | Guía proactiva que reduce el tiempo de inactividad no planificado y extiende la vida útil de los equipos |
| Checklist operativo | Herramienta de verificación que garantiza consistencia en las tareas rutinarias independientemente de quién las ejecute |
| Análisis de impacto IoT | Marco analítico que adapta las prácticas de gestión tradicionales a la realidad de las redes modernas con dispositivos heterogéneos |

Los conceptos del módulo 7.1 que has aplicado directamente incluyen: la necesidad de **segmentación mediante VLANs** para aislar dispositivos IoT, el impacto del **consumo de ancho de banda** de dispositivos de video continuo, los **riesgos de seguridad inherentes al IoT** (contraseñas predeterminadas, firmware desactualizado, protocolos sin cifrado), y el valor del **monitoreo proactivo** con herramientas como SNMP y Syslog para detectar anomalías antes de que se conviertan en incidentes.

La habilidad de crear documentación operativa de calidad es tan valiosa en el mundo profesional de TI como la habilidad de configurar dispositivos. Un administrador de red que documenta bien su infraestructura protege a su organización de la pérdida de conocimiento, acelera la resolución de incidentes y facilita la incorporación de nuevos miembros al equipo.

### Recursos de referencia recomendados

| Recurso | URL / Referencia |
|---------|-----------------|
| ITIL 4 Foundation — Gestión de cambios y activos | `https://www.axelos.com/certifications/itil-service-management` |
| Cisco — Mejores prácticas de mantenimiento de red | `https://www.cisco.com/c/en/us/support/docs/availability/high-availability/15114-NW-Availability.html` |
| NIST SP 800-183 — Networks of Things (IoT Framework) | `https://csrc.nist.gov/publications/detail/sp/800-183/final` |
| ipcalc — Calculadora de subredes online | `https://jodies.de/ipcalc` |
| MQTT.org — Documentación del protocolo MQTT | `https://mqtt.org/` |
| Cisco Packet Tracer — Descarga (requiere cuenta Netacad) | `https://www.netacad.com/courses/packet-tracer` |

---

> **Nota ética y de privacidad:** Los documentos de red que has creado en esta práctica contienen información sensible de infraestructura (IPs, credenciales de referencia, topología). En un entorno real, este tipo de documentación debe clasificarse como **Confidencial — Uso interno** y almacenarse en sistemas con control de acceso. Nunca compartas documentación de red real en repositorios públicos, foros o redes sociales.

---
LAB_END---
