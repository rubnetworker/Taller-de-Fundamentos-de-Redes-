# Práctica 6 — Diagnóstico y Resolución Guiada de Incidentes de Red

## Metadatos

| Campo            | Detalle                                      |
|------------------|----------------------------------------------|
| **Duración**     | 33 minutos                                   |
| **Complejidad**  | Media                                        |
| **Nivel Bloom**  | Aplicar (Apply)                              |
| **Modalidad**    | Equipo real del estudiante + análisis textual|
| **Práctica N.°** | 6 de 7                                       |

---

## Descripción General

En esta práctica el estudiante ejecutará los comandos fundamentales de diagnóstico de red (`ping`, `ipconfig`/`ifconfig`, `tracert`/`traceroute`, `nslookup`, `netstat`) directamente en su equipo real, interpretará cada salida con criterio técnico y aplicará una metodología de troubleshooting por capas OSI para resolver tres escenarios de incidentes descritos con outputs reales. La práctica culmina con la redacción de un ticket de incidente estructurado para cada escenario, siguiendo un formato básico alineado con ITIL. Al finalizar, el estudiante habrá convertido el conocimiento teórico de los temas 6.1–6.3 en habilidades de diagnóstico aplicables a cualquier entorno de red.

---

## Objetivos de Aprendizaje

- [ ] Ejecutar e interpretar correctamente los comandos `ping`, `ipconfig`/`ifconfig`, `tracert`/`traceroute`, `nslookup` y `netstat` en el sistema operativo del equipo propio.
- [ ] Aplicar la metodología de troubleshooting *bottom-up* (de Capa 1 hacia capas superiores) para identificar la causa raíz de un incidente de red.
- [ ] Interpretar salidas de comandos y fragmentos de log para detectar pérdida de paquetes, latencia elevada y fallos de resolución DNS.
- [ ] Documentar el proceso de diagnóstico y resolución en un ticket de incidente estructurado con campos de síntoma, pasos ejecutados, hallazgo y solución.

---

## Prerrequisitos

### Conocimiento previo
- Comprensión del modelo OSI y TCP/IP (Prácticas 1–5).
- Conocimiento de direccionamiento IPv4: dirección IP, máscara de subred, gateway (Práctica 2).
- Lectura previa de los temas **6.1, 6.2 y 6.3** del módulo sobre comandos y utilidades de diagnóstico.

### Acceso requerido
- Equipo con sistema operativo Windows, macOS o Linux con acceso a terminal.
- Conexión a internet activa (se realizarán pings y trazas a destinos públicos).
- Permisos para ejecutar comandos de red (usuario estándar es suficiente; algunos comandos en Linux/macOS pueden requerir `sudo`).

> **⚠️ Aviso ético:** Durante esta práctica solo capturarás y analizarás tráfico de tu propia red. El uso de herramientas de diagnóstico en redes ajenas sin autorización expresa es ilegal y antiético.

---

## Entorno de Laboratorio

### Hardware mínimo requerido

| Componente         | Requisito mínimo                              |
|--------------------|-----------------------------------------------|
| Procesador         | 2 GHz dual-core                               |
| RAM                | 4 GB                                          |
| Almacenamiento     | 500 MB libres para capturas y documentos      |
| Interfaz de red    | NIC Ethernet o Wi-Fi funcional                |
| Conexión a internet| Activa durante toda la práctica               |

### Software requerido

| Software                        | Versión mínima          | Uso en esta práctica                  |
|---------------------------------|-------------------------|---------------------------------------|
| Terminal del SO                 | Nativa del sistema      | Ejecución de todos los comandos       |
| Editor de texto / procesador    | Cualquier versión reciente | Redacción de tickets de incidente  |
| Navegador web                   | Chrome/Firefox/Edge 110+| Referencia y verificación opcional    |

### Verificación del entorno antes de comenzar

Abre tu terminal y confirma que tienes conectividad básica ejecutando el siguiente comando según tu sistema operativo:

**Windows (CMD o PowerShell):**
```cmd
ping 127.0.0.1 -n 2
```

**macOS / Linux (Terminal):**
```bash
ping -c 2 127.0.0.1
```

Si recibes respuestas del loopback, el entorno está listo. Si no, revisa que la interfaz de red esté activa antes de continuar.

---

## Procedimiento

> **Estructura de la práctica:**
> - **Fase 1 (Pasos 1–5):** Ejercicios guiados — ejecución e interpretación de comandos en tu equipo real. *(~18 minutos)*
> - **Fase 2 (Pasos 6–8):** Resolución de incidentes — análisis de escenarios con outputs proporcionados y redacción de tickets. *(~15 minutos)*

---

### FASE 1: Ejercicios Guiados de Diagnóstico

---

### Paso 1 — Diagnóstico de Conectividad con `ping` (Metodología por Capas)

**Objetivo:** Ejecutar `ping` en secuencia progresiva para verificar la conectividad desde el loopback hasta un destino externo, identificando en qué capa se rompe la comunicación.

#### Instrucciones

1. Abre tu terminal (CMD/PowerShell en Windows; Terminal en macOS/Linux).

2. Ejecuta los siguientes comandos **en orden**. Registra el resultado de cada uno en tu hoja de trabajo:

**Windows:**
```cmd
REM Paso A: Verificar interfaz de loopback (Capa 3 local)
ping 127.0.0.1 -n 4

REM Paso B: Obtener tu gateway (anota la dirección que aparece)
ipconfig | findstr "Puerta de enlace"

REM Paso C: Verificar conectividad con el gateway (Capa 3 local)
ping 192.168.1.1 -n 4
REM (Reemplaza 192.168.1.1 con el gateway obtenido en el paso B)

REM Paso D: Verificar conectividad hacia Internet por IP (Capa 3 externa)
ping 8.8.8.8 -n 4

REM Paso E: Verificar resolución DNS (Capa de Aplicación)
ping google.com -n 4
```

