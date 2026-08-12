# Práctica 4 — Configurar NAT y DHCP en Router

## Metadatos

| Campo            | Detalle                          |
|------------------|----------------------------------|
| **Duración**     | 33 minutos                       |
| **Complejidad**  | Media                            |
| **Nivel Bloom**  | Aplicar (Apply)                  |
| **Herramienta**  | Cisco Packet Tracer 8.2+         |
| **Práctica**     | 4 de 7                           |

---

## Descripción General

En esta práctica configurarás dos servicios esenciales en un router Cisco simulado en Packet Tracer: **DHCP** (para asignación automática de parámetros de red a los hosts de la LAN) y **NAT/PAT** (para permitir que múltiples dispositivos privados compartan una única dirección IP pública al acceder a Internet). Partirás de la topología construida en la Práctica 3 —o de un archivo `.pkt` equivalente proporcionado por el instructor— y aplicarás la configuración directamente en la CLI del router. Al finalizar, verificarás el funcionamiento con comandos de diagnóstico y reflexionarás sobre el papel de NAT en el contexto del agotamiento de IPv4.

---

## Objetivos de Aprendizaje

- [ ] Configurar un pool DHCP en un router Cisco que asigne automáticamente dirección IP, máscara, gateway y DNS a los hosts de la LAN, excluyendo las primeras direcciones reservadas para infraestructura.
- [ ] Implementar NAT estático para que un servidor interno sea accesible desde el exterior con una IP pública fija, y PAT (NAT con sobrecarga) para que el resto de hosts compartan una única IP pública.
- [ ] Verificar el correcto funcionamiento de DHCP y NAT/PAT usando los comandos `show ip dhcp binding` y `show ip nat translations`, interpretando las traducciones de dirección.
- [ ] Explicar por escrito el impacto de NAT en la conservación del espacio de direcciones IPv4 y su relación con los principios de ruteo estudiados en la Lección 4.1.

---

## Prerrequisitos

### Conocimientos previos
- Haber completado la **Práctica 3** (configuración básica de router y switch en Packet Tracer) o revisar sus resultados antes de comenzar.
- Comprensión de los principios de ruteo IP: tabla de ruteo, *next hop*, ruta predeterminada (Lección 4.1).
- Conocimiento teórico de NAT, PAT y servicios DHCP/DNS (Lecciones 4.2 y 4.3 del módulo).
- Familiaridad básica con la CLI de Cisco IOS: modos de configuración (`enable`, `configure terminal`), sintaxis de comandos.

