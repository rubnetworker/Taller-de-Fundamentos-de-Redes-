---LAB_START---
LAB_ID: 03-00-01
---MARKDOWN---
# Práctica 3 — Montaje y Diagnóstico de una LAN en Packet Tracer

## 1. Metadatos

| Campo            | Detalle                                      |
|------------------|----------------------------------------------|
| **Duración**     | 33 minutos                                   |
| **Complejidad**  | Media                                        |
| **Nivel Bloom**  | Crear (*Create*)                             |
| **Práctica N°**  | 3 de 7                                       |
| **Herramienta**  | Cisco Packet Tracer 8.2 o superior           |

---

## 2. Descripción General

En esta práctica construirás desde cero una red de área local (LAN) que simula la infraestructura de una oficina pequeña dentro de Cisco Packet Tracer. Aplicarás los conceptos de conmutación de tramas y tabla de direcciones MAC estudiados en la Lección 3.1, junto con la selección correcta de tipos de cable y la configuración básica de CLI en dispositivos Cisco IOS. La segunda fase del laboratorio introduce un escenario de diagnóstico con errores deliberados que deberás identificar y corregir usando ping y el modo simulación de Packet Tracer, documentando todo el proceso con capturas de pantalla.

> **Nota sobre privacidad y ética:** Todo el trabajo se realiza en un entorno completamente simulado. No se captura tráfico de redes ajenas ni se accede a equipos reales sin autorización.

---

## 3. Objetivos de Aprendizaje

Al completar esta práctica serás capaz de:

- [ ] Diseñar y construir una topología LAN funcional en Cisco Packet Tracer que incluya switch, router, punto de acceso inalámbrico y hosts cableados e inalámbricos.
- [ ] Seleccionar el tipo de cable correcto (directo, cruzado, consola, serial) para cada par de dispositivos interconectados.
- [ ] Configurar parámetros básicos de red (nombre de dispositivo, direcciones IP en interfaces, IPs estáticas en hosts) mediante la CLI de Cisco IOS para lograr conectividad completa entre todos los segmentos.
- [ ] Verificar y diagnosticar la conectividad usando ping y el modo simulación de Packet Tracer, identificando y corrigiendo al menos dos errores deliberados en la topología.
- [ ] Documentar el proceso de construcción y diagnóstico con capturas de pantalla organizadas en un reporte breve.

---

## 4. Prerrequisitos

### Conocimientos Previos

| Tema | Nivel Requerido |
|------|----------------|
| Direccionamiento IPv4 y subredes (Práctica 2) | Completado o equivalente |
| Rol de switches, routers y puntos de acceso (Lección 3.1 y 3.2) | Comprensión conceptual |
| Tabla de direcciones MAC y proceso de conmutación | Comprensión conceptual |
| Tipos de cables Ethernet (directo, cruzado, consola) | Comprensión básica |
| Navegación básica en CLI de Cisco IOS (modos privilegiado/global) | Comprensión básica |

### Acceso y Software

| Recurso | Requerimiento |
|---------|---------------|
| Cisco Packet Tracer | Versión 8.2 o superior instalada |
| Cuenta Cisco NetAcad | Activa y verificada en `netacad.com` |
| Editor de texto o procesador de documentos | Para el reporte final |
| Carpeta de trabajo | Crear `Practica3_LAN` en el escritorio |