**macOS / Linux:**
```bash
# Paso A: Verificar interfaz de loopback (Capa 3 local)
ping -c 4 127.0.0.1

# Paso B: Obtener tu gateway
ip route show default
# o en macOS:
netstat -rn | grep default

# Paso C: Verificar conectividad con el gateway (Capa 3 local)
ping -c 4 192.168.1.1
# (Reemplaza con el gateway obtenido en el paso B)

# Paso D: Verificar conectividad hacia Internet por IP (Capa 3 externa)
ping -c 4 8.8.8.8

# Paso E: Verificar resolución DNS (Capa de Aplicación)
ping -c 4 google.com
```

3. Para cada resultado, completa la siguiente tabla en tu hoja de trabajo:

| Paso | Destino       | ¿Responde? | RTT promedio | % Pérdida | Observación |
|------|---------------|------------|--------------|-----------|-------------|
| A    | 127.0.0.1     |            |              |           |             |
| B    | Gateway local |            |              |           |             |
| C    | 8.8.8.8       |            |              |           |             |
| D    | google.com    |            |              |           |             |

#### Salida esperada (ejemplo de referencia — Windows)

```
Haciendo ping a 8.8.8.8 con 32 bytes de datos:
Respuesta desde 8.8.8.8: bytes=32 tiempo=14ms TTL=118
Respuesta desde 8.8.8.8: bytes=32 tiempo=13ms TTL=118
Respuesta desde 8.8.8.8: bytes=32 tiempo=15ms TTL=118
Respuesta desde 8.8.8.8: bytes=32 tiempo=14ms TTL=118

Estadísticas de ping para 8.8.8.8:
    Paquetes: enviados = 4, recibidos = 4, perdidos = 0 (0% perdidos)
Tiempos aproximados de ida y vuelta en milisegundos:
    Mínimo = 13ms, Máximo = 15ms, Media = 14ms
```

#### Preguntas de análisis — Paso 1

Responde brevemente en tu hoja de trabajo:

1. ¿Cuántos saltos de router estimarías que atravesó el paquete hacia `8.8.8.8`? (Pista: TTL inicial de Windows es 128; TTL inicial de Linux es 64.)
2. Si el Paso D (`ping 8.8.8.8`) funciona pero el Paso E (`ping google.com`) falla, ¿en qué capa OSI está el problema y qué componente de red es el responsable?
3. ¿Qué significaría recibir una dirección IP `169.254.x.x` al ejecutar `ipconfig` en Windows?

#### Verificación

✅ Debes obtener respuestas exitosas en los pasos A, B, C y D si tu equipo tiene conectividad a internet.
✅ El paso E debe resolver el nombre `google.com` a una dirección IP antes de enviar los paquetes.
✅ La pérdida de paquetes debe ser 0% en condiciones normales de red.

---

### Paso 2 — Inspección de Configuración de Red con `ipconfig` / `ifconfig`

**Objetivo:** Obtener y analizar la configuración completa de las interfaces de red del equipo, identificando todos los parámetros relevantes para el diagnóstico.

#### Instrucciones

1. Ejecuta el comando de configuración completa según tu sistema operativo:

**Windows:**
```cmd
ipconfig /all
```

**Linux:**
```bash
# Opción 1: Comando clásico
ifconfig -a

# Opción 2: Comando moderno (recomendado en distros actuales)
ip addr show
```

**macOS:**
```bash
ifconfig -a
```

2. Localiza la interfaz de red principal (generalmente `Ethernet0`, `eth0`, `en0` o `Wi-Fi`) y registra los siguientes valores en tu hoja de trabajo:

| Campo                        | Valor en tu equipo |
|------------------------------|--------------------|
| Nombre de la interfaz        |                    |
| Dirección MAC (física)       |                    |
| ¿DHCP habilitado?            |                    |
| Dirección IPv4               |                    |
| Máscara de subred            |                    |
| Puerta de enlace predeterminada |               |
| Servidor(es) DNS             |                    |
| Dirección IPv6 de enlace local |                |

3. Ejecuta también el siguiente comando para ver la tabla de rutas:

**Windows:**
```cmd
route print
```

**Linux / macOS:**
```bash
ip route show
# o en macOS:
netstat -rn
```

4. Identifica la ruta predeterminada (`0.0.0.0/0` en Windows o `default` en Linux/macOS) y anota la dirección del gateway que aparece.

#### Salida esperada (ejemplo de referencia — Linux)

