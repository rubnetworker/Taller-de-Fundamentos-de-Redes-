---LAB_START---
LAB_ID: 01-00-01
---MARKDOWN---
# Práctica 1 — Análisis de Encapsulado y Desencapsulado de Paquetes

## 1. Metadatos

| Campo            | Detalle                                      |
|------------------|----------------------------------------------|
| **Duración**     | 26 minutos                                   |
| **Complejidad**  | Fácil                                        |
| **Nivel Bloom**  | Aplicar (Apply)                              |
| **Módulo**       | 1 — Fundamentos de Redes                     |
| **Práctica**     | 01 de 07                                     |

---

## 2. Descripción General

En esta práctica observarás en tiempo real cómo los datos de una aplicación son envueltos capa por capa con encabezados (headers) a medida que descienden por la pila de protocolos —proceso conocido como **encapsulado**— y cómo esos encabezados son retirados en sentido inverso al llegar al destino —**desencapsulado**—. Utilizarás Wireshark para capturar tráfico generado por tu propio equipo (ping y navegación web HTTP), identificarás los campos de cada PDU (*Protocol Data Unit*) y los mapearás con las capas del modelo OSI y del modelo TCP/IP estudiados en los temas 1.1, 1.2 y 1.3. Al finalizar documentarás tus hallazgos en una tabla de correspondencia que servirá como referencia para el resto del curso.

> **⚠️ Aviso ético:** Wireshark solo debe usarse para capturar tráfico en tu propia red o en la interfaz de loopback. Capturar tráfico en redes ajenas sin autorización es ilegal y antiético.

---

## 3. Objetivos de Aprendizaje

Al completar esta práctica serás capaz de:

- [ ] Identificar las capas del modelo OSI y su equivalencia en el modelo TCP/IP observando tráfico real capturado con Wireshark.
- [ ] Analizar el proceso de encapsulado reconociendo los encabezados añadidos en las capas Ethernet (capa 2), IP (capa 3) y TCP/UDP (capa 4).
- [ ] Describir el proceso de desencapsulado relacionando cada campo del encabezado con la capa OSI que lo procesa.
- [ ] Comparar la estructura de un paquete TCP/IP capturado con las siete capas del modelo OSI, estableciendo correspondencias precisas.

---

## 4. Prerrequisitos

### Conocimientos previos
- Comprensión teórica de los modelos OSI (7 capas) y TCP/IP (4 capas) — temas 1.1, 1.2 y 1.3.
- Familiaridad con la terminología: PDU, trama, paquete, segmento, datagrama, encabezado.
- Conocimiento básico de la línea de comandos de tu sistema operativo (ejecutar `ping`).