> ⚠️ **Importante:** Si aún no tienes cuenta en Cisco NetAcad, regístrate en [netacad.com](https://www.netacad.com) antes de continuar. El proceso de registro puede tomar hasta 24 horas.

---

## 5. Entorno de Laboratorio

### Hardware Requerido

| Componente | Especificación Mínima |
|------------|----------------------|
| Procesador | 2 GHz dual-core |
| RAM | 4 GB (8 GB recomendado) |
| Almacenamiento libre | 500 MB para archivos `.pkt` |
| Pantalla | Resolución mínima 1280 × 768 |

### Dispositivos Simulados en Packet Tracer

| Dispositivo | Modelo en PT | Cantidad | Propósito |
|-------------|-------------|----------|-----------|
| Router | Cisco 1941 | 1 | Gateway de la LAN |
| Switch capa 2 | Cisco 2960-24TT | 1 | Conmutación de hosts cableados |
| Punto de acceso | Cisco AP-PT (Generic) | 1 | Segmento inalámbrico |
| PC de escritorio | PC-PT | 4 | Hosts cableados |
| Laptop | Laptop-PT | 2 | Hosts inalámbricos |

### Esquema de Direccionamiento IP

Utiliza este esquema a lo largo de toda la práctica. **No lo modifiques.**

| Dispositivo | Interfaz | Dirección IP | Máscara | Gateway por defecto |
|-------------|----------|-------------|---------|---------------------|
| Router R1 | Fa0/0 (hacia switch) | 192.168.10.1 | 255.255.255.0 | — |
| Router R1 | Fa0/1 (hacia AP) | 192.168.20.1 | 255.255.255.0 | — |
| PC-1 | NIC | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| PC-2 | NIC | 192.168.10.11 | 255.255.255.0 | 192.168.10.1 |
| PC-3 | NIC | 192.168.10.12 | 255.255.255.0 | 192.168.10.1 |
| PC-4 | NIC | 192.168.10.13 | 255.255.255.0 | 192.168.10.1 |
| Laptop-1 | Wireless | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |
| Laptop-2 | Wireless | 192.168.20.11 | 255.255.255.0 | 192.168.20.1 |
| Switch SW1 | VLAN 1 (mgmt) | 192.168.10.2 | 255.255.255.0 | 192.168.10.1 |

### Preparación del Entorno

Antes de comenzar, verifica que Packet Tracer abre correctamente:

1. Abre Cisco Packet Tracer.
2. Inicia sesión con tu cuenta NetAcad cuando se solicite.
3. Selecciona **File → New** para crear un proyecto en blanco.
4. Guarda el archivo inmediatamente: **File → Save As** → nombre `Practica3_LAN.pkt` dentro de la carpeta `Practica3_LAN`.

---

## 6. Procedimiento Paso a Paso

---

### Fase A: Construcción de la Topología

---

### Paso A1 — Agregar los Dispositivos al Área de Trabajo

**Objetivo:** Colocar todos los dispositivos de red en el lienzo de Packet Tracer y organizarlos visualmente de forma lógica.

**Instrucciones:**

1. En el panel inferior izquierdo, selecciona la categoría **Network Devices → Routers**.
2. Arrastra un **Cisco 1941** al área de trabajo. Colócalo en la parte superior central del lienzo.
3. Selecciona **Network Devices → Switches** y arrastra un **Cisco 2960-24TT** debajo del router, ligeramente a la izquierda.
4. Selecciona **Network Devices → Wireless Devices** y arrastra un **Access Point (AP-PT)** debajo del router, ligeramente a la derecha.
5. Selecciona **End Devices → End Devices** y arrastra **4 PC-PT** debajo del switch, distribuyéndolas horizontalmente.
6. Arrastra **2 Laptop-PT** debajo del punto de acceso.
7. Haz doble clic en cada dispositivo y en la pestaña **Config** cambia el nombre (*Display Name*) según la tabla siguiente:

| Dispositivo por defecto | Nombre a asignar |
|------------------------|-----------------|
| Router0 | R1 |
| Switch0 | SW1 |
| AccessPoint0 | AP1 |
| PC0 | PC-1 |
| PC1 | PC-2 |
| PC2 | PC-3 |
| PC3 | PC-4 |
| Laptop0 | Laptop-1 |
| Laptop1 | Laptop-2 |

8. Guarda el archivo: **Ctrl + S**.

**Salida esperada:** El lienzo muestra 9 dispositivos distribuidos visualmente, con los nombres correctos visibles debajo de cada icono.

**Verificación:** Compara tu lienzo con el siguiente esquema lógico:

```
              [ R1 ]
             /      \
         [SW1]      [AP1]
       /  |  |  \      \  \
    PC-1 PC-2 PC-3 PC-4  Laptop-1  Laptop-2
```
<img width="991" height="531" alt="1 Topologia Inicial" src="https://github.com/user-attachments/assets/77db59b9-2e53-4aef-9f29-f0091af1db77" />

---

### Paso A2 — Conectar los Dispositivos con el Cable Correcto

**Objetivo:** Interconectar todos los dispositivos utilizando el tipo de cable apropiado para cada par, aplicando las reglas de selección de cable estudiadas en el módulo.

**Referencia de selección de cable:**

| Par de dispositivos | Tipo de cable | Razón |
|--------------------|--------------|-------|
| PC → Switch | Cobre directo (*Copper Straight-Through*) | Dispositivos de diferente tipo |
| Switch → Router | Cobre directo (*Copper Straight-Through*) | Dispositivos de diferente tipo |
| Switch → Switch | Cobre cruzado (*Copper Cross-Over*) | Dispositivos del mismo tipo |
| Router → Router (serial) | Serial DCE/DTE | Enlace WAN serial |
| Laptop → AP | Inalámbrico (automático) | Conexión Wi-Fi |
| PC/Laptop → Switch/Router (administración) | Consola (*Console*) | Acceso de gestión |

> 💡 **Tip de Packet Tracer:** Si no estás seguro del cable, usa la opción **Automatically Choose Connection Type** (el rayo amarillo en el panel de cables). Sin embargo, en esta práctica debes seleccionar manualmente el cable correcto para desarrollar el criterio de selección.

**Instrucciones:**

1. En el panel inferior, selecciona **Connections** (icono de rayo) y elige **Copper Straight-Through** (línea sólida negra).
2. Haz clic en **PC-1** → selecciona la interfaz **FastEthernet0** → haz clic en **SW1** → selecciona el puerto **FastEthernet0/1**.
3. Repite el proceso para las demás PCs:
   - **PC-2** FastEthernet0 → **SW1** FastEthernet0/2
   - **PC-3** FastEthernet0 → **SW1** FastEthernet0/3
   - **PC-4** FastEthernet0 → **SW1** FastEthernet0/4
4. Conecta el switch al router: **SW1** GigabitEthernet0/1 → **R1** GigabitEthernet0/0 (cable **Copper Straight-Through**).
5. Conecta el AP al router: **AP1** puerto 0 (Internet) → **R1** GigabitEthernet0/0 (cable **Copper Straight-Through**).
6. Las laptops se asociarán al AP de forma inalámbrica en el Paso A4; **no** las conectes con cable ahora.
7. Guarda el archivo: **Ctrl + S**.

**Salida esperada:** Los indicadores de enlace en cada cable deben mostrar puntos de color. Los puntos **verdes** indican enlace activo; los puntos **naranjas/rojos** indican que el enlace está activo físicamente pero que las interfaces aún no están configuradas (esto es normal en este punto).

**Verificación:**

- Haz clic en cualquier cable y verifica que los extremos corresponden a los dispositivos y puertos indicados.
- Observa que entre SW1 y R1 el indicador puede mostrar naranja/ámbar porque las interfaces del router están apagadas por defecto en Cisco IOS. Esto se corregirá en el Paso A3.

---

### Paso A3 — Configurar el Router R1 mediante CLI

**Objetivo:** Configurar el nombre del dispositivo, las interfaces FastEthernet0/0 y FastEthernet0/1 con sus respectivas direcciones IP, y activar dichas interfaces para lograr conectividad entre los segmentos de red.

**Instrucciones:**

1. Haz doble clic en **R1** para abrir su panel de configuración.
2. Selecciona la pestaña **CLI**.
3. Presiona **Enter** para activar la consola. Si aparece el asistente de configuración inicial (*Would you like to enter the initial configuration dialog?*), escribe `no` y presiona **Enter**.
4. Ingresa al modo privilegiado y luego al modo de configuración global:

```
Router> enable
Router# configure terminal
Router(config)#
```

5. Configura el nombre del dispositivo:

```
Router(config)# hostname R1
R1(config)#
```

6. Configura la interfaz **GigabitEthernet0/0** (segmento cableado, hacia SW1):

```
R1(config)# interface GigabitEthernet0/0
R1(config-if)# description Enlace hacia SW1 - Segmento 192.168.10.0/24
R1(config-if)# ip address 192.168.10.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
```

7. Configura la interfaz **GigabitEthernet0/1** (segmento inalámbrico, hacia AP1):

```
R1(config)# interface GigabitEthernet0/1
R1(config-if)# description Enlace hacia AP1 - Segmento 192.168.20.0/24
R1(config-if)# ip address 192.168.20.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
```

8. Guarda la configuración en la NVRAM:

```
R1(config)# exit
R1# write memory
```

   O equivalentemente:

```
R1# copy running-config startup-config
```

**Salida esperada:**

Cuando ejecutes `no shutdown` en cada interfaz, deberías ver un mensaje similar a:

```
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up
```

Después de guardar, el mensaje de confirmación es:

```
Building configuration...
[OK]
```

**Verificación:**

Ejecuta los siguientes comandos de verificación en la CLI del router:

```
R1# show ip interface brief
```

La salida debe mostrar ambas interfaces con estado `up/up` y sus IPs asignadas:

```
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0        192.168.10.1    YES manual up                    up
GigabitEthernet0/1       192.168.20.1    YES manual up                    up
```

> ✅ **Indicador visual:** Los puntos de los cables conectados a R1 deben cambiar a **verde** en el lienzo de Packet Tracer.

---

### Paso A4 — Configurar la Dirección IP de Administración del Switch SW1

**Objetivo:** Asignar una dirección IP de administración al switch en la interfaz VLAN 1 para permitir acceso de gestión remota.

**Instrucciones:**

1. Haz doble clic en **SW1** y selecciona la pestaña **CLI**.
2. Presiona **Enter** y accede al modo de configuración global:

```
Switch> enable
Switch# configure terminal
```

3. Configura el nombre del dispositivo:

```
Switch(config)# hostname SW1
```

4. Accede a la interfaz de administración VLAN 1 y asigna la IP:

```
SW1(config)# interface vlan 1
SW1(config-if)# ip address 192.168.10.2 255.255.255.0
SW1(config-if)# no shutdown
SW1(config-if)# exit
```

5. Configura el gateway por defecto del switch:

```
SW1(config)# ip default-gateway 192.168.10.1
SW1(config)# exit
SW1# write memory
```

**Salida esperada:**

```
Building configuration...
[OK]
```

**Verificación:**

```
SW1# show interface vlan 1
```

La salida debe indicar que la interfaz VLAN 1 está `up/up` y con la IP `192.168.10.2`.

También puedes verificar la tabla de direcciones MAC (concepto central de la Lección 3.1):

```
SW1# show mac address-table
```

En este punto la tabla puede estar vacía o mostrar pocas entradas dinámicas, ya que aún no hay tráfico. Esto es completamente normal; la tabla se poblará cuando los hosts comiencen a comunicarse.

---

### Paso A5 — Configurar las Direcciones IP Estáticas en los Hosts Cableados

**Objetivo:** Asignar manualmente la dirección IP, máscara de subred y gateway a cada PC para que puedan comunicarse dentro de la red.

**Instrucciones:**

Repite el siguiente procedimiento para cada PC (PC-1, PC-2, PC-3, PC-4) usando los valores de la tabla de direccionamiento del Paso 5:

1. Haz doble clic en **PC-1**.
2. Selecciona la pestaña **Desktop** → **IP Configuration**.
3. Selecciona **Static** (IP estática).
4. Ingresa los valores correspondientes:

| Campo | Valor para PC-1 |
|-------|----------------|
| IP Address | 192.168.10.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.10.1 |
| DNS Server | (dejar en blanco por ahora) |

5. Cierra la ventana.
6. Repite para **PC-2** (`.11`), **PC-3** (`.12`) y **PC-4** (`.13`).

**Salida esperada:** Cada PC muestra su configuración IP sin mensajes de error.

**Verificación:**

En **PC-1**, ve a **Desktop → Command Prompt** y ejecuta:

```
C:\> ipconfig
```

La salida debe mostrar:

```
FastEthernet0 Connection:
   IP Address.............: 192.168.10.10
   Subnet Mask............: 255.255.255.0
   Default Gateway........: 192.168.10.1
```

---

### Paso A6 — Configurar el Punto de Acceso Inalámbrico y las Laptops

**Objetivo:** Configurar el AP1 con un SSID identificable y conectar las laptops al segmento inalámbrico con sus respectivas IPs estáticas.

**Instrucciones — Configurar AP1:**

1. Haz doble clic en **AP1**.
2. Selecciona la pestaña **Config**.
3. En el panel izquierdo, selecciona **Port 1** (la interfaz inalámbrica).
4. Cambia el **SSID** a: `OficinaLAN-WiFi`
5. Deja la autenticación en **Disabled** (sin contraseña) para simplificar la práctica.
6. Cierra la ventana.

<img width="1126" height="432" alt="PasoA 6" src="https://github.com/user-attachments/assets/5f8a79f8-0fa5-4540-91a3-238ca133797f" />

**Instrucciones — Configurar Laptop-1:**

1. Haz doble clic en **Laptop-1**.
2. Verifica que tiene instalada una tarjeta inalámbrica: pestaña **Physical**. Si la laptop tiene únicamente tarjeta Ethernet, deberás reemplazarla:
   - Apaga la laptop (botón de encendido en la pestaña Physical).
   - Arrastra la tarjeta **PT-LAPTOP-NM-1W** desde el panel de módulos al slot disponible.
   - Enciende la laptop nuevamente.
3. Ve a **Desktop → PC Wireless**.
4. Selecciona la pestaña **Connect** → haz clic en **Refresh**.
5. Selecciona la red `OficinaLAN-WiFi` y haz clic en **Connect**.
6. Cierra la ventana de PC Wireless.

<img width="1126" height="432" alt="PasoA 6" src="https://github.com/user-attachments/assets/b4f522e7-48f4-4871-8f14-55de09f84749" />

7. Ve a **Desktop → IP Configuration** → **Static**:

| Campo | Valor para Laptop-1 |
|-------|---------------------|
| IP Address | 192.168.20.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.20.1 |

8. Repite el proceso para **Laptop-2** con IP `192.168.20.11`.

**Salida esperada:** Las laptops muestran el indicador de señal inalámbrica en el lienzo y aparecen conectadas al AP1.

**Verificación:**

En **Laptop-1**, ve a **Desktop → Command Prompt**:

```
C:\> ipconfig
```

Debe mostrar la IP `192.168.20.10` con gateway `192.168.20.1`.

---

### Fase B: Verificación de Conectividad

---

### Paso B1 — Pruebas de Ping entre Hosts del Mismo Segmento

**Objetivo:** Confirmar que los hosts dentro del mismo segmento pueden comunicarse entre sí, validando el funcionamiento del switch y la tabla CAM.

**Instrucciones:**

1. Abre el **Command Prompt** de **PC-1** (Desktop → Command Prompt).
2. Ejecuta ping hacia PC-2:

```
C:\> ping 192.168.10.11
```

3. Ejecuta ping hacia PC-3:

```
C:\> ping 192.168.10.12
```

4. Desde **Laptop-1**, ejecuta ping hacia Laptop-2:

```
C:\> ping 192.168.20.11
```

**Salida esperada:**

```
Pinging 192.168.10.11 with 32 bytes of data:

Reply from 192.168.10.11: bytes=32 time<1ms TTL=128
Reply from 192.168.10.11: bytes=32 time<1ms TTL=128
Reply from 192.168.10.11: bytes=32 time<1ms TTL=128
Reply from 192.168.10.11: bytes=32 time<1ms TTL=128

Ping statistics for 192.168.10.11:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

**Verificación — Tabla CAM:**

Después de los pings, verifica que el switch ha aprendido las MACs de los hosts:

```
SW1# show mac address-table
```

Deberías ver entradas dinámicas para las MACs de PC-1, PC-2, PC-3 y PC-4, tal como se estudió en la Lección 3.1:

```
          Mac Address Table
-------------------------------------------
Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0001.xxxx.xxxx    DYNAMIC     Fa0/1
   1    0002.xxxx.xxxx    DYNAMIC     Fa0/2
   1    0003.xxxx.xxxx    DYNAMIC     Fa0/3
   1    0004.xxxx.xxxx    DYNAMIC     Fa0/4
```

> 📸 **Captura requerida #1:** Toma una captura de pantalla de la salida del comando `show mac address-table` en SW1 para tu reporte.

---

### Paso B2 — Pruebas de Ping entre Segmentos (a través del Router)

**Objetivo:** Verificar que la comunicación entre el segmento cableado (192.168.10.0/24) y el segmento inalámbrico (192.168.20.0/24) funciona correctamente a través del router R1.

**Instrucciones:**

1. Desde el **Command Prompt** de **PC-1**, ejecuta ping hacia Laptop-1:

```
C:\> ping 192.168.20.10
```

2. Desde **PC-1**, ejecuta ping hacia el gateway del segmento inalámbrico:

```
C:\> ping 192.168.20.1
```

3. Desde **Laptop-1**, ejecuta ping hacia PC-1:

```
C:\> ping 192.168.10.10
```

**Salida esperada:** Los cuatro pings deben recibir respuesta exitosa (0% de pérdida de paquetes).

**Verificación — Modo Simulación:**

1. Cambia Packet Tracer al **Modo Simulación** haciendo clic en el botón **Simulation** en la esquina inferior derecha (o presiona **Shift + S**).
2. En el panel de eventos, asegúrate de que el filtro incluye el protocolo **ICMP**.
3. Desde PC-1, ejecuta un ping a Laptop-1 (`192.168.20.10`).
4. Haz clic en **Play** o avanza los eventos manualmente con **Capture/Forward**.
5. Observa cómo los paquetes ICMP viajan: PC-1 → SW1 → R1 → AP1 → Laptop-1 y de regreso.

> 📸 **Captura requerida #2:** Toma una captura del modo simulación mostrando el recorrido del paquete ICMP entre segmentos.

---

### Fase C: Escenario de Diagnóstico — Identificar y Corregir Errores

---

> ⚠️ **Instrucciones para el instructor:** Antes de entregar esta práctica a los estudiantes, introduce los siguientes errores deliberados en el archivo `.pkt`. Si el estudiante trabaja de forma autónoma, puede introducirlos él mismo y luego resolverlos como ejercicio.
O bien descarga el Archivo .pkt aqui  [Practica3_LAN_errores.zip](https://github.com/user-attachments/files/30797466/Practica3_LAN_errores.zip)

---

### Paso C1 — Introducir los Errores Deliberados en la Topología

**Objetivo:** Preparar el escenario de diagnóstico introduciendo tres errores típicos de configuración de red.

**Instrucciones para introducir los errores:**

**Error 1 — Interfaz apagada:**

1. Abre la CLI de **R1**.
2. Ejecuta:

```
R1# configure terminal
R1(config)# interface GigabitEthernet0/1
R1(config-if)# shutdown
R1(config-if)# exit
R1(config)# exit
```

**Error 2 — Dirección IP incorrecta en PC-3:**

1. Abre **PC-3** → **Desktop** → **IP Configuration**.
2. Cambia la IP a `192.168.10.99` (fuera del rango asignado pero dentro de la subred; no genera conflicto, simplemente es una IP "incorrecta" según el esquema).

   > *Nota pedagógica:* Este error simula un error de digitación al asignar IPs manualmente.

**Error 3 — Gateway incorrecto en PC-4:**

1. Abre **PC-4** → **Desktop** → **IP Configuration**.
2. Cambia el **Default Gateway** a `192.168.10.254` (una IP que no existe en la red).

3. Guarda el archivo con un nombre diferente: `Practica3_LAN_errores.pkt`.

---

### Paso C2 — Diagnosticar y Corregir los Errores

**Objetivo:** Usar herramientas de diagnóstico de Packet Tracer para identificar sistemáticamente los tres errores introducidos y corregirlos.

**Instrucciones:**

**Diagnóstico del Error 1 — Interfaz apagada:**

1. Observa el lienzo: el cable entre **R1** y **AP1** mostrará un punto **rojo** en el extremo del router.
2. Desde **Laptop-1**, intenta hacer ping a PC-1:

```
C:\> ping 192.168.10.10
```

La respuesta será:

```
Request timeout for icmp_seq 0
Request timeout for icmp_seq 1
...
```

3. Verifica el estado de las interfaces del router:

```
R1# show ip interface brief
```

Observa que `GigabitEthernet0/1` aparece como `administratively down`:

```
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0       192.168.10.1    YES manual up                    up
GigabitEthernet0/1        192.168.20.1    YES manual administratively down down
```

4. **Corrección:**

```
R1# configure terminal
R1(config)# interface GigabitEthernet0/1
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# exit
R1# write memory
```

5. Verifica que el enlace vuelve a estar activo (punto verde) y que el ping de Laptop-1 a PC-1 ahora tiene éxito.

---

**Diagnóstico del Error 2 — IP incorrecta en PC-3:**

1. Desde **PC-1**, intenta hacer ping a PC-3 usando la IP del esquema original (`192.168.10.12`):

```
C:\> ping 192.168.10.12
```

La respuesta será `Request timeout` porque PC-3 ahora tiene la IP `.99`, no `.12`.

2. Desde **PC-3**, ejecuta `ipconfig` para verificar su IP actual:

```
C:\> ipconfig
```

Observa que la IP es `192.168.10.99` en lugar de `192.168.10.12`.

3. **Corrección:** En **PC-3** → **Desktop** → **IP Configuration**, restaura la IP correcta: `192.168.10.12`.

4. Verifica el ping desde PC-1 a PC-3 (`192.168.10.12`): debe responder exitosamente.

---

**Diagnóstico del Error 3 — Gateway incorrecto en PC-4:**

1. Desde **PC-4**, intenta hacer ping a PC-1 (mismo segmento):

```
C:\> ping 192.168.10.10
```

Este ping **sí funcionará** porque PC-1 y PC-4 están en el mismo segmento; el gateway no se usa para comunicación local.

2. Desde **PC-4**, intenta hacer ping a Laptop-1 (segmento diferente):

```
C:\> ping 192.168.20.10
```

Este ping **fallará** porque PC-4 enviará el tráfico al gateway incorrecto (`192.168.10.254`), que no existe.

3. Verifica el gateway de PC-4:

```
C:\> ipconfig
```

Observa que el **Default Gateway** es `192.168.10.254` en lugar de `192.168.10.1`.

4. **Corrección:** En **PC-4** → **Desktop** → **IP Configuration**, restaura el gateway: `192.168.10.1`.

5. Verifica que PC-4 ahora puede hacer ping a Laptop-1 (`192.168.20.10`).

> 📸 **Captura requerida #3:** Toma una captura del `show ip interface brief` de R1 mostrando la interfaz `administratively down` (antes de la corrección).

> 📸 **Captura requerida #4:** Toma una captura de los pings exitosos desde PC-4 hacia Laptop-1 después de corregir todos los errores.

---

## 7. Validación y Pruebas Finales

Una vez corregidos todos los errores, realiza la siguiente batería de pruebas para confirmar que la topología está completamente funcional.

### Matriz de Pruebas de Conectividad

Ejecuta cada ping y registra el resultado (✅ Éxito / ❌ Fallo) en tu reporte:

| # | Origen | Destino | IP Destino | Resultado Esperado |
|---|--------|---------|-----------|-------------------|
| 1 | PC-1 | PC-2 | 192.168.10.11 | ✅ Éxito |
| 2 | PC-1 | PC-4 | 192.168.10.13 | ✅ Éxito |
| 3 | PC-2 | SW1 (mgmt) | 192.168.10.2 | ✅ Éxito |
| 4 | PC-1 | R1 (Gi0/0) | 192.168.10.1 | ✅ Éxito |
| 5 | PC-1 | Laptop-1 | 192.168.20.10 | ✅ Éxito |
| 6 | PC-3 | Laptop-2 | 192.168.20.11 | ✅ Éxito |
| 7 | Laptop-1 | Laptop-2 | 192.168.20.11 | ✅ Éxito |
| 8 | Laptop-2 | PC-4 | 192.168.10.13 | ✅ Éxito |
| 9 | PC-4 | R1 (Gi0/1) | 192.168.20.1 | ✅ Éxito |

### Verificación Final de la Tabla CAM

Ejecuta el siguiente comando en SW1 y confirma que hay al menos 4 entradas dinámicas (una por cada PC):

```
SW1# show mac address-table
```

### Verificación Final del Router

Confirma que ambas interfaces del router están activas:

```
R1# show ip interface brief
R1# show running-config
```

> 📸 **Captura requerida #5:** Toma una captura del `show ip interface brief` final de R1 con ambas interfaces en estado `up/up`.

### Verificación del Modo Simulación — Recorrido Completo

1. Activa el **Modo Simulación**.
2. Filtra para mostrar únicamente el protocolo **ICMP**.
3. Ejecuta un ping desde **PC-1** hacia **Laptop-2** (`192.168.20.11`).
4. Avanza los eventos y verifica que el paquete recorre: `PC-1 → SW1 → R1 → AP1 → Laptop-2`.
5. Verifica también el paquete de respuesta en sentido inverso.

> 📸 **Captura requerida #6:** Toma una captura del modo simulación mostrando el recorrido completo del paquete ICMP de extremo a extremo.

---

## 8. Resolución de Problemas Frecuentes

---

### Problema 1: Los pings dentro del mismo segmento fallan (PC-1 no puede hacer ping a PC-2)

**Síntoma:**

```
C:\> ping 192.168.10.11

Pinging 192.168.10.11 with 32 bytes of data:
Request timeout for icmp_seq 0
Request timeout for icmp_seq 1
Request timeout for icmp_seq 2
Request timeout for icmp_seq 3
```

**Causa probable:**

El tipo de cable utilizado entre alguna PC y el switch es incorrecto (cable cruzado en lugar de cable directo), o bien la dirección IP de uno de los hosts tiene un error tipográfico (por ejemplo, máscara incorrecta como `255.255.0.0` en lugar de `255.255.255.0`, lo que coloca a los hosts en subredes diferentes).

**Diagnóstico y corrección:**

1. Verifica el tipo de cable haciendo clic sobre el cable en el lienzo: debe indicar *Copper Straight-Through*.
2. Si el cable es incorrecto, elimínalo (clic derecho → Delete) y reemplázalo con el tipo correcto.
3. Verifica la configuración IP de ambos hosts:

```
C:\> ipconfig
```

Confirma que ambos tienen la misma máscara (`255.255.255.0`) y que sus IPs pertenecen al mismo rango (`192.168.10.x`).

4. Si la máscara es incorrecta, corrígela en **Desktop → IP Configuration**.
5. Repite el ping para confirmar la corrección.

---

### Problema 2: Las laptops no aparecen conectadas al punto de acceso (no hay señal inalámbrica)

**Síntoma:** Las laptops no muestran el icono de señal Wi-Fi en el lienzo. Al intentar hacer ping desde una laptop a cualquier otro dispositivo, el resultado es `Request timeout`. En **Desktop → PC Wireless**, la red `OficinaLAN-WiFi` no aparece en la lista de redes disponibles.

**Causa probable:**

La laptop no tiene instalada una tarjeta de red inalámbrica (por defecto, las Laptop-PT en Packet Tracer pueden venir con únicamente tarjeta Ethernet). Alternativamente, el SSID del AP no fue configurado o fue escrito con un error tipográfico.

**Diagnóstico y corrección:**

1. Haz doble clic en la laptop problemática y ve a la pestaña **Physical**.
2. Verifica si hay una tarjeta inalámbrica instalada. Si solo aparece una tarjeta Ethernet (PT-LAPTOP-NM-1CFE), procede así:
   - Apaga la laptop haciendo clic en el botón de encendido (pestaña Physical).
   - Arrastra la tarjeta Ethernet fuera del slot para retirarla.
   - Desde el panel de módulos disponibles, arrastra la tarjeta **PT-LAPTOP-NM-1W** al slot vacío.
   - Enciende la laptop nuevamente.
3. Verifica el SSID del AP: haz doble clic en **AP1** → pestaña **Config** → **Port 1** → confirma que el SSID es exactamente `OficinaLAN-WiFi` (respeta mayúsculas, minúsculas y guiones).
4. En la laptop, ve a **Desktop → PC Wireless → Connect → Refresh** y selecciona la red.
5. Asigna la IP estática correspondiente en **Desktop → IP Configuration**.

---

## 9. Limpieza del Entorno

Al finalizar la práctica, realiza los siguientes pasos para dejar el entorno en orden:

1. **Guarda el archivo final** con todos los errores corregidos:
   - **File → Save As** → nombre: `Practica3_LAN_FINAL.pkt`
   - Guárdalo en la carpeta `Practica3_LAN` en el escritorio.

2. **Organiza tus capturas de pantalla:** Coloca las 6 capturas requeridas en una subcarpeta llamada `capturas` dentro de `Practica3_LAN`. Nómbralas secuencialmente:
   - `cap01_tabla_cam.png`
   - `cap02_simulacion_icmp.png`
   - `cap03_interfaz_apagada.png`
   - `cap04_pings_corregidos.png`
   - `cap05_interfaces_final.png`
   - `cap06_simulacion_extremo_a_extremo.png`

3. **Cierra Packet Tracer** correctamente desde **File → Exit** para evitar archivos corruptos.

4. **No es necesario** desinstalar ningún software ni revertir configuraciones del sistema operativo, ya que toda la práctica se realizó en un entorno simulado.

> 💡 **Para la Práctica 4:** Conserva el archivo `Practica3_LAN_FINAL.pkt`. La Práctica 4 construye directamente sobre esta topología, agregando servicios DHCP y NAT. No elimines este archivo.

---

## 10. Resumen y Recursos Adicionales

### Resumen de la Práctica

En esta práctica completaste el ciclo completo de diseño, construcción, configuración y diagnóstico de una LAN en Cisco Packet Tracer:

| Fase | Actividad | Conceptos Aplicados |
|------|-----------|---------------------|
| A | Construcción de topología | Roles de dispositivos, tipos de cable |
| A | Configuración de R1 | CLI IOS, `hostname`, `ip address`, `no shutdown` |
| A | Configuración de SW1 | Interfaz VLAN 1, IP de administración, `ip default-gateway` |
| A | Configuración de hosts | IP estática, máscara, gateway |
| A | Configuración de AP y laptops | SSID, asociación inalámbrica |
| B | Verificación de conectividad | `ping`, `show mac address-table`, modo simulación |
| C | Diagnóstico y corrección | `show ip interface brief`, análisis sistemático de errores |

### Comandos CLI Utilizados en esta Práctica

```
# Navegación de modos
enable                          ! Modo privilegiado
configure terminal              ! Modo configuración global
exit                            ! Salir un nivel

# Configuración básica
hostname <nombre>               ! Nombre del dispositivo

# Configuración de interfaces (router)
interface FastEthernet0/0       ! Seleccionar interfaz
ip address <IP> <máscara>       ! Asignar dirección IP
no shutdown                     ! Activar interfaz
shutdown                        ! Desactivar interfaz (error deliberado)
description <texto>             ! Descripción de interfaz

# Configuración de switch (administración)
interface vlan 1                ! Interfaz de gestión
ip default-gateway <IP>         ! Gateway del switch

# Guardar configuración
write memory                    ! Guardar en NVRAM
copy running-config startup-config  ! Equivalente

# Verificación
show ip interface brief         ! Estado resumido de interfaces
show mac address-table          ! Tabla de direcciones MAC (CAM)
show running-config             ! Configuración activa completa
show interface vlan 1           ! Estado de interfaz VLAN 1
```

### Conexión con la Lección 3.1

Esta práctica demostró en acción los conceptos teóricos de la Lección 3.1:

- **Tabla CAM en funcionamiento:** Después de ejecutar los pings, el comando `show mac address-table` mostró cómo el switch aprendió dinámicamente las MACs de cada host conectado a sus puertos, exactamente como se describió en el proceso de tres etapas (aprendizaje → búsqueda → reenvío).
- **Inundación inicial:** Los primeros pings generaron inundación (*flooding*) porque la tabla CAM estaba vacía; los pings subsecuentes fueron dirigidos directamente al puerto correcto.
- **Modo Store-and-Forward:** El switch 2960 opera en modo Store-and-Forward por defecto, verificando la integridad de cada trama antes de reenviarla.

### Lista de Verificación para el Reporte

Antes de entregar tu reporte, confirma que incluye:

- [ ] Diagrama de la topología final (captura del lienzo de Packet Tracer)
- [ ] Tabla de direccionamiento IP completada con los valores configurados
- [ ] Captura #1: `show mac address-table` con entradas dinámicas
- [ ] Captura #2: Modo simulación con paquete ICMP entre segmentos
- [ ] Captura #3: `show ip interface brief` con interfaz `administratively down`
- [ ] Captura #4: Pings exitosos después de corregir los errores
- [ ] Captura #5: `show ip interface brief` final con ambas interfaces `up/up`
- [ ] Captura #6: Modo simulación extremo a extremo (PC-1 → Laptop-2)
- [ ] Descripción breve (2-3 oraciones) de cada error encontrado y cómo fue corregido
- [ ] Archivo `Practica3_LAN_FINAL.pkt` adjunto o disponible para revisión

### Recursos Adicionales

| Recurso | Descripción | URL |
|---------|-------------|-----|
| Cisco Packet Tracer Tutorials | Videos oficiales de uso de PT | [netacad.com](https://www.netacad.com) |
| Cisco IOS Command Reference | Referencia completa de comandos IOS | [cisco.com/c/en/us/td/docs](https://www.cisco.com/c/en/us/td/docs) |
| NetworkLessons — Switch MAC Learning | Explicación detallada de la tabla CAM | [networklessons.com](https://networklessons.com/switching/how-a-switch-learns-mac-addresses) |
| Cisco Learning Network | Foros de soporte para estudiantes | [learningnetwork.cisco.com](https://learningnetwork.cisco.com) |

---

> **Preparación para la Práctica 4:** La siguiente práctica utilizará la topología construida aquí como base para implementar servicios DHCP en el router R1, eliminando la necesidad de configurar IPs estáticas en los hosts. También se introducirá NAT para simular el acceso a internet. Asegúrate de tener disponible el archivo `Practica3_LAN_FINAL.pkt`.

---
LAB_END---