```
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.105  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::a6bb:6dff:fe12:3456  prefixlen 64  scopeid 0x20<link>
        ether a4:bb:6d:12:34:56  txqueuelen 1000  (Ethernet)
        RX packets 15234  bytes 18543210 (18.5 MB)
        TX packets 9821   bytes 1234567 (1.2 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

#### Preguntas de análisis — Paso 2

1. ¿Qué indica el flag `RUNNING` en la salida de `ifconfig`? ¿Qué diferencia hay si solo aparece `UP` pero no `RUNNING`?
2. En la salida de `ipconfig /all` (Windows), ¿qué significan los campos "Concesión obtenida" y "La concesión expira"? ¿Qué ocurre cuando la concesión expira?
3. Si los contadores `RX errors` o `TX errors` de `ifconfig` son distintos de cero, ¿qué tipo de problema podría estar ocurriendo?

#### Verificación

✅ La interfaz principal debe mostrar una dirección IPv4 válida (no `169.254.x.x`).
✅ Debe existir una puerta de enlace predeterminada configurada.
✅ Deben aparecer al menos un servidor DNS configurado.
✅ Los contadores de errores (`RX errors`, `TX errors`) deben ser 0 en condiciones normales.

---

### Paso 3 — Análisis de Ruta con `tracert` / `traceroute`

**Objetivo:** Trazar la ruta que siguen los paquetes desde el equipo hasta un destino externo, identificando cada salto intermedio y midiendo la latencia acumulada.

#### Instrucciones

1. Ejecuta el comando de trazado de ruta hacia un destino público:

**Windows:**
```cmd
tracert 8.8.8.8
```

**Linux / macOS:**
```bash
traceroute 8.8.8.8
```

> **Nota:** En algunos sistemas Linux, `traceroute` puede no estar instalado por defecto. Instálalo con:
> ```bash
> sudo apt install traceroute    # Debian/Ubuntu
> sudo yum install traceroute    # CentOS/RHEL
> ```

2. Espera a que el comando complete (puede tardar 30–60 segundos). Registra la salida completa.

3. Ejecuta una segunda traza hacia un nombre de dominio para observar la resolución:

**Windows:**
```cmd
tracert www.cloudflare.com
```

**Linux / macOS:**
```bash
traceroute www.cloudflare.com
```

4. Analiza la salida e identifica en tu hoja de trabajo:
   - Número total de saltos hasta el destino.
   - El salto donde la latencia aumenta significativamente (posible cuello de botella).
   - Saltos que muestran `* * *` (sin respuesta) — ¿qué significa esto?

#### Salida esperada (ejemplo de referencia — Windows)

```
Traza a la dirección 8.8.8.8 sobre un máximo de 30 saltos

  1     1 ms     1 ms     1 ms  192.168.1.1
  2    12 ms    11 ms    12 ms  10.20.30.1
  3    13 ms    14 ms    13 ms  200.40.50.1
  4     *        *        *     Tiempo de espera agotado para esta solicitud.
  5    15 ms    14 ms    15 ms  142.250.200.1
  6    14 ms    13 ms    14 ms  8.8.8.8

Traza completa.
```

#### Preguntas de análisis — Paso 3

1. ¿Qué representa el primer salto (hop 1) en la traza? ¿Qué dispositivo es ese?
2. Cuando un salto muestra `* * *`, ¿significa necesariamente que hay un fallo en la ruta? Explica.
3. Si el salto 3 muestra una latencia de 200 ms y todos los saltos anteriores muestran menos de 15 ms, ¿qué conclusión puedes sacar sobre ese punto de la red?

#### Verificación

✅ El primer salto debe corresponder a la dirección de tu gateway local.
✅ La traza debe completarse (llegar al destino) o mostrar el punto exacto donde se interrumpe.
✅ La latencia debe aumentar progresivamente con cada salto, no de forma errática.

---

### Paso 4 — Resolución DNS con `nslookup`

**Objetivo:** Consultar el sistema DNS para resolver nombres de dominio a direcciones IP, verificar qué servidor DNS responde y consultar registros específicos.

#### Instrucciones

1. Ejecuta las siguientes consultas DNS (los comandos son idénticos en Windows, macOS y Linux):

```cmd
REM / bash — Consulta básica de dirección IP para un dominio
nslookup google.com

REM / bash — Consulta usando un servidor DNS alternativo (DNS de Cloudflare)
nslookup google.com 1.1.1.1

REM / bash — Consulta de registros MX (servidores de correo)
nslookup -type=MX google.com

REM / bash — Consulta de registros NS (servidores de nombres)
nslookup -type=NS google.com

REM / bash — Resolución inversa: de IP a nombre (PTR record)
nslookup 8.8.8.8
```

2. Registra los resultados en tu hoja de trabajo:

| Consulta                     | Servidor DNS usado | Resultado obtenido |
|------------------------------|--------------------|--------------------|
| `nslookup google.com`        |                    |                    |
| `nslookup google.com 1.1.1.1`|                    |                    |
| `nslookup -type=MX google.com`|                   |                    |
| `nslookup 8.8.8.8`           |                    |                    |

3. Observa si `nslookup` indica que la respuesta es **"Authoritative"** (autoritativa) o **"Non-authoritative answer"** (respuesta no autoritativa). Anota qué significa cada una.

#### Salida esperada (ejemplo de referencia)

```
Servidor:  dns.google
Address:  8.8.8.8

Respuesta no autoritativa:
Nombre:  google.com
Addresses:  142.250.200.46
          2607:f8b0:4004:c09::71
```

#### Preguntas de análisis — Paso 4

1. ¿Por qué `nslookup google.com` y `nslookup google.com 1.1.1.1` pueden devolver direcciones IP diferentes para el mismo dominio?
2. ¿Qué información proporciona un registro MX y para qué sirve en la práctica?
3. Si `nslookup google.com` falla pero `ping 8.8.8.8` funciona, ¿cuál es el diagnóstico?

#### Verificación

✅ `nslookup google.com` debe devolver al menos una dirección IPv4 válida.
✅ La consulta con `1.1.1.1` debe funcionar aunque sea diferente al DNS configurado en el equipo.
✅ La resolución inversa de `8.8.8.8` debe devolver `dns.google`.

---

### Paso 5 — Conexiones Activas con `netstat`

**Objetivo:** Inspeccionar las conexiones de red activas, puertos en escucha y estadísticas de protocolo del equipo.

#### Instrucciones

1. Ejecuta los siguientes comandos de `netstat`:

**Windows:**
```cmd
REM Ver todas las conexiones y puertos en escucha con sus PIDs
netstat -ano

REM Ver estadísticas por protocolo
netstat -s

REM Ver la tabla de enrutamiento
netstat -r

REM Filtrar solo conexiones ESTABLISHED (activas)
netstat -ano | findstr ESTABLISHED
```

**Linux / macOS:**
```bash
# Ver todas las conexiones y puertos en escucha
netstat -an

# Ver con nombres de procesos (requiere sudo en algunos sistemas)
sudo netstat -anp

# Ver estadísticas por protocolo
netstat -s

# Filtrar solo conexiones ESTABLISHED
netstat -an | grep ESTABLISHED