### Acceso requerido
- Cisco Packet Tracer 8.2 o superior instalado y con licencia activa (requiere cuenta gratuita en **netacad.com**).
- Archivo de topología `Lab04_topologia_base.pkt` proporcionado por el instructor. [Lab04_topologia_base.zip](https://github.com/user-attachments/files/30796908/Lab04_topologia_base.zip)

- Editor de texto para las respuestas analíticas (Notepad++, VS Code, Word o LibreOffice).

---

## Entorno de Laboratorio

### Hardware y software

| Componente              | Especificación mínima                         |
|-------------------------|-----------------------------------------------|
| Procesador              | 2 GHz dual-core                               |
| RAM                     | 4 GB (8 GB recomendados)                      |
| Espacio en disco        | 500 MB libres para archivos `.pkt`            |
| Sistema operativo       | Windows 10/11, macOS 12+, Ubuntu 20.04+       |
| Cisco Packet Tracer     | Versión 8.2 o superior                        |
| Editor de texto         | Cualquier editor moderno                      |
| Navegador web           | Chrome 110+, Firefox 110+ (para referencias)  |

### Topología de referencia

La topología que utilizarás tiene la siguiente estructura lógica:


<img width="776" height="644" alt="1 Topologia Inicial" src="https://github.com/user-attachments/assets/fb988de1-0dc8-43f4-ba2f-da3d95ab5e51" />


| Dispositivo  | Interfaz | Dirección IP         | Rol                        |
|--------------|----------|----------------------|----------------------------|
| Router0      | Gi0/0    | 192.168.10.1/24      | Gateway LAN (inside NAT)   |
| Router0      | Gi0/1    | 203.0.113.1/30       | Enlace WAN (outside NAT)   |
| Servidor0    | NIC      | 192.168.10.100/24    | Servidor web interno       |
| PC0          | NIC      | Por DHCP             | Host cliente               |
| PC1          | NIC      | Por DHCP             | Host cliente               |
| ISP_Router   | Gi0/0    | 203.0.113.2/30       | Simulación de ISP          |
| Servidor_Web | NIC      | 8.8.8.8/8            | Servidor externo simulado  |



### Preparación del entorno

1. Abre Cisco Packet Tracer.
2. Carga el archivo `Lab03_completado.pkt` (tu trabajo de la Práctica 3) o el archivo `Lab04_topologia_base.pkt` proporcionado.
3. Verifica que la topología muestra los dispositivos indicados en la tabla anterior.
4. Guarda inmediatamente una copia de respaldo:
   - **File → Save As → `Lab04_[TuNombre]_inicio.pkt`**
5. Abre un editor de texto y crea el archivo `Lab04_respuestas_[TuNombre].txt` donde registrarás tus observaciones y análisis.

---

## Procedimiento Paso a Paso

---

### Paso 1 — Verificación del Estado Inicial del Router

**Objetivo:** Confirmar que el router tiene conectividad básica entre sus interfaces antes de agregar DHCP y NAT, aplicando los conceptos de tabla de ruteo de la Lección 4.1.

#### Instrucciones

1. Haz clic en **Router0** y selecciona la pestaña **CLI**.
2. Si aparece el mensaje de configuración inicial (`Would you like to enter the initial configuration dialog?`), escribe `no` y presiona **Enter**.
3. Ingresa al modo privilegiado:
   ```
   R1> enable
   ```
4. Verifica la tabla de ruteo actual:
   ```
   R1# show ip route
   ```
5. Verifica el estado de las interfaces:
   ```
   R1# show ip interface brief
   ```
6. Prueba conectividad hacia el ISP:
   ```
   R1# ping 203.0.113.2
   ```

#### Salida esperada — `show ip route`

```
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is 0.0.0.0 to network 0.0.0.0

     192.168.10.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.10.0/24 is directly connected, GigabitEthernet0/0
L       192.168.10.1/32 is directly connected, GigabitEthernet0/0
     203.0.113.0/24 is variably subnetted, 2 subnets, 2 masks
C       203.0.113.0/30 is directly connected, GigabitEthernet0/1
L       203.0.113.1/32 is directly connected, GigabitEthernet0/1
S*   0.0.0.0/0 is directly connected, GigabitEthernet0/1
```

> Observa los códigos `C` (connected): estas son rutas directamente conectadas, el equivalente a las entradas automáticas que el router genera al tener interfaces activas. Recuerda de la Lección 4.1 que el router aplica *coincidencia más larga* al consultar esta tabla.

#### Salida esperada — `show ip interface brief`

```
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0     192.168.10.1    YES manual up                    up 
GigabitEthernet0/1     203.0.113.1     YES manual up                    up
```

#### Verificación

- [ ] Ambas interfaces aparecen con estado `up/up`.
- [ ] La tabla de ruteo muestra exactamente dos entradas `C`.
- [ ] El ping hacia `203.0.113.2` responde con éxito (5 signos de exclamación `!!!!!`).

> **Si alguna interfaz aparece `down/down`:** Haz clic en el cable de conexión en la topología y verifica que esté correctamente conectado. Revisa también que la interfaz esté habilitada con `no shutdown`.

---

### Paso 2 — Configuración del Servicio DHCP

**Objetivo:** Configurar un pool DHCP en el router para que los hosts de la LAN (PC0 y PC1) reciban automáticamente todos los parámetros de red necesarios.

#### Instrucciones

1. Desde el modo privilegiado, entra al modo de configuración global:
   ```
   R1# configure terminal
   ```

2. **Excluye las primeras 10 direcciones** del rango para uso de infraestructura (gateway, servidores, impresoras, etc.):
   ```
   R1(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.10
   ```

3. **Excluye también la IP del servidor interno** (que tiene dirección estática):
   ```
   R1(config)# ip dhcp excluded-address 192.168.10.100
   ```

4. **Crea el pool DHCP** con el nombre `LAN_POOL`:
   ```
   R1(config)# ip dhcp pool LAN_POOL
   ```

5. Define la **red** que el pool administrará:
   ```
   R1(dhcp-config)# network 192.168.10.0 255.255.255.0
   ```

6. Define el **default-gateway** que recibirán los clientes:
   ```
   R1(dhcp-config)# default-router 192.168.10.1
   ```

7. Define el **servidor DNS** que recibirán los clientes:
   ```
   R1(dhcp-config)# dns-server 8.8.8.8
   ```

8. (Opcional pero recomendado) Define el **tiempo de concesión** en días/horas/minutos:
   ```
   R1(dhcp-config)# lease 0 1 0
   ```
   > Esto configura una concesión de 0 días, 1 hora, 0 minutos. En entornos de producción se usan valores más largos (ej. 1 día).

9. Sal del modo de configuración DHCP:
   ```
   R1(dhcp-config)# exit
   R1(config)# exit
   ```

10. **Guarda la configuración** en memoria no volátil:
    ```
    R1# write memory
    ```
    o equivalentemente:
    ```
    R1# copy running-config startup-config
    ```

#### Verificación inmediata del pool

```
R1# show ip dhcp pool
```

**Salida esperada:**

```
Pool LAN_POOL :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0
 Total addresses                : 254
 Leased addresses               : 0
 Pending event                  : none
 1 subnet is currently in the subnet table
 192.168.10.0/24
 Range of addresses to hand out : 192.168.10.11 to 192.168.10.254
 (excluding 192.168.10.100)
```

#### Activación de DHCP en los hosts clientes

11. Haz clic en **PC0** → pestaña **Desktop** → **IP Configuration**.
12. Selecciona **DHCP** (en lugar de Static).
13. Espera 2–3 segundos. PC0 debería recibir automáticamente una IP en el rango `192.168.10.11–192.168.10.99` o `192.168.10.101–192.168.10.254`.
14. Repite los pasos 11–13 para **PC1**.
15. Anota las IPs asignadas en tu archivo de respuestas.

#### Verificación en el router

```
R1# show ip dhcp binding
```

**Salida esperada:**

```
IP address       Client-ID/              Lease expiration        Type
                 Hardware address
192.168.10.11    0060.2F1A.3B4C          --                      Automatic
192.168.10.12    0060.4D2E.5F6A          --                      Automatic
```

> Las direcciones MAC serán diferentes en tu simulación. Lo importante es que aparezcan **dos entradas** correspondientes a PC0 y PC1.

#### Verificación

- [ ] PC0 y PC1 muestran una IP en el rango `192.168.10.11–192.168.10.254` (excluyendo `.100`).
- [ ] Ambos hosts muestran Gateway: `192.168.10.1` y DNS: `8.8.8.8`.
- [ ] `show ip dhcp binding` lista dos entradas activas.
- [ ] La dirección `192.168.10.100` **no** aparece en el binding (está excluida).

---

### Paso 3 — Configuración de NAT Estático para el Servidor Interno

**Objetivo:** Configurar una traducción NAT estática de uno a uno para que el Servidor0 (`192.168.10.100`) sea accesible desde el exterior con la IP pública `203.0.113.5`.

> **Concepto clave:** El NAT estático crea un mapeo permanente entre una IP privada y una IP pública. Esto es fundamental cuando un servidor interno debe ser accesible desde Internet con una dirección predecible, como un servidor web o FTP.

#### Instrucciones

1. Entra al modo de configuración global:
   ```
   R1# configure terminal
   ```

2. **Define las interfaces inside y outside** de NAT. Esta distinción es esencial: el router necesita saber qué lado es la red privada y qué lado es Internet.

   Interfaz LAN (lado privado):
   ```
   R1(config)# interface GigabitEthernet0/0
   R1(config-if)# ip nat inside
   R1(config-if)# exit
   ```

   Interfaz WAN (lado público):
   ```
   R1(config)# interface GigabitEthernet0/1
   R1(config-if)# ip nat outside
   R1(config-if)# exit
   ```

3. **Crea la traducción NAT estática** para el servidor:
   ```
   R1(config)# ip nat inside source static 192.168.10.100 203.0.113.5
   ```

   > Esto le dice al router: "Cualquier paquete que llegue por Gi0/1 con destino `203.0.113.5` debe ser redirigido a `192.168.10.100`, y viceversa."

4. Sal del modo de configuración:
   ```
   R1(config)# exit
   ```

#### Verificación de NAT estático

```
R1# show ip nat translations
```

**Salida esperada (antes de cualquier tráfico):**

```
Pro Inside global      Inside local       Outside local      Outside global
--- 203.0.113.5        192.168.10.100     ---                ---
```

> La entrada de NAT estático aparece inmediatamente, incluso sin tráfico, porque es una traducción permanente.

5. **Prueba desde el servidor externo simulado:** Haz clic en **Servidor_Web** (el dispositivo externo en tu topología) → **Desktop** → **Command Prompt** y ejecuta:
   ```
   ping 203.0.113.5
   ```

   **Salida esperada:**
   ```
   Pinging 203.0.113.5 with 32 bytes of data:
   Reply from 203.0.113.5: bytes=32 time=1ms TTL=127
   Reply from 203.0.113.5: bytes=32 time=1ms TTL=127
   Reply from 203.0.113.5: bytes=32 time=1ms TTL=127
   Reply from 203.0.113.5: bytes=32 time=1ms TTL=127
   ```

6. Después del ping, vuelve al router y ejecuta nuevamente:
   ```
   R1# show ip nat translations
   ```

   **Salida esperada (con tráfico activo):**
   ```
   Pro Inside global      Inside local       Outside local      Outside global
   --- 203.0.113.5        192.168.10.100     ---                ---
   icmp 203.0.113.5:512   192.168.10.100:512 8.8.8.8:512        8.8.8.8:512
   ```

   > Ahora aparece la traducción dinámica del tráfico ICMP. El router está traduciendo `203.0.113.5` ↔ `192.168.10.100`.

#### Verificación

- [ ] `show ip nat translations` muestra la entrada estática para `192.168.10.100 ↔ 203.0.113.5`.
- [ ] El ping desde el servidor externo hacia `203.0.113.5` tiene respuesta exitosa.
- [ ] Fa0/0 tiene configurado `ip nat inside` y Fa0/1 tiene `ip nat outside`.

---

### Paso 4 — Configuración de PAT (NAT con Sobrecarga)

**Objetivo:** Implementar PAT para que PC0 y PC1 (y cualquier otro host de la LAN) compartan la IP pública `203.0.113.1` (la IP de Gi0/1) para acceder a Internet, usando números de puerto para distinguir las sesiones.

> **Concepto clave:** PAT (Port Address Translation), también llamado NAT con sobrecarga (*overload*), es la tecnología que permite que cientos de dispositivos compartan una sola IP pública. El router mantiene una tabla que asocia cada conexión interna con un puerto único en la IP pública. Esto es fundamental para entender por qué IPv4 no se agotó tan rápido como se temía en los años 90.

#### Instrucciones

1. **Crea una lista de acceso (ACL)** que identifique el tráfico de la red interna que debe ser traducido:
   ```
   R1# configure terminal
   R1(config)# access-list 1 permit 192.168.10.0 0.0.0.255
   ```

   > La ACL número `1` es una lista de acceso estándar. El wildcard `0.0.0.255` es el inverso de la máscara `255.255.255.0` y significa "cualquier host en la red `192.168.10.0/24`".

2. **Configura PAT usando la interfaz de salida** como IP pública (de esta forma, si la IP pública cambia, la configuración sigue siendo válida):
   ```
   R1(config)# ip nat inside source list 1 interface GigabitEthernet0/1  overload
   ```

   > La palabra clave `overload` es lo que activa PAT. Sin ella, sería NAT dinámico normal (un mapeo uno a uno por cada IP disponible en el pool).

3. Sal y guarda:
   ```
   R1(config)# exit
   R1# write memory
   ```

#### Generación de tráfico para verificar PAT

4. Haz clic en **PC0** → **Desktop** → **Web Browser**.
5. En la barra de direcciones, escribe `http://8.8.8.8` y presiona Enter.
6. Repite desde **PC1** → **Desktop** → **Web Browser** → `http://8.8.8.8`.

7. Vuelve al router y ejecuta:
   ```
   R1# show ip nat translations
   ```

**Salida esperada con PAT activo:**

```
Pro Inside global        Inside local         Outside local      Outside global
--- 203.0.113.5          192.168.10.100       ---                ---
tcp 203.0.113.1:1025     192.168.10.11:1025   8.8.8.8:80         8.8.8.8:80
tcp 203.0.113.1:1026     192.168.10.12:1025   8.8.8.8:80         8.8.8.8:80
```

> **Observación crítica:** Nota que tanto PC0 (`192.168.10.11`) como PC1 (`192.168.10.12`) aparecen con la **misma IP pública** (`203.0.113.1`) pero con **puertos de origen diferentes** (`:1025` y `:1026`). Así es exactamente como PAT distingue las sesiones de múltiples hosts.

8. Para ver también las estadísticas de NAT:
   ```
   R1# show ip nat statistics
   ```

   **Salida esperada:**
   ```
   Total active translations: 3 (1 static, 2 dynamic; 2 extended)
   Outside interfaces:
     GigabitEthernet0/1
   Inside interfaces:
     GigabitEthernet0/0 
   Hits: 24  Misses: 0
   ...
   ```

#### Verificación

- [ ] `show ip nat translations` muestra las traducciones PAT con la misma IP global pero puertos distintos para PC0 y PC1.
- [ ] PC0 y PC1 pueden acceder al servidor web externo (`8.8.8.8`) desde el navegador de Packet Tracer.
- [ ] La entrada estática del Servidor0 sigue presente en la tabla NAT.
- [ ] `show ip nat statistics` confirma traducciones activas tanto estáticas como dinámicas.

---

### Paso 5 — Pruebas de Conectividad Integral

**Objetivo:** Verificar que todos los servicios configurados funcionan correctamente de manera simultánea y que el flujo de tráfico es el esperado.

#### Instrucciones

**Prueba 1: Conectividad interna (LAN)**

1. Desde **PC0** → **Desktop** → **Command Prompt**:
   ```
   ping 192.168.10.1
   ping 192.168.10.100
   ping 192.168.10.12
   ```
   Todos deben responder con éxito (`Reply from...`).

**Prueba 2: Conectividad hacia Internet desde hosts DHCP**

2. Desde **PC0** → **Desktop** → **Command Prompt**:
   ```
   ping 8.8.8.8
   ```
   Debe responder con éxito (el tráfico pasa por PAT).

3. Desde **PC1** → **Desktop** → **Command Prompt**:
   ```
   ping 8.8.8.8
   ```
   Debe responder con éxito.

**Prueba 3: Accesibilidad del servidor desde el exterior**

4. Desde **Servidor_Web** (externo) → **Desktop** → **Command Prompt**:
   ```
   ping 203.0.113.5
   ```
   Debe responder (NAT estático redirige hacia `192.168.10.100`).

5. Desde **Servidor_Web** → **Desktop** → **Web Browser** → `http://203.0.113.5`
   Debe cargar la página del Servidor0 interno.

**Prueba 4: Inspección final de la tabla NAT**

6. En el router:
   ```
   Router# show ip nat translations verbose
   ```

   Registra la salida completa en tu archivo de respuestas. Identifica y etiqueta:
   - La entrada de NAT estático
   - Las entradas de PAT para PC0 y PC1
   - El campo "Inside global", "Inside local", "Outside global"

#### Tabla de resultados esperados

| Prueba                              | Origen             | Destino          | Resultado esperado |
|-------------------------------------|--------------------|------------------|--------------------|
| Ping LAN → Gateway                  | PC0                | 192.168.10.1     | ✅ Éxito           |
| Ping LAN → Servidor interno         | PC0                | 192.168.10.100   | ✅ Éxito           |
| Ping LAN → Internet                 | PC0                | 8.8.8.8          | ✅ Éxito (PAT)     |
| Ping LAN → Internet                 | PC1                | 8.8.8.8          | ✅ Éxito (PAT)     |
| Ping Externo → IP pública servidor  | Servidor_Web       | 203.0.113.5      | ✅ Éxito (NAT est.)|
| Web Externo → Servidor interno      | Servidor_Web       | http://203.0.113.5 | ✅ Página carga  |

#### Verificación

- [ ] Todas las pruebas de ping producen respuesta exitosa.
- [ ] El navegador externo carga la página del servidor interno.
- [ ] La tabla NAT muestra simultáneamente entradas estáticas y dinámicas (PAT).

---

### Paso 6 — Análisis Escrito y Reflexión

**Objetivo:** Consolidar la comprensión conceptual relacionando la configuración realizada con los principios de ruteo (Lección 4.1) y el contexto del agotamiento de IPv4.

#### Instrucciones

En tu archivo de respuestas `Lab04_respuestas_[TuNombre].txt`, responde las siguientes preguntas con un párrafo cada una (mínimo 3 oraciones):

**Pregunta 1 — Relación con la tabla de ruteo:**
> Cuando PC0 envía un paquete hacia `8.8.8.8`, ¿cómo usa el router su tabla de ruteo para decidir por dónde enviarlo? ¿Qué entrada de la tabla es la que aplica? Relaciona tu respuesta con el concepto de *coincidencia más larga* y *gateway de último recurso* estudiados en la Lección 4.1.

**Pregunta 2 — Diferencia entre NAT estático y PAT:**
> Explica con tus propias palabras la diferencia fundamental entre el NAT estático configurado para el Servidor0 y el PAT configurado para PC0 y PC1. ¿Por qué no se puede usar PAT para el servidor si queremos que sea accesible desde Internet?

**Pregunta 3 — NAT y el agotamiento de IPv4:**
> ¿Por qué NAT/PAT es considerado una solución de "parche" al agotamiento de IPv4 y no una solución definitiva? ¿Qué limitaciones introduce NAT en la conectividad extremo a extremo? ¿Cómo resuelve IPv6 este problema?

**Pregunta 4 — Observación de la tabla NAT:**
> Copia la salida del comando `show ip nat translations verbose` de tu router y explica cada columna: *Inside local*, *Inside global*, *Outside local*, *Outside global*. ¿Qué representa el número de puerto en las entradas PAT?

---

## Validación y Pruebas Finales

Una vez completados todos los pasos, ejecuta la siguiente secuencia de verificación completa en el router:

```
R1# show ip dhcp binding
R1# show ip dhcp pool
R1# show ip nat translations
R1# show ip nat statistics
R1# show ip interface brief
R1# show running-config | section dhcp
R1# show running-config | section nat
```

### Lista de verificación final

| Criterio                                                                 | Estado |
|--------------------------------------------------------------------------|--------|
| PC0 tiene IP asignada por DHCP en rango `192.168.10.11–192.168.10.254`  | ☐      |
| PC1 tiene IP asignada por DHCP en rango `192.168.10.11–192.168.10.254`  | ☐      |
| PC0 y PC1 tienen Gateway `192.168.10.1` y DNS `8.8.8.8`                 | ☐      |
| `show ip dhcp binding` muestra 2 entradas activas                        | ☐      |
| La IP `192.168.10.100` NO aparece en el binding DHCP                     | ☐      |
| `show ip nat translations` muestra entrada estática `203.0.113.5 ↔ 192.168.10.100` | ☐ |
| `show ip nat translations` muestra entradas PAT para PC0 y PC1           | ☐      |
| PC0 puede hacer ping a `8.8.8.8`                                         | ☐      |
| Servidor externo puede hacer ping a `203.0.113.5`                        | ☐      |
| Fa0/0 tiene `ip nat inside` y Fa0/1 tiene `ip nat outside`               | ☐      |
| Respuestas escritas completadas en archivo de texto                       | ☐      |

### Configuración de referencia completa

Para comparar con tu configuración, la sección relevante del `running-config` debe verse así:

```
!
ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 192.168.10.100
!
ip dhcp pool LAN_POOL
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8
 lease 0 1 0
!
...
!
interface GigabitEthernet0/0 
 ip address 192.168.10.1 255.255.255.0
 ip nat inside
 duplex auto
 speed auto
!
interface GigabitEthernet0/1 
 ip address 203.0.113.1 255.255.255.252
 ip nat outside
 duplex auto
 speed auto
!
ip nat inside source static 192.168.10.100 203.0.113.5
ip nat inside source list 1 interface GigabitEthernet0/1  overload
!
access-list 1 permit 192.168.10.0 0.0.0.255
!
```

---

## Solución de Problemas
Descarga el archivo .pkt aqui [Lab-4  Solucion de problemas.zip](https://github.com/user-attachments/files/30797315/Lab-4.Solucion.de.problemas.zip)

### Problema 1 — Los hosts no reciben dirección IP por DHCP

**Síntoma:**
PC0 o PC1 muestran la dirección `0.0.0.0` o una IP APIPA (`169.254.x.x`) después de seleccionar DHCP. El campo Gateway también aparece vacío.

**Causa probable:**
El pool DHCP excluye incorrectamente todo el rango de la red, o la interfaz `Gi0/0` del router no tiene configurado `ip nat inside` (aunque esto no afecta DHCP directamente, indica que la interfaz puede estar mal configurada). La causa más común en Packet Tracer es que el pool DHCP tiene un error tipográfico en el nombre de red o la máscara.

**Diagnóstico y solución:**

1. Verifica la configuración del pool:
   ```
   R1# show ip dhcp pool
   R1# show running-config | section dhcp
   ```
2. Confirma que el rango de exclusión no cubre toda la red. Si excluiste `192.168.10.1 192.168.10.254`, no quedan IPs disponibles.
3. Si hay un error, elimina el pool y recréalo:
   ```
   R1# configure terminal
   R1(config)# no ip dhcp pool LAN_POOL
   R1(config)# no ip dhcp excluded-address 192.168.10.1 192.168.10.10
   R1(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.10
   R1(config)# ip dhcp pool LAN_POOL
   R1(dhcp-config)# network 192.168.10.0 255.255.255.0
   R1(dhcp-config)# default-router 192.168.10.1
   R1(dhcp-config)# dns-server 8.8.8.8
   R1(dhcp-config)# exit
   ```
4. En PC0, cambia a **Static** y vuelve a **DHCP** para forzar una nueva solicitud DHCP.
5. Verifica con `show ip dhcp binding` que aparezcan las asignaciones.

---

### Problema 2 — PAT configurado pero los hosts no pueden acceder a Internet (ping a 8.8.8.8 falla)

**Síntoma:**
`show ip nat translations` muestra la entrada estática del servidor, pero no aparecen entradas dinámicas para PC0 o PC1. El ping desde los hosts hacia `8.8.8.8` devuelve `Request timed out` o `Destination host unreachable`.

**Causa probable:**
Existen tres causas frecuentes: (a) la ACL número 1 no coincide con el tráfico de la red interna (error en la dirección de red o wildcard), (b) falta la ruta predeterminada en el router hacia Internet, o (c) las interfaces no tienen correctamente asignados los roles `ip nat inside` / `ip nat outside`.

**Diagnóstico y solución:**

1. Verifica que la ACL existe y es correcta:
   ```
   R1# show access-lists
   ```
   Debe mostrar:
   ```
   Standard IP access list 1
       10 permit 192.168.10.0 0.0.0.255
   ```

2. Verifica que existe una ruta predeterminada (necesaria para que el router sepa cómo llegar a `8.8.8.8`):
   ```
   R1# show ip route
   ```
   Debe existir una línea con `S* 0.0.0.0/0`. Si no existe, agrégala:
   ```
   R1(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.2
   ```
   > Recuerda de la Lección 4.1: la ruta `0.0.0.0/0` es el *gateway de último recurso*. Sin ella, el router no sabe cómo llegar a destinos fuera de sus redes directamente conectadas.

3. Verifica los roles NAT en las interfaces:
   ```
   R1r# show ip interface GigabitEthernet0/0 | include NAT
   R1# show ip interface GigabitEthernet0/1  | include NAT
   ```
   Gi0/0 debe decir `Inbound access list is not set` y estar marcada como `inside`; Gi0/1 como `outside`.

4. Si el comando PAT no está presente, agrégalo:
   ```
   R1(config)# ip nat inside source list 1 interface GigabitEthernet0/1  overload
   ```

5. Genera tráfico nuevamente desde PC0 y verifica:
   ```
   R1# show ip nat translations
   ```

---

## Limpieza del Entorno

Al finalizar la práctica, realiza los siguientes pasos para organizar y entregar tu trabajo correctamente:

1. **Guarda el archivo final de Packet Tracer:**
   ```
   File → Save As → Lab04_[TuNombre]_completado.pkt
   ```

2. **Verifica que el archivo de respuestas esté completo** con las cuatro preguntas analíticas respondidas y la salida del comando `show ip nat translations verbose` incluida.

3. **Si necesitas limpiar la configuración NAT** para reutilizar la topología en otra práctica, puedes eliminar las traducciones dinámicas sin borrar la configuración:
   ```
   Router# clear ip nat translation *
   ```
   > **Advertencia:** Este comando elimina todas las traducciones dinámicas de la tabla, pero **no** elimina las traducciones estáticas ni la configuración del pool DHCP. Para eliminar la configuración completa de NAT habría que usar `no ip nat inside source...` en modo de configuración global.

4. **Archivos a entregar al instructor:**
   - `Lab04_[TuNombre]_completado.pkt`
   - `Lab04_respuestas_[TuNombre].txt`

5. **No es necesario** desinstalar ningún software ni modificar la configuración de red de tu equipo real, ya que toda la práctica se realizó dentro del entorno simulado de Packet Tracer.

---

## Resumen

En esta práctica aplicaste tres servicios fundamentales de infraestructura de red en un router Cisco simulado:

1. **DHCP:** Configuraste un pool que asigna automáticamente IP, máscara, gateway y DNS a los hosts de la LAN, excluyendo rangos reservados. Verificaste las asignaciones con `show ip dhcp binding`.

2. **NAT estático:** Creaste un mapeo permanente entre la IP privada del servidor interno (`192.168.10.100`) y una IP pública fija (`203.0.113.5`), permitiendo acceso desde Internet de manera predecible.

3. **PAT (NAT con sobrecarga):** Implementaste la traducción de múltiples hosts internos hacia una única IP pública usando números de puerto para distinguir sesiones, reflejando el mecanismo que ha permitido que IPv4 siga siendo viable décadas después de que se predijo su agotamiento.

Estos conceptos se conectan directamente con los **principios de ruteo de la Lección 4.1**: el router consulta su tabla de ruteo para determinar el *next hop* de cada paquete, y NAT/PAT actúa como una capa adicional que modifica las cabeceras IP antes de que el paquete sea reenviado. La ruta predeterminada (`0.0.0.0/0`) es el componente de ruteo que hace posible que el tráfico PAT llegue a Internet.

### Comandos clave utilizados

| Comando                                              | Función                                              |
|------------------------------------------------------|------------------------------------------------------|
| `ip dhcp excluded-address`                           | Excluye IPs del pool DHCP                            |
| `ip dhcp pool [nombre]`                              | Crea y configura un pool DHCP                        |
| `show ip dhcp binding`                               | Muestra asignaciones DHCP activas                    |
| `ip nat inside` / `ip nat outside`                   | Define roles de interfaz para NAT                    |
| `ip nat inside source static [privada] [pública]`    | Configura NAT estático                               |
| `access-list 1 permit [red] [wildcard]`              | Define tráfico a traducir con PAT                    |
| `ip nat inside source list 1 interface [if] overload`| Configura PAT usando la IP de la interfaz            |
| `show ip nat translations`                           | Muestra la tabla de traducciones NAT activas         |
| `show ip nat statistics`                             | Muestra estadísticas de NAT                          |
| `clear ip nat translation *`                         | Limpia traducciones dinámicas de la tabla NAT        |

### Recursos adicionales

- [Cisco IOS IP Addressing Services Configuration Guide — NAT](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipaddr_nat/configuration/xe-16/nat-xe-16-book.html)
- [Cisco IOS IP Addressing Services — DHCP](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipaddr_dhcp/configuration/xe-16/dhcp-xe-16-book.html)
- [RFC 3022 — Traditional IP Network Address Translator (NAT)](https://www.rfc-editor.org/rfc/rfc3022)
- [RFC 2131 — Dynamic Host Configuration Protocol (DHCP)](https://www.rfc-editor.org/rfc/rfc2131)
- [Packet Tracer — Cisco Skills for All (Netacad)](https://skillsforall.com/course/getting-started-cisco-packet-tracer)

---
*Lab 04-00-01 — Versión 1.0 | Módulo 4: Configuración y Administración de Componentes de Red*