### Acceso y software requerido
- Wireshark 4.0 o superior instalado y funcional ([wireshark.org/download.html](https://www.wireshark.org/download.html)).
- Conexión a internet activa (para generar tráfico HTTP/HTTPS).
- Permisos de administrador o root en el equipo (necesarios para capturar paquetes con Wireshark).
- Terminal del sistema operativo disponible (CMD/PowerShell en Windows; Terminal en macOS/Linux).

---

## 5. Entorno de Laboratorio

### Hardware

| Componente       | Mínimo recomendado                              |
|------------------|-------------------------------------------------|
| Procesador       | 2 GHz dual-core                                 |
| RAM              | 4 GB (8 GB recomendado)                         |
| Almacenamiento   | 500 MB libres para archivos de captura (.pcapng)|
| Interfaz de red  | NIC Ethernet o Wi-Fi funcional                  |

### Software

| Software         | Versión mínima | Propósito en esta práctica                 |
|------------------|----------------|--------------------------------------------|
| Wireshark        | 4.0            | Captura y análisis de paquetes             |
| CMD / PowerShell | Windows 10+    | Ejecutar `ping` en Windows                 |
| Terminal (Bash)  | macOS / Linux  | Ejecutar `ping` en macOS/Linux             |
| Navegador web    | Chrome/Firefox 110+ | Generar tráfico HTTP                  |

### Verificación previa del entorno

Antes de comenzar, confirma que Wireshark está correctamente instalado ejecutando el siguiente comando en tu terminal:

**Windows (CMD o PowerShell):**
```cmd
"C:\Program Files\Wireshark\Wireshark.exe" --version
```

** Linux (Terminal):**
```
sudo apt update && sudo apt install wireshark
```

Si el comando no es reconocido, verifica que Wireshark esté instalado y que su directorio esté en el PATH del sistema.

---

## 6. Procedimiento Paso a Paso

---

### Paso 1 — Identificar las interfaces de red disponibles en Wireshark

**Objetivo:** Seleccionar la interfaz de red correcta antes de iniciar la captura, para asegurarte de que el tráfico capturado corresponde a tu conexión activa.

#### Instrucciones

1. Abre Wireshark haciendo doble clic en su ícono o buscándolo en el menú de aplicaciones.
2. En la pantalla de inicio, observa la lista de interfaces de red disponibles. Cada interfaz mostrará una línea de actividad (gráfica de ondas) si hay tráfico pasando por ella.
3. Identifica tu interfaz activa:
   - Si estás conectado por cable Ethernet, busca una entrada llamada **Ethernet**, **eth0**, **en0** o similar.
   - Si estás conectado por Wi-Fi, busca **Wi-Fi**, **wlan0**, **en1** o similar.
   - Observa cuál interfaz tiene actividad visible en la gráfica de ondas.
4. **No inicies la captura todavía.** Solo identifica la interfaz.

> **💡 Nota:** Si no ves actividad en ninguna interfaz, abre un navegador web y carga cualquier página; el tráfico debería aparecer en la interfaz de red activa.

#### Salida esperada

Deberías ver una lista similar a esta (los nombres varían por sistema operativo):

<img width="1542" height="599" alt="Lab1-img 1" src="https://github.com/user-attachments/assets/f8463ae3-f079-42b7-ba5c-72cc59c93cd9" />


#### Verificación

✅ Puedes nombrar la interfaz de red activa en tu equipo.
✅ Distingues entre una interfaz con tráfico y una sin tráfico.

---

### Paso 2 — Capturar tráfico ICMP generado por el comando `ping`

**Objetivo:** Generar tráfico ICMP controlado y capturarlo en Wireshark para observar la estructura de un paquete simple que involucra las capas 2, 3 y 4 del modelo OSI.

#### Instrucciones

1. En Wireshark, haz doble clic sobre la interfaz de red activa identificada en el Paso 1. La captura comenzará automáticamente.

2. Para filtrar únicamente tráfico ICMP y reducir el ruido, escribe en la barra de filtro de Wireshark (parte superior, campo verde/blanco):
   ```
   icmp
   ```
   Presiona **Enter** para aplicar el filtro.

3. Abre una terminal en tu sistema operativo y ejecuta el siguiente comando:

   **Windows (CMD o PowerShell):**
   ```cmd
   ping 8.8.8.8 -n 4
   ```

   **Linux (Terminal):**
   ```bash
   ping -c 4 8.8.8.8
   ```

   > Este comando envía 4 paquetes ICMP Echo Request al servidor DNS público de Google (8.8.8.8).

4. Observa cómo aparecen paquetes en Wireshark mientras el comando `ping` se ejecuta.

5. Una vez que el `ping` termine, haz clic en el botón **Stop** (ícono de cuadrado rojo) en Wireshark para detener la captura.

6. **Guarda la captura:** Ve a `File > Save As`, nómbrala `captura_ping.pcapng` y guárdala en tu carpeta de trabajo.

#### Salida esperada en la terminal

**Windows:**
```
Haciendo ping a 8.8.8.8 con 32 bytes de datos:
Respuesta desde 8.8.8.8: bytes=32 tiempo=15ms TTL=118
Respuesta desde 8.8.8.8: bytes=32 tiempo=14ms TTL=118
Respuesta desde 8.8.8.8: bytes=32 tiempo=15ms TTL=118
Respuesta desde 8.8.8.8: bytes=32 tiempo=16ms TTL=118

Estadísticas de ping para 8.8.8.8:
    Paquetes: enviados = 4, recibidos = 4, perdidos = 0 (0% perdidos)
```

**Linux:**
```
PING 8.8.8.8 (8.8.8.8): 56 data bytes
64 bytes from 8.8.8.8: icmp_seq=0 ttl=118 time=14.5 ms
64 bytes from 8.8.8.8: icmp_seq=1 ttl=118 time=15.1 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=118 time=14.8 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=118 time=15.3 ms

--- 8.8.8.8 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss
```

#### Salida esperada en Wireshark

Deberías ver al menos 8 filas (4 Echo Request + 4 Echo Reply) con la columna **Protocol** mostrando `ICMP`:

```
No.  Time     Source          Destination     Protocol  Length  Info
1    0.000    192.168.1.x     8.8.8.8         ICMP      74      Echo (ping) request  id=0x0001, seq=1
2    0.014    8.8.8.8         192.168.1.x     ICMP      74      Echo (ping) reply    id=0x0001, seq=1
3    1.001    192.168.1.x     8.8.8.8         ICMP      74      Echo (ping) request  id=0x0001, seq=2
4    1.015    8.8.8.8         192.168.1.x     ICMP      74      Echo (ping) reply    id=0x0001, seq=2
...
```

#### Verificación

✅ Aparecen paquetes ICMP en Wireshark mientras se ejecuta el `ping`.
✅ Se observan tanto paquetes `Echo request` como `Echo reply`.
✅ La captura fue guardada como `captura_ping.pcapng`.

---

### Paso 3 — Analizar el encapsulado en un paquete ICMP (capas 2, 3 y 4)

**Objetivo:** Examinar la estructura interna de un paquete ICMP capturado, identificando los encabezados de cada capa y relacionándolos con el modelo OSI.

#### Instrucciones

1. En Wireshark, con la captura `captura_ping.pcapng` abierta y el filtro `icmp` activo, haz clic **una sola vez** sobre el primer paquete `Echo (ping) request` de la lista (fila No. 1).

2. Observa el panel central de Wireshark (panel de detalles del paquete). Verás una estructura expandible con varias secciones. Haz clic en cada sección para expandirla:

   ```
   ▼ Frame 1: 74 bytes on wire
   ▼ Ethernet II, Src: xx:xx:xx:xx:xx:xx, Dst: yy:yy:yy:yy:yy:yy
   ▼ Internet Protocol Version 4, Src: 192.168.1.x, Dst: 8.8.8.8
   ▼ Internet Control Message Protocol
   ```

3. **Analiza la sección Ethernet II** (Capa 2 — Enlace de datos):
   - Anota la dirección MAC de origen (`Source`).
   - Anota la dirección MAC de destino (`Destination`).
   - Anota el valor del campo `Type` (debería ser `0x0800` para IPv4).

4. **Analiza la sección Internet Protocol Version 4** (Capa 3 — Red):
   - Anota la dirección IP de origen (`Source Address`).
   - Anota la dirección IP de destino (`Destination Address`).
   - Anota el valor del campo `Time to Live (TTL)`.
   - Anota el valor del campo `Protocol` (debería ser `1` para ICMP).

5. **Analiza la sección Internet Control Message Protocol** (Capa 3 — Red / Protocolo de soporte):
   - Anota el `Type` (8 = Echo Request, 0 = Echo Reply).
   - Anota el `Code` (debería ser 0).
   - Anota el `Identifier` y el `Sequence Number`.

6. **Observa el panel inferior** (panel de bytes hexadecimales). Cuando hagas clic en un campo en el panel central, se resaltará en azul el byte correspondiente en el panel hexadecimal. Haz clic en:
   - La dirección MAC de destino → observa qué bytes se resaltan.
   - La dirección IP de origen → observa qué bytes se resaltan.

7. Completa la **Tabla de Encapsulado** al final de esta sección con los valores que observaste.

#### Tabla de Encapsulado — Para completar

| Capa OSI       | Capa TCP/IP     | PDU       | Protocolo | Campo observado en Wireshark       | Valor capturado |
|----------------|-----------------|-----------|-----------|-------------------------------------|-----------------|
| Capa 2 (Enlace)| Acceso a la red | Trama     | Ethernet  | MAC Destino                         |                 |
| Capa 2 (Enlace)| Acceso a la red | Trama     | Ethernet  | MAC Origen                          |                 |
| Capa 2 (Enlace)| Acceso a la red | Trama     | Ethernet  | Type                                |                 |
| Capa 3 (Red)   | Internet        | Paquete   | IPv4      | IP Origen                           |                 |
| Capa 3 (Red)   | Internet        | Paquete   | IPv4      | IP Destino                          |                 |
| Capa 3 (Red)   | Internet        | Paquete   | IPv4      | TTL                                 |                 |
| Capa 3 (Red)   | Internet        | Paquete   | IPv4      | Protocol (número)                   |                 |
| Capa 3 (Red)   | Internet        | Mensaje   | ICMP      | Type (8=Request / 0=Reply)          |                 |
| Capa 3 (Red)   | Internet        | Mensaje   | ICMP      | Sequence Number                     |                 |

#### Salida esperada (valores de referencia)

```
Ethernet II:
  Destination: aa:bb:cc:dd:ee:ff  (dirección MAC de tu router/gateway)
  Source:      11:22:33:44:55:66  (dirección MAC de tu NIC)
  Type:        IPv4 (0x0800)

Internet Protocol Version 4:
  Source Address:      192.168.1.x   (tu IP local)
  Destination Address: 8.8.8.8
  Time to Live:        128 (Windows) / 64 (Linux/macOS)
  Protocol:            ICMP (1)

Internet Control Message Protocol:
  Type:            8 (Echo Request)
  Code:            0
  Sequence Number: 1
```

#### Verificación

✅ La tabla de encapsulado está completamente llenada con valores reales de tu captura.
✅ Puedes identificar visualmente en Wireshark qué sección corresponde a qué capa OSI.
✅ Comprendes que el campo `Type: 0x0800` en Ethernet indica que el payload es un paquete IPv4.

---

### Paso 4 — Capturar tráfico HTTP para observar la capa de aplicación

**Objetivo:** Generar tráfico HTTP (no cifrado) para observar datos de la capa de aplicación y completar el mapeo de todas las capas OSI, incluyendo TCP (capa 4) y la capa de aplicación (capas 5-7).

> **💡 Nota importante:** La mayoría de los sitios modernos usan HTTPS (tráfico cifrado), lo que impide ver el contenido de la capa de aplicación en texto claro. Para esta práctica utilizaremos el sitio `http://httpforever.com` o `http://neverssl.com`, que mantienen HTTP sin cifrar por propósito educativo.

#### Instrucciones

1. En Wireshark, inicia una **nueva captura** haciendo clic en el ícono de aleta de tiburón verde (o `Capture > Start`). Si te pregunta si deseas guardar la captura anterior, selecciona **Continue without Saving** (ya la guardaste en el Paso 2).

2. Aplica el siguiente filtro de captura para ver únicamente tráfico HTTP y TCP relevante:
   ```
   tcp.port == 80
   ```

3. Abre tu navegador web y navega a:
   ```
   http://httpforever.com
   ```
   o, como alternativa:
   ```
   http://neverssl.com
   ```

4. Espera a que la página cargue completamente (3-5 segundos).

5. Detén la captura en Wireshark (`Capture > Stop` o clic en el cuadrado rojo).

6. **Guarda la captura** como `captura_http.pcapng`.

7. En la lista de paquetes, busca una fila donde la columna **Protocol** diga `HTTP` y la columna **Info** muestre algo como `GET / HTTP/1.1`. Haz clic sobre ese paquete.

#### Salida esperada en Wireshark

```
No.  Time    Source         Destination    Protocol  Length  Info
5    0.123   192.168.1.x    93.184.x.x     TCP       66      52341 → 80 [SYN]
6    0.145   93.184.x.x     192.168.1.x    TCP       66      80 → 52341 [SYN, ACK]
7    0.145   192.168.1.x    93.184.x.x     TCP       54      52341 → 80 [ACK]
8    0.146   192.168.1.x    93.184.x.x     HTTP      431     GET / HTTP/1.1
9    0.168   93.184.x.x     192.168.1.x    HTTP      892     HTTP/1.1 200 OK (text/html)
```

> Si no aparece tráfico HTTP, verifica que el sitio no haya redirigido a HTTPS. Prueba con `http://neverssl.com`.

#### Verificación

✅ Aparecen paquetes con protocolo `HTTP` en Wireshark.
✅ Se puede observar un paquete `GET / HTTP/1.1` (solicitud del navegador).
✅ Se puede observar un paquete `HTTP/1.1 200 OK` (respuesta del servidor).
✅ La captura fue guardada como `captura_http.pcapng`.

---

### Paso 5 — Analizar el encapsulado completo en un paquete HTTP (todas las capas)

**Objetivo:** Examinar un paquete HTTP completo para mapear todas las capas OSI desde la capa de aplicación hasta la capa de enlace, completando el análisis de encapsulado/desencapsulado.

#### Instrucciones

1. Con la captura `captura_http.pcapng` abierta, haz clic sobre el paquete `GET / HTTP/1.1` identificado en el Paso 4.

2. En el panel de detalles, expande **todas** las secciones disponibles. Deberías ver:

   ```
   ▼ Frame X: XXX bytes on wire
   ▼ Ethernet II, Src: ..., Dst: ...
   ▼ Internet Protocol Version 4, Src: ..., Dst: ...
   ▼ Transmission Control Protocol, Src Port: XXXXX, Dst Port: 80
   ▼ Hypertext Transfer Protocol
       ▼ GET / HTTP/1.1\r\n
           Request Method: GET
           Request URI: /
           Request Version: HTTP/1.1
       Host: httpforever.com\r\n
       User-Agent: Mozilla/5.0 ...\r\n
   ```

3. **Analiza la sección TCP** (Capa 4 — Transporte):
   - Anota el puerto de origen (`Source Port`).
   - Anota el puerto de destino (`Destination Port`) — debería ser `80`.
   - Anota el valor del campo `Sequence Number`.
   - Anota las flags activas (debería incluir `PSH, ACK`).

4. **Analiza la sección HTTP** (Capas 5-7 — Sesión/Presentación/Aplicación):
   - Anota el método HTTP (`GET`, `POST`, etc.).
   - Anota el campo `Host`.
   - Anota el campo `User-Agent` (identifica el navegador).

5. Completa la **Tabla de Mapeo OSI-TCP/IP** con todos los valores observados.

6. **Ejercicio de desencapsulado:** Ahora haz clic sobre el paquete de **respuesta** `HTTP/1.1 200 OK`. Observa que la estructura es idéntica pero en sentido inverso: los datos de aplicación están en la capa superior y los encabezados de red en las capas inferiores. Esto representa el proceso de **desencapsulado** que realiza tu computadora al recibir la respuesta.

#### Tabla de Mapeo OSI-TCP/IP — Para completar

| Capa OSI              | N° | Capa TCP/IP         | PDU       | Protocolo  | Sección en Wireshark                    | Campo / Valor observado |
|-----------------------|----|---------------------|-----------|------------|-----------------------------------------|-------------------------|
| Aplicación            | 7  | Aplicación          | Datos     | HTTP       | Hypertext Transfer Protocol             | Método:                 |
| Presentación          | 6  | Aplicación          | Datos     | HTTP       | (codificación del contenido)            | Content-Type:           |
| Sesión                | 5  | Aplicación          | Datos     | HTTP       | (control de sesión HTTP)                | Host:                   |
| Transporte            | 4  | Transporte          | Segmento  | TCP        | Transmission Control Protocol           | Puerto destino:         |
| Transporte            | 4  | Transporte          | Segmento  | TCP        | Transmission Control Protocol           | Sequence Number:        |
| Red                   | 3  | Internet            | Paquete   | IPv4       | Internet Protocol Version 4             | IP Destino:             |
| Red                   | 3  | Internet            | Paquete   | IPv4       | Internet Protocol Version 4             | TTL:                    |
| Enlace de datos       | 2  | Acceso a la red     | Trama     | Ethernet   | Ethernet II                             | MAC Destino:            |
| Física                | 1  | Acceso a la red     | Bits      | (eléctrico/óptico/radio) | Frame (tamaño total)       | Tamaño total (bytes):   |

#### Salida esperada (valores de referencia)

```
Transmission Control Protocol:
  Source Port:        52341  (puerto efímero del navegador)
  Destination Port:   80     (HTTP)
  Sequence Number:    1
  Flags:              PSH, ACK

Hypertext Transfer Protocol:
  Request Method:     GET
  Request URI:        /
  Host:               httpforever.com
  User-Agent:         Mozilla/5.0 (Windows NT 10.0; Win64; x64)...
  Accept:             text/html,application/xhtml+xml,...
```

#### Visualización del proceso de encapsulado

```
ENCAPSULADO (al enviar datos — de arriba hacia abajo):

Capa 7 (Aplicación):   [  Datos HTTP: "GET / HTTP/1.1"  ]
                                         ↓ añade encabezado TCP
Capa 4 (Transporte):   [ Enc.TCP | Datos HTTP: "GET / HTTP/1.1" ]
                                         ↓ añade encabezado IP
Capa 3 (Red):          [ Enc.IP | Enc.TCP | Datos HTTP ]
                                         ↓ añade encabezado + cola Ethernet
Capa 2 (Enlace):       [ Enc.Eth | Enc.IP | Enc.TCP | Datos HTTP | Cola.Eth ]
                                         ↓ convierte a bits
Capa 1 (Física):       01001000 01010100 01010100 01010000 ... (bits)

DESENCAPSULADO (al recibir datos — de abajo hacia arriba):

Capa 1 (Física):       Recibe bits eléctricos/ópticos/radio
                                         ↑ reconstruye trama
Capa 2 (Enlace):       Verifica MAC destino, retira encabezado Ethernet
                                         ↑ retira encabezado IP
Capa 3 (Red):          Verifica IP destino, retira encabezado IP
                                         ↑ retira encabezado TCP
Capa 4 (Transporte):   Verifica puerto, reensambla segmentos, retira enc. TCP
                                         ↑ entrega datos
Capa 7 (Aplicación):   Recibe "HTTP/1.1 200 OK..." → el navegador renderiza la página
```

#### Verificación

✅ La tabla de mapeo OSI-TCP/IP está completamente llenada con valores reales.
✅ Puedes describir verbalmente qué encabezado añade cada capa durante el encapsulado.
✅ Comprendes que el desencapsulado es el proceso inverso, retirando encabezados de abajo hacia arriba.
✅ Identificas la diferencia entre un paquete ICMP (sin capa 4 TCP/UDP) y un paquete HTTP (con capa 4 TCP).

---

### Paso 6 — Usar filtros de Wireshark para comparar protocolos

**Objetivo:** Aplicar filtros en Wireshark para comparar la estructura de diferentes tipos de paquetes y reforzar el mapeo con las capas OSI.

#### Instrucciones

1. Abre la captura `captura_ping.pcapng` en Wireshark.

2. Prueba los siguientes filtros uno por uno, escribiéndolos en la barra de filtro y presionando Enter. Anota cuántos paquetes aparecen con cada filtro:

   | Filtro Wireshark    | Descripción                              | N° de paquetes observados |
   |---------------------|------------------------------------------|---------------------------|
   | `icmp`              | Solo paquetes ICMP (ping)                |                           |
   | `ip`                | Solo paquetes IPv4 (capa 3)              |                           |
   | `eth`               | Todas las tramas Ethernet (capa 2)       |                           |
   | `icmp.type == 8`    | Solo Echo Requests (pings enviados)      |                           |
   | `icmp.type == 0`    | Solo Echo Replies (respuestas recibidas) |                           |

3. Ahora abre la captura `captura_http.pcapng` y prueba:

   | Filtro Wireshark       | Descripción                          | N° de paquetes observados |
   |------------------------|--------------------------------------|---------------------------|
   | `tcp`                  | Solo segmentos TCP (capa 4)          |                           |
   | `tcp.port == 80`       | Solo TCP en puerto 80 (HTTP)         |                           |
   | `http`                 | Solo paquetes HTTP (capa aplicación) |                           |
   | `http.request.method == "GET"` | Solo solicitudes GET HTTP  |                           |
   | `tcp.flags.syn == 1`   | Solo paquetes SYN (inicio de sesión TCP) |                       |

4. **Observación clave:** Nota que el filtro `tcp` muestra MÁS paquetes que el filtro `http`. Esto se debe a que TCP incluye también los paquetes de control de la conexión (SYN, ACK, FIN) que no contienen datos HTTP. Esto ilustra la diferencia entre la capa 4 (Transporte) y la capa de aplicación.

#### Verificación

✅ Comprendes que diferentes filtros en Wireshark corresponden a diferentes capas OSI.
✅ Puedes explicar por qué `tcp` muestra más paquetes que `http`.
✅ Las tablas de filtros están completadas con los valores observados.

---

## 7. Validación y Pruebas

Una vez completados todos los pasos, realiza las siguientes verificaciones finales para confirmar que la práctica fue ejecutada correctamente:

### Lista de verificación final

| N° | Criterio de validación                                                                 | ✅/❌ |
|----|----------------------------------------------------------------------------------------|-------|
| 1  | Tienes dos archivos de captura guardados: `captura_ping.pcapng` y `captura_http.pcapng` |       |
| 2  | La Tabla de Encapsulado (Paso 3) está completamente llenada con valores reales          |       |
| 3  | La Tabla de Mapeo OSI-TCP/IP (Paso 5) tiene datos en todas las filas                   |       |
| 4  | Puedes identificar en Wireshark las secciones Ethernet II, IPv4, TCP y HTTP             |       |
| 5  | Puedes explicar la diferencia entre encapsulado y desencapsulado                        |       |
| 6  | Las tablas de filtros (Paso 6) están completadas                                        |       |
| 7  | Puedes nombrar el PDU correcto para cada capa: bits, trama, paquete, segmento, datos   |       |

### Preguntas de reflexión (para incluir en tu reporte)

Responde brevemente las siguientes preguntas basándote en lo que observaste:

1. **¿Qué campo del encabezado Ethernet indica qué protocolo de capa 3 está encapsulado dentro de la trama?** ¿Cuál es su valor para IPv4?

2. **¿Por qué el paquete ICMP no tiene una sección TCP o UDP en Wireshark?** ¿A qué capa pertenece ICMP según lo que observaste?

3. **¿Qué diferencia observas entre el campo TTL de un paquete enviado desde Windows versus uno enviado desde Linux/macOS?** ¿Por qué crees que existen estos valores predeterminados diferentes?

4. **En el diagrama de encapsulado del Paso 5, ¿cuántos encabezados diferentes se añaden a los datos de aplicación antes de ser transmitidos?** Nómbralos en orden.

5. **¿Qué representa el campo `Sequence Number` en TCP?** ¿Para qué sirve en el proceso de comunicación?

---

## 8. Solución de Problemas

### Problema 1 — Wireshark no muestra paquetes al ejecutar el `ping`

**Síntoma:**
La lista de paquetes en Wireshark permanece vacía o no aparecen paquetes ICMP aunque el comando `ping` se ejecuta correctamente en la terminal y devuelve respuestas.

**Causa probable:**
La interfaz de red seleccionada en Wireshark no es la misma que está siendo utilizada para enviar el tráfico `ping`. Por ejemplo, el equipo puede estar usando Wi-Fi pero Wireshark está capturando en la interfaz Ethernet (que está inactiva), o viceversa. También puede ocurrir que el filtro `icmp` esté mal escrito o aplicado antes de iniciar la captura como filtro de captura en lugar de filtro de visualización.

**Solución:**

1. Detén la captura actual en Wireshark.
2. Verifica qué interfaz está activa en tu sistema operativo:

   **Windows (CMD):**
   ```cmd
   ipconfig
   ```
   Busca la interfaz que tiene una dirección IPv4 asignada (ej. `192.168.1.x`).

   **macOS / Linux (Terminal):**
   ```bash
   ip addr
   # o en macOS:
   ifconfig
   ```
   Busca la interfaz con una dirección `inet` activa (distinta de `127.0.0.1`).

3. Regresa a Wireshark y selecciona la interfaz correcta (la que tiene la IP activa).
4. Asegúrate de que el filtro `icmp` esté escrito en la barra de **filtro de visualización** (parte superior, se vuelve verde cuando es válido), NO en el campo de filtro de captura.
5. Inicia la captura nuevamente y ejecuta el `ping`.

---

### Problema 2 — No aparecen paquetes HTTP en la captura (solo HTTPS/TLS)

**Síntoma:**
Al navegar a un sitio web y filtrar por `tcp.port == 80` o `http`, Wireshark no muestra paquetes HTTP. En su lugar, aparecen paquetes con protocolo `TLS` o `TCP` sin contenido HTTP visible. La columna **Info** muestra `Application Data` en lugar de `GET / HTTP/1.1`.

**Causa probable:**
El navegador está conectándose al sitio mediante HTTPS (puerto 443) en lugar de HTTP (puerto 80). Los sitios modernos redirigen automáticamente de HTTP a HTTPS, y el tráfico HTTPS está cifrado con TLS, por lo que Wireshark no puede mostrar el contenido de la capa de aplicación en texto claro. Algunos navegadores también tienen configuraciones que fuerzan HTTPS (`HSTS`).

**Solución:**

1. Verifica que estás usando una URL que comienza explícitamente con `http://` (no `https://`):
   ```
   http://httpforever.com
   ```
   o:
   ```
   http://neverssl.com
   ```

2. Si el navegador redirige automáticamente a HTTPS, prueba con otro navegador o deshabilita temporalmente las redirecciones HSTS borrando la caché del navegador.

3. Como alternativa, puedes generar tráfico HTTP usando `curl` desde la terminal:

   **Windows (PowerShell):**
   ```powershell
   curl http://httpforever.com
   ```

   **macOS / Linux:**
   ```bash
   curl -v http://httpforever.com
   ```

4. Si ninguna de las opciones anteriores funciona, puedes usar la captura de tráfico DNS (puerto 53) como alternativa para observar la capa de aplicación:
   - Filtro en Wireshark: `dns`
   - Ejecutar en terminal: `nslookup google.com` (Windows) o `dig google.com` (Linux/macOS)
   - Los paquetes DNS mostrarán la capa de aplicación sin cifrado.

---

## 9. Limpieza del Entorno

Al finalizar la práctica, realiza los siguientes pasos para dejar el entorno ordenado:

1. **Cierra las capturas activas en Wireshark:** Ve a `Capture > Stop` si hay alguna captura en curso.

2. **Organiza tus archivos de captura:** Mueve ambos archivos a una carpeta dedicada para la práctica:
   ```
   /MisCursos/Redes/Practica01/
   ├── captura_ping.pcapng
   ├── captura_http.pcapng
   └── reporte_practica01.docx  (tu reporte)
   ```

3. **Cierra Wireshark** correctamente usando `File > Quit` o `Archivo > Salir`.

4. **Limpia el historial de la terminal** si lo deseas (opcional):

   **Windows (CMD):**
   ```cmd
   cls
   ```

   **Linux:**
   ```bash
   clear
   ```

5. **Conserva los archivos `.pcapng`:** Estos archivos serán referenciados en prácticas posteriores del curso para comparar con capturas de tráfico más complejas.

> **⚠️ Importante:** No elimines los archivos `captura_ping.pcapng` y `captura_http.pcapng`. Pueden ser solicitados como evidencia de la práctica o reutilizados en actividades de evaluación.

---

## 10. Resumen

### Lo que aprendiste en esta práctica

En esta práctica aplicaste de forma práctica los conceptos teóricos de los modelos OSI y TCP/IP que estudiaste en los temas 1.1, 1.2 y 1.3. Los logros clave fueron:

| Concepto                  | Lo que hiciste                                                                 |
|---------------------------|--------------------------------------------------------------------------------|
| **Encapsulado**           | Observaste cómo cada capa añade su encabezado (Ethernet → IP → TCP → HTTP)    |
| **Desencapsulado**        | Analizaste el proceso inverso en los paquetes de respuesta                     |
| **PDUs por capa**         | Identificaste bits, tramas, paquetes, segmentos y datos en capturas reales     |
| **Mapeo OSI ↔ TCP/IP**   | Completaste una tabla de correspondencia entre ambos modelos                   |
| **Filtros Wireshark**     | Aprendiste a usar `icmp`, `tcp`, `http` y filtros específicos de campos        |
| **Protocolos en práctica**| Trabajaste con ICMP, IPv4, Ethernet, TCP y HTTP en capturas reales             |

### Diagrama resumen: Correspondencia OSI — TCP/IP — Wireshark

```
Modelo OSI          Modelo TCP/IP       PDU          Sección en Wireshark
─────────────────   ─────────────────   ──────────   ──────────────────────────────
7 - Aplicación   ┐                      Datos        Hypertext Transfer Protocol
6 - Presentación ├─ Aplicación          Datos        (parte de la sección HTTP)
5 - Sesión       ┘                      Datos        (parte de la sección HTTP)
4 - Transporte   ─── Transporte         Segmento     Transmission Control Protocol
3 - Red          ─── Internet           Paquete      Internet Protocol Version 4
2 - Enlace datos ┐                      Trama        Ethernet II
1 - Física       ├─ Acceso a la red     Bits         Frame (tamaño en bytes/wire)
```

### Conexión con el resto del curso

Los conceptos practicados aquí son la base para:
- **Práctica 3 y 4:** Configuración de switches y routers en Packet Tracer — ahora entiendes qué procesa cada dispositivo (los switches operan en capa 2; los routers en capa 3).
- **Práctica 6:** Diagnóstico de red — los comandos `ping` y `traceroute` generan tráfico ICMP que ya sabes analizar.
- **Práctica 7:** Integración — analizarás tráfico más complejo que incluye todos los protocolos vistos aquí.

### Recursos adicionales

- 📖 [Documentación oficial de Wireshark (en inglés)](https://www.wireshark.org/docs/)
- 📖 [Wireshark User's Guide — Filtros de visualización](https://www.wireshark.org/docs/wsug_html_chunked/ChWorkBuildDisplayFilterSection.html)
- 📖 [RFC 792 — Internet Control Message Protocol (ICMP)](https://www.rfc-editor.org/rfc/rfc792)
- 📖 [RFC 9110 — HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110)
- 🎥 [Cisco Networking Academy — Introduction to Networks (CCNA)](https://www.netacad.com/courses/networking/ccna-introduction-networks)
- 🔧 [Wireshark Sample Captures (para práctica adicional)](https://wiki.wireshark.org/SampleCaptures)

---

*Práctica 1 de 7 — Módulo 1: Fundamentos de Redes de Computadoras*
*Próxima práctica: Práctica 2 — Análisis del Modelo OSI con Cisco Packet Tracer*

---
LAB_END---