# Alternativa moderna en Linux (comando ss)
ss -tuln
```

2. Identifica en la salida:
   - Al menos **3 puertos en estado `LISTEN`** (escucha). Anota el número de puerto y el servicio que normalmente usa ese puerto.
   - Al menos **1 conexión en estado `ESTABLISHED`** (si existe). Anota la IP remota y el puerto.
   - Cualquier conexión en estado `TIME_WAIT` o `CLOSE_WAIT`.

3. Completa la tabla:

| Puerto local | Estado   | Servicio probable         |
|--------------|----------|---------------------------|
|              | LISTEN   |                           |
|              | LISTEN   |                           |
|              | LISTEN   |                           |
|              | ESTABLISHED (si aplica) |            |

#### Salida esperada (ejemplo de referencia — Windows)

```
  Proto  Dirección local          Dirección remota       Estado           PID
  TCP    0.0.0.0:135              0.0.0.0:0              ESCUCHANDO       1020
  TCP    0.0.0.0:443              0.0.0.0:0              ESCUCHANDO       4
  TCP    192.168.1.105:52341      142.250.200.46:443     ESTABLECIDO      3456
  TCP    192.168.1.105:52342      52.96.10.1:443         TIME_WAIT        0
```

#### Preguntas de análisis — Paso 5

1. ¿Qué significa el estado `TIME_WAIT` en una conexión TCP? ¿Es un problema?
2. Si ves un puerto desconocido en estado `LISTEN` con un PID que no reconoces, ¿cómo investigarías qué proceso lo está usando?
3. ¿Qué diferencia existe entre una conexión en `0.0.0.0:80` y una en `127.0.0.1:80` en términos de accesibilidad desde la red?

#### Verificación

✅ Deben aparecer puertos estándar en escucha (135, 443, 445 en Windows; 22, 53, 80 en Linux según los servicios instalados).
✅ Si tienes el navegador abierto, deben aparecer conexiones `ESTABLISHED` hacia puertos 443 (HTTPS).
✅ El comando debe ejecutarse sin errores de permisos.

---

### FASE 2: Resolución de Incidentes

> **Instrucciones generales para la Fase 2:**
> Los siguientes tres escenarios presentan incidentes de red reales con sus outputs de comandos. **No necesitas ejecutar estos comandos** — los outputs ya están proporcionados. Tu tarea es:
> 1. Analizar los outputs proporcionados.
> 2. Identificar la causa raíz siguiendo la metodología *bottom-up*.
> 3. Proponer la solución.
> 4. Completar el **Ticket de Incidente** para cada escenario.
>
> Usa el **Formato de Ticket de Incidente** que se proporciona al final de cada escenario.

---

### Paso 6 — Escenario 1: Fallo de Resolución DNS

**Objetivo:** Diagnosticar un incidente donde el usuario puede acceder a sitios por IP pero no por nombre de dominio.

#### Descripción del incidente

**Reporte del usuario:** *"No puedo abrir ninguna página web. El navegador dice 'No se puede acceder a este sitio'. Pero mi compañero, que está en la misma red, sí puede navegar sin problemas."*

**Equipo afectado:** PC-CONTABILIDAD-03 (Windows 10)
**Hora del reporte:** 14:32

#### Outputs de diagnóstico recopilados

El técnico ejecutó los siguientes comandos en el equipo afectado. Analiza cada salida:

**Output 1 — `ipconfig /all` (fragmento relevante):**
```
Adaptador Ethernet Ethernet0:
   Dirección IPv4. . . . . . . . . : 192.168.10.45(Preferido)
   Máscara de subred . . . . . . . : 255.255.255.0
   Puerta de enlace predeterminada : 192.168.10.1
   Servidores DNS . . . . . . . . . : 192.168.10.200
```

**Output 2 — `ping` en secuencia:**
```
C:\> ping 127.0.0.1 -n 2
Respuesta desde 127.0.0.1: bytes=32 tiempo<1ms TTL=128
Respuesta desde 127.0.0.1: bytes=32 tiempo<1ms TTL=128
[OK]

C:\> ping 192.168.10.1 -n 2
Respuesta desde 192.168.10.1: bytes=32 tiempo=1ms TTL=64
Respuesta desde 192.168.10.1: bytes=32 tiempo=1ms TTL=64
[OK]

C:\> ping 8.8.8.8 -n 2
Respuesta desde 8.8.8.8: bytes=32 tiempo=15ms TTL=118
Respuesta desde 8.8.8.8: bytes=32 tiempo=14ms TTL=118
[OK]

C:\> ping google.com -n 2
Ping request could not find host google.com.
Please check the name and try again.
[FALLO]
```

**Output 3 — `nslookup`:**
```
C:\> nslookup google.com
Servidor:  UnKnown
Address:  192.168.10.200

*** 192.168.10.200 no puede encontrar google.com: Server failed
```

**Output 4 — `nslookup` con DNS alternativo:**
```
C:\> nslookup google.com 8.8.8.8
Servidor:  dns.google
Address:  8.8.8.8

Respuesta no autoritativa:
Nombre:  google.com
Addresses:  142.250.200.46
```

**Output 5 — `ping` al servidor DNS interno:**
```
C:\> ping 192.168.10.200 -n 2
Solicitud de tiempo de espera agotada.
Solicitud de tiempo de espera agotada.
[FALLO — El servidor DNS interno no responde a ICMP]
```

#### Análisis guiado

Responde las siguientes preguntas en tu hoja de trabajo antes de completar el ticket:

1. Usando la metodología *bottom-up*, ¿hasta qué capa OSI funciona correctamente la conectividad? ¿En qué capa falla?
2. ¿Por qué `ping 8.8.8.8` funciona pero `ping google.com` falla?
3. ¿Qué nos indica el Output 4 (nslookup con DNS 8.8.8.8) sobre la naturaleza del problema?
4. ¿El problema es del equipo del usuario, del servidor DNS interno, o de la conexión a internet? Justifica.
5. ¿Qué solución inmediata (workaround) podrías aplicar para que el usuario navegue mientras se resuelve el problema raíz?

#### Ticket de Incidente — Escenario 1

Completa el siguiente ticket en tu hoja de trabajo o documento:

```
╔══════════════════════════════════════════════════════════════════╗
║              TICKET DE INCIDENTE — TI-2025-001                   ║
╠══════════════════════════════════════════════════════════════════╣
║ Fecha/Hora apertura : 2025-06-02  14:32                          ║
║ Equipo afectado     : PC-CONTABILIDAD-03                         ║
║ Reportado por       : Usuario del área de contabilidad           ║
╠══════════════════════════════════════════════════════════════════╣
║ SÍNTOMA REPORTADO:                                               ║
║ [Completa con la descripción del usuario]                        ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║ PASOS DE DIAGNÓSTICO EJECUTADOS:                                 ║
║ 1.                                                               ║
║ 2.                                                               ║
║ 3.                                                               ║
║ 4.                                                               ║
║ 5.                                                               ║
╠══════════════════════════════════════════════════════════════════╣
║ CAPA OSI DONDE SE DETECTÓ EL FALLO:                              ║
║ [Número y nombre de la capa]                                     ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║ CAUSA RAÍZ IDENTIFICADA:                                         ║
║ [Describe el problema específico]                                ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║ SOLUCIÓN APLICADA:                                               ║
║ [Describe los pasos de resolución]                               ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║ SOLUCIÓN INMEDIATA (WORKAROUND):                                 ║
║ [Describe la solución temporal si aplica]                        ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║ ESTADO: [ ] Abierto  [ ] En progreso  [X] Cerrado                ║
║ Técnico responsable: _______________________                     ║
╚══════════════════════════════════════════════════════════════════╝
```

#### Verificación

✅ El ticket debe identificar correctamente el servidor DNS interno (`192.168.10.200`) como causa raíz.
✅ La capa OSI identificada debe ser la **Capa 7 (Aplicación)** o **Capa de Aplicación/DNS**.
✅ El workaround debe incluir el cambio manual del DNS a `8.8.8.8` o `1.1.1.1`.

---

### Paso 7 — Escenario 2: Gateway Inalcanzable

**Objetivo:** Diagnosticar un incidente de pérdida total de conectividad externa causado por un problema en la configuración de la puerta de enlace.

#### Descripción del incidente

**Reporte del usuario:** *"Desde esta mañana no tengo internet en mi laptop. Tampoco puedo acceder al servidor de archivos de la empresa que está en otra sede. Pero sí puedo acceder a la impresora de red que está aquí en mi piso."*

**Equipo afectado:** LAPTOP-VENTAS-07 (Windows 11)
**Hora del reporte:** 09:15

#### Outputs de diagnóstico recopilados

**Output 1 — `ipconfig` (fragmento):**
```
Adaptador Ethernet Wi-Fi:
   Dirección IPv4. . . . . . . . . : 192.168.5.88(Preferido)
   Máscara de subred . . . . . . . : 255.255.255.0
   Puerta de enlace predeterminada : 192.168.5.254
   Servidores DNS . . . . . . . . . : 192.168.5.1
                                      8.8.8.8
```

**Output 2 — `ping` en secuencia:**
```
C:\> ping 127.0.0.1 -n 2
Respuesta desde 127.0.0.1: bytes=32 tiempo<1ms TTL=128  [OK]

C:\> ping 192.168.5.88 -n 2
Respuesta desde 192.168.5.88: bytes=32 tiempo<1ms TTL=128  [OK]

C:\> ping 192.168.5.10 -n 2
REM (192.168.5.10 es la impresora de red)
Respuesta desde 192.168.5.10: bytes=32 tiempo=2ms TTL=64  [OK]

C:\> ping 192.168.5.254 -n 4
REM (192.168.5.254 es el gateway configurado)
Solicitud de tiempo de espera agotada.
Solicitud de tiempo de espera agotada.
Solicitud de tiempo de espera agotada.
Solicitud de tiempo de espera agotada.
[FALLO]

C:\> ping 8.8.8.8 -n 2
Solicitud de tiempo de espera agotada.
Solicitud de tiempo de espera agotada.
[FALLO]
```

**Output 3 — `tracert`:**
```
C:\> tracert 8.8.8.8
Traza a la dirección 8.8.8.8 sobre un máximo de 30 saltos

  1     *        *        *     Tiempo de espera agotado para esta solicitud.
  2     *        *        *     Tiempo de espera agotado para esta solicitud.
  3     *        *        *     Tiempo de espera agotado para esta solicitud.
[Continúa con * hasta 30 saltos]
```

**Output 4 — `route print` (fragmento):**
```
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0      192.168.5.254  192.168.5.88      25
      192.168.5.0    255.255.255.0         On-link    192.168.5.88      281
    192.168.5.88  255.255.255.255         On-link    192.168.5.88      281
```

**Output 5 — Verificación adicional del técnico:**
```
C:\> arp -a
Interface: 192.168.5.88 --- 0x4
  Internet Address      Physical Address      Type
  192.168.5.10          00-1a-2b-3c-4d-5e     dynamic
  192.168.5.254         ff-ff-ff-ff-ff-ff     static
```

> **Nota técnica:** Una entrada ARP con dirección MAC `ff-ff-ff-ff-ff-ff` para el gateway indica que el equipo no ha podido resolver la MAC real del gateway mediante ARP — el gateway no está respondiendo a solicitudes ARP.

#### Análisis guiado

1. El usuario puede comunicarse con la impresora (`192.168.5.10`) pero no con el gateway (`192.168.5.254`). ¿En qué capa OSI se interrumpe la comunicación hacia el exterior?
2. Analiza el Output 5 (tabla ARP). ¿Qué indica la entrada `ff-ff-ff-ff-ff-ff` para el gateway?
3. ¿El problema es del equipo del usuario o del gateway? ¿Cómo lo determinarías?
4. Lista al menos **3 causas posibles** que explicarían por qué el gateway no responde.
5. ¿Qué pasos físicos y de configuración verificarías primero?

#### Ticket de Incidente — Escenario 2

```
╔══════════════════════════════════════════════════════════════════╗
║              TICKET DE INCIDENTE — TI-2025-002                   ║
╠══════════════════════════════════════════════════════════════════╣
║ Fecha/Hora apertura : 2025-06-02  09:15                          ║
║ Equipo afectado     : LAPTOP-VENTAS-07                           ║
║ Reportado por       : Usuario del área de ventas                 ║
╠══════════════════════════════════════════════════════════════════╣
║ SÍNTOMA REPORTADO:                                               ║
║ [Completa con la descripción del usuario]                        ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║ PASOS DE DIAGNÓSTICO EJECUTADOS:                                 ║
║ 1.                                                               ║
║ 2.                                                               ║
║ 3.                                                               ║
║ 4.                                                               ║
╠══════════════════════════════════════════════════════════════════╣
║ CAPA OSI DONDE SE DETECTÓ EL FALLO:                              ║
║ [Número y nombre de la capa]                                     ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║ CAUSA RAÍZ IDENTIFICADA:                                         ║
║ [Describe el problema específico]                                ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║ POSIBLES CAUSAS ADICIONALES A INVESTIGAR:                        ║
║ 1.                                                               ║
║ 2.                                                               ║
║ 3.                                                               ║
╠══════════════════════════════════════════════════════════════════╣
║ SOLUCIÓN APLICADA / PASOS DE ESCALAMIENTO:                       ║
║ [Describe los pasos de resolución o a quién escalar]             ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║ ESTADO: [ ] Abierto  [X] Escalado a nivel 2  [ ] Cerrado         ║
║ Técnico responsable: _______________________                     ║
╚══════════════════════════════════════════════════════════════════╝
```

#### Verificación

✅ El fallo debe identificarse en **Capa 2 (Enlace de datos) / Capa 3 (Red)** — el gateway no es alcanzable.
✅ Las causas posibles deben incluir: gateway apagado, cable desconectado en el router, dirección IP de gateway incorrecta, o fallo del router.
✅ El escalamiento debe indicar que se requiere acceso físico al dispositivo gateway.

---

### Paso 8 — Escenario 3: Pérdida de Paquetes Intermitente + Análisis de Log

**Objetivo:** Diagnosticar pérdida de paquetes intermitente usando `ping` extendido y correlacionar con un fragmento de log de router para identificar la causa raíz.

#### Descripción del incidente

**Reporte del usuario:** *"La conexión va y viene. A veces funciona bien, pero de repente se corta por unos segundos y vuelve. Las videollamadas se cortan constantemente. Esto pasa desde que cambiaron el cable del switch la semana pasada."*

**Equipo afectado:** PC-DISEÑO-12 (Linux Ubuntu 22.04)
**Hora del reporte:** 11:45

#### Outputs de diagnóstico recopilados

**Output 1 — `ping` extendido (100 paquetes):**
```bash
$ ping -c 100 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=118 time=14.2 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=118 time=13.9 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=118 time=14.1 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=118 time=245.8 ms
64 bytes from 8.8.8.8: icmp_seq=5 ttl=118 time=312.4 ms
From 8.8.8.8 icmp_seq=6: Destination Host Unreachable
From 8.8.8.8 icmp_seq=7: Destination Host Unreachable
From 8.8.8.8 icmp_seq=8: Destination Host Unreachable
64 bytes from 8.8.8.8: icmp_seq=9 ttl=118 time=15.0 ms
64 bytes from 8.8.8.8: icmp_seq=10 ttl=118 time=14.3 ms
[... patrón similar continúa ...]
64 bytes from 8.8.8.8: icmp_seq=98 ttl=118 time=14.1 ms
64 bytes from 8.8.8.8: icmp_seq=99 ttl=118 time=13.8 ms
64 bytes from 8.8.8.8: icmp_seq=100 ttl=118 time=14.2 ms

--- 8.8.8.8 ping statistics ---
100 packets transmitted, 87 received, 13% packet loss, time 99821ms
rtt min/avg/max/mdev = 13.800/28.450/312.400/45.231 ms
```

**Output 2 — `ifconfig` del equipo afectado:**
```
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.20.45  netmask 255.255.255.0  broadcast 192.168.20.255
        ether b8:27:eb:45:67:89  txqueuelen 1000  (Ethernet)
        RX packets 45231  bytes 62345678 (62.3 MB)
        TX packets 38921  bytes 12345678 (12.3 MB)
        RX errors 0  dropped 127  overruns 0  frame 0
        TX errors 3  dropped 0 overruns 0  carrier 3  collisions 0
```

**Output 3 — Fragmento de log del switch al que está conectado PC-DISEÑO-12:**

```
Jun  2 11:30:15 SW-PISO2 %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/12, changed state to down
Jun  2 11:30:18 SW-PISO2 %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/12, changed state to up
Jun  2 11:32:44 SW-PISO2 %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/12, changed state to down
Jun  2 11:32:47 SW-PISO2 %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/12, changed state to up
Jun  2 11:35:10 SW-PISO2 %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/12, changed state to down
Jun  2 11:35:14 SW-PISO2 %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/12, changed state to up
Jun  2 11:38:22 SW-PISO2 %ETH_PORT-3-IF_DOWN_LINK_FAILURE: Interface FastEthernet0/12 is down (Link failure)
Jun  2 11:38:29 SW-PISO2 %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/12, changed state to up
Jun  2 11:40:05 SW-PISO2 %PM-4-ERR_DISABLE: link-flap error detected on Fa0/12, putting Fa0/12 in err-disabled state
```

#### Análisis del log

Antes de completar el ticket, analiza el fragmento de log respondiendo:

1. ¿Qué interfaz del switch está experimentando problemas? ¿A qué equipo corresponde según el escenario?
2. El log muestra ciclos repetidos de `down → up → down → up`. ¿Cómo se llama este fenómeno en redes? ¿Qué lo puede causar?
3. La última línea del log menciona `err-disabled state`. ¿Qué significa esto? ¿El puerto sigue funcionando?
4. Correlaciona el timestamp del log (`11:30:15`) con el reporte del usuario (`11:45`) y el patrón del `ping`. ¿Son consistentes?
5. Los contadores `carrier 3` y `collisions 0` en `ifconfig`, junto con `dropped 127`, ¿qué indican sobre la calidad del enlace físico?

#### Análisis de la salida de `ping`

6. Con un 13% de pérdida de paquetes y un `mdev` de 45.231 ms, ¿qué tipo de aplicaciones se verían más afectadas y por qué?
7. El RTT mínimo es 13.8 ms pero el máximo es 312.4 ms. ¿Qué término describe esta variabilidad extrema? ¿Cómo afecta a las videollamadas?

#### Ticket de Incidente — Escenario 3

```
╔══════════════════════════════════════════════════════════════════╗
║              TICKET DE INCIDENTE — TI-2025-003                   ║
╠══════════════════════════════════════════════════════════════════╣
║ Fecha/Hora apertura : 2025-06-02  11:45                          ║
║ Equipo afectado     : PC-DISEÑO-12                               ║
║ Reportado por       : Usuario del área de diseño                 ║
╠══════════════════════════════════════════════════════════════════╣
║ SÍNTOMA REPORTADO:                                               ║
║ [Completa con la descripción del usuario]                        ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║ EVIDENCIA DEL LOG (cita las líneas relevantes):                  ║
║                                                                  ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║ MÉTRICAS CLAVE IDENTIFICADAS:                                    ║
║ - % Pérdida de paquetes:                                         ║
║ - RTT promedio:                                                  ║
║ - Jitter (mdev):                                                 ║
║ - Errores de interfaz:                                           ║
╠══════════════════════════════════════════════════════════════════╣
║ CAPA OSI DONDE SE DETECTÓ EL FALLO:                              ║
║ [Número y nombre de la capa]                                     ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║ CAUSA RAÍZ IDENTIFICADA:                                         ║
║ [Describe el problema específico]                                ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║ SOLUCIÓN APLICADA:                                               ║
║ [Describe los pasos de resolución]                               ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║ ACCIÓN PREVENTIVA RECOMENDADA:                                   ║
║ [Qué hacer para evitar que vuelva a ocurrir]                     ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║ ESTADO: [ ] Abierto  [ ] En progreso  [X] Cerrado                ║
║ Técnico responsable: _______________________                     ║
╚══════════════════════════════════════════════════════════════════╝
```

#### Verificación

✅ La causa raíz debe identificarse como **cable Ethernet defectuoso o mal conectado** (Capa 1 — Física).
✅ El fenómeno del log debe identificarse como **"link flapping"** (oscilación del enlace).
✅ El término **"jitter"** debe aparecer en el análisis de las videollamadas.
✅ La solución debe incluir: reemplazar el cable Ethernet, reconectar el puerto del switch (habilitar el puerto desde `err-disabled`).
✅ La acción preventiva debe mencionar la verificación de cables tras cualquier intervención física.

---

## Validación y Prueba Final

Una vez completados todos los pasos, verifica que has logrado los siguientes resultados:

### Lista de verificación de la práctica completa

| # | Criterio de validación                                                              | ✅/❌ |
|---|-------------------------------------------------------------------------------------|------|
| 1 | Ejecuté `ping` en secuencia (loopback → gateway → IP externa → nombre de dominio)  |      |
| 2 | Registré y analicé la salida de `ipconfig /all` o `ifconfig -a` de mi equipo       |      |
| 3 | Ejecuté `tracert`/`traceroute` e identifiqué al menos 3 saltos en la ruta          |      |
| 4 | Consulté registros A y MX con `nslookup` usando al menos 2 servidores DNS          |      |
| 5 | Identifiqué al menos 3 puertos en escucha con `netstat`                             |      |
| 6 | Completé el Ticket de Incidente del Escenario 1 (DNS) con causa raíz correcta      |      |
| 7 | Completé el Ticket de Incidente del Escenario 2 (Gateway) con capa OSI correcta    |      |
| 8 | Completé el Ticket de Incidente del Escenario 3 (Pérdida de paquetes) con análisis de log |  |
| 9 | Identifiqué el fenómeno de "link flapping" en el log del Escenario 3               |      |
|10 | Relacioné el jitter elevado con el impacto en aplicaciones en tiempo real          |      |

### Preguntas de reflexión final

Responde brevemente en tu hoja de trabajo:

1. **Metodología:** ¿Por qué es importante seguir el orden *bottom-up* (de Capa 1 hacia arriba) en lugar de empezar por la capa de aplicación cuando se diagnostica un problema de red?

2. **Correlación de herramientas:** En el Escenario 3, ningún comando individual por sí solo hubiera revelado la causa raíz. ¿Qué combinación de herramientas fue necesaria y por qué?

3. **Valor de los logs:** ¿Qué información crítica aportó el log del switch que los comandos ejecutados en el equipo del usuario no podían revelar directamente?

---

## Solución de Problemas Comunes

### Problema 1: `traceroute` no muestra saltos (solo `* * *` en todos los hops)

**Síntomas:**
```
$ traceroute 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max
 1  * * *
 2  * * *
 3  * * *
[todos los saltos muestran * * *]
```

**Causa:**
El firewall del equipo del estudiante, o el firewall del router doméstico, está bloqueando los paquetes UDP o ICMP que usa `traceroute`. En Windows, `tracert` usa ICMP por defecto; en Linux/macOS, `traceroute` usa UDP por defecto. Muchos firewalls bloquean estos protocolos.

**Solución:**

En Linux/macOS, prueba con el modo ICMP explícito:
```bash
# Usar ICMP en lugar de UDP (requiere sudo)
sudo traceroute -I 8.8.8.8

# Alternativa: usar TCP en puerto 80
sudo traceroute -T -p 80 8.8.8.8
```

En Windows, verifica que el Firewall de Windows no esté bloqueando ICMP saliente:
```cmd
REM Verificar reglas de firewall para ICMP
netsh advfirewall firewall show rule name="File and Printer Sharing (Echo Request - ICMPv4-In)"
```

Si el problema persiste, acepta que los `* * *` son esperables en algunos saltos intermedios y continúa el análisis con los saltos que sí responden.

---

### Problema 2: `nslookup` devuelve error `SERVFAIL` o `REFUSED` con el DNS configurado

**Síntomas:**
```
C:\> nslookup google.com
Servidor:  dns-local
Address:  192.168.1.1

*** 192.168.1.1 no puede encontrar google.com: Server failed
```
o
```
$ nslookup google.com
;; connection timed out; no servers could be reached
```

**Causa:**
El servidor DNS configurado en el equipo (generalmente el router doméstico o un servidor DNS interno) no está disponible, está sobrecargado, o tiene un fallo de reenvío (*forwarding*) hacia los servidores DNS raíz. También puede ocurrir si el servicio DNS del router está caído o si hay un problema de configuración en el servidor DNS local.

**Solución:**

Paso 1 — Verificar conectividad con el servidor DNS:
```cmd
ping 192.168.1.1 -n 4
```

Paso 2 — Si el DNS local no responde, cambiar temporalmente a un DNS público:

**Windows (temporal, solo para la sesión de diagnóstico):**
```cmd
REM Consultar con DNS de Google directamente
nslookup google.com 8.8.8.8

REM Para cambio permanente, ir a Configuración de red > Propiedades del adaptador
```

**Linux (temporal):**
```bash
# Agregar DNS alternativo al archivo resolv.conf (temporal, se pierde al reiniciar)
echo "nameserver 8.8.8.8" | sudo tee -a /etc/resolv.conf

# Verificar
nslookup google.com
```

Paso 3 — Vaciar la caché DNS en Windows si el problema era transitorio:
```cmd
ipconfig /flushdns
```

---

## Limpieza del Entorno

Al finalizar la práctica, realiza los siguientes pasos de limpieza:

1. **Cierra todas las ventanas de terminal** que hayas abierto durante la práctica.

2. **En Windows**, si ejecutaste `ipconfig /release` durante las pruebas, renueva la dirección IP:
   ```cmd
   ipconfig /renew
   ```

3. **En Linux**, si modificaste temporalmente `/etc/resolv.conf`, revierte el cambio:
   ```bash
   # Verificar el contenido actual
   cat /etc/resolv.conf
   # Si agregaste líneas manuales, editar y eliminarlas
   sudo nano /etc/resolv.conf
   ```

4. **Guarda tus tickets de incidente** en un archivo con nombre `Practica6_Tickets_[TuNombre].docx` (o `.txt` / `.md`).

5. **Guarda tu hoja de trabajo** con todas las tablas completadas y respuestas a las preguntas de análisis.

6. **Verifica que tu conexión a internet** siga funcionando correctamente ejecutando:
   ```cmd
   ping 8.8.8.8 -n 4
   ```

> No es necesario desinstalar ni desconfigurar ningún software. Todos los comandos ejecutados son de solo lectura y no modifican la configuración del sistema (excepto los comandos `release`/`renew` de DHCP, que se autoresuelven).

---

## Resumen

En esta práctica aplicaste los comandos de diagnóstico de red más utilizados en el trabajo diario de un técnico o administrador de redes. Ejecutaste `ping` con metodología progresiva por capas, inspeccionaste la configuración completa de tu interfaz con `ipconfig`/`ifconfig`, trazaste rutas con `tracert`/`traceroute`, consultaste el DNS con `nslookup` y revisaste conexiones activas con `netstat`.

En la Fase 2 aplicaste estos conocimientos a tres escenarios reales de incidentes: un fallo de servidor DNS interno, un gateway inalcanzable por problema físico en el router, y pérdida de paquetes intermitente causada por un cable defectuoso — diagnosticada mediante la correlación de métricas de `ping`, contadores de `ifconfig` y logs del switch. Para cada escenario documentaste el proceso en un ticket de incidente estructurado, practicando la habilidad de comunicar hallazgos técnicos de forma clara y trazable.

### Conceptos clave consolidados

| Concepto                    | Herramienta principal        | Capa OSI relacionada        |
|-----------------------------|------------------------------|-----------------------------|
| Conectividad básica (RTT, pérdida) | `ping`              | Capa 3 (Red)                |
| Configuración de interfaz   | `ipconfig` / `ifconfig`      | Capas 2–3                   |
| Ruta y latencia por saltos  | `tracert` / `traceroute`     | Capa 3 (Red)                |
| Resolución de nombres       | `nslookup`                   | Capa 7 (Aplicación / DNS)   |
| Conexiones y puertos activos| `netstat`                    | Capas 4–7                   |
| Eventos del dispositivo     | Logs syslog                  | Capa 1–2 (según el evento)  |
| Link flapping               | Logs del switch              | Capa 1 (Física)             |
| Jitter                      | `ping` (mdev)                | Capa 3–4                    |

### Recursos de referencia adicionales

- **RFC 792** — Especificación oficial del protocolo ICMP (base de `ping`): [https://www.rfc-editor.org/rfc/rfc792](https://www.rfc-editor.org/rfc/rfc792)
- **Cisco IOS Syslog Messages Guide** — Referencia para interpretar mensajes de log como los del Escenario 3: [https://www.cisco.com/c/en/us/td/docs/ios/system/messages/guide/sm_guide.html](https://www.cisco.com/c/en/us/td/docs/ios/system/messages/guide/sm_guide.html)
- **Man page de `traceroute`** (Linux): Ejecuta `man traceroute` en tu terminal para ver todas las opciones disponibles.
- **ipcalc** — Calculadora de subredes en línea útil para verificar máscaras y rangos: [http://jodies.de/ipcalc](http://jodies.de/ipcalc)

---
