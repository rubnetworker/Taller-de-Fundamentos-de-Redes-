# Práctica 2 — Cálculo de Subredes y Asignación de IPs

## 1. Metadatos

| Campo            | Detalle                          |
|------------------|----------------------------------|
| **Duración**     | 34 minutos                       |
| **Complejidad**  | Media                            |
| **Nivel Bloom**  | Aplicar (*Apply*)                |
| **Módulo**       | 2 — Direccionamiento IPv4 e IPv6 |
| **Práctica**     | 02-00-01                         |

---

## 2. Descripción General

En esta práctica aplicarás los conceptos de estructura de direcciones IPv4 estudiados en la Lección 2.1 para dividir un bloque de direcciones de Clase C en subredes de tamaño variable utilizando la técnica VLSM (*Variable Length Subnet Masking*). Calcularás manualmente las propiedades de cada subred —dirección de red, máscara, rango de hosts, broadcast y total de hosts utilizables— y validarás tus resultados con la herramienta `ipcalc` o una calculadora web equivalente. En la segunda parte analizarás una dirección IPv6 de ejemplo y la compararás con su contraparte IPv4, identificando diferencias clave entre ambas versiones del protocolo.

---

## 3. Objetivos de Aprendizaje

- [ ] Aplicar el método VLSM para dividir el bloque `192.168.10.0/24` en cuatro subredes con requerimientos de hosts distintos.
- [ ] Calcular para cada subred: dirección de red, máscara (decimal y CIDR), primer host válido, último host válido, dirección de broadcast y número de hosts utilizables.
- [ ] Completar una tabla de asignación de IPs para dispositivos concretos (router, switches, servidores y hosts) dentro del esquema diseñado.
- [ ] Identificar la estructura de una dirección IPv6 (prefijo de red e ID de interfaz) y describir al menos tres diferencias fundamentales respecto a IPv4.
- [ ] Validar los cálculos manuales utilizando la herramienta `ipcalc` o una calculadora de subredes web.

---

## 4. Prerrequisitos

### Conocimientos previos
- Comprensión de la estructura de 32 bits de una dirección IPv4 y su representación en notación decimal punteada (Lección 2.1).
- Conocimiento de las clases de direcciones IPv4 (A, B, C) y los rangos privados definidos en RFC 1918.
- Capacidad de convertir números decimales a binario y viceversa (al menos para valores de 0 a 255).
- Comprensión conceptual de qué es una máscara de subred y para qué sirve.

### Acceso requerido
- Terminal del sistema operativo (Windows CMD/PowerShell, macOS Terminal o Linux Bash).
- Navegador web moderno para acceder a una calculadora de subredes en línea **o** herramienta `ipcalc` instalada localmente.
- Editor de texto o procesador de documentos para registrar los resultados.

> **Nota:** No se requiere Cisco Packet Tracer en esta práctica. Los cálculos son manuales y se validan con herramientas de línea de comandos o web.

---

## 5. Entorno de Laboratorio

### Hardware requerido

| Componente       | Especificación mínima                  |
|------------------|----------------------------------------|
| Computadora      | Procesador 2 GHz, 4 GB RAM             |
| Conectividad     | Acceso a internet (para herramienta web) |
| Almacenamiento   | 50 MB libres para archivos de práctica  |

### Software requerido

| Herramienta                    | Versión / Fuente                                             | Uso en la práctica              |
|-------------------------------|--------------------------------------------------------------|---------------------------------|
| Terminal del SO               | Nativa (CMD, PowerShell, Bash, zsh)                         | Ejecutar `ipcalc` (Linux/macOS) |
| Calculadora de subredes web   | [subnet-calculator.com](https://www.subnet-calculator.com) o [cidr.xyz](https://cidr.xyz) | Validación de cálculos          |
| Editor de texto               | Notepad++, VS Code, LibreOffice Writer, Word                | Registro de resultados          |
| Navegador web                 | Chrome 110+, Firefox 110+, Edge 110+                        | Acceso a calculadora web        |

### Preparación del entorno

#### Opción A — Linux / macOS: verificar `ipcalc`

```bash
# Verificar si ipcalc está disponible
ipcalc --version

# Si no está instalado en Ubuntu/Debian:
sudo apt-get install ipcalc -y

# Si no está instalado en macOS con Homebrew:
brew install ipcalc
```

#### Opción B — Windows: usar calculadora web

Si utilizas Windows y no tienes `ipcalc`, abre tu navegador y navega a:

```
https://cidr.xyz
```

> Mantén esta pestaña abierta durante toda la práctica para validar tus cálculos.

#### Verificación rápida del entorno (todos los SO)

```bash
# Consulta tu dirección IP actual para confirmar que entiendes la salida
# Windows:
ipconfig

# macOS / Linux:
ip addr show
# o bien:
ifconfig
```

Identifica en la salida: tu dirección IPv4, la máscara de subred y la puerta de enlace. Esto confirma que comprendes el contexto antes de comenzar los cálculos.

---

## 6. Procedimiento Paso a Paso

---

### Parte A — Cálculo Manual de Subredes VLSM

---

### Paso A-1: Comprender el Escenario de Red

**Objetivo:** Leer e interpretar los requerimientos del escenario antes de calcular.

#### Instrucciones

1. Lee cuidadosamente el siguiente escenario empresarial:

---

**Escenario:** La empresa *TechPyme S.A.* tiene su sede central con cuatro segmentos de red diferenciados. El administrador de red ha recibido el bloque `192.168.10.0/24` y debe dividirlo en subredes usando VLSM, respetando los siguientes requerimientos:

| Segmento | Descripción                   | Hosts requeridos |
|----------|-------------------------------|-----------------|
| S1       | Departamento de Ventas        | 60 hosts        |
| S2       | Departamento de Administración| 28 hosts        |
| S3       | Servidores internos           | 12 hosts        |
| S4       | Enlace punto a punto (WAN)    | 2 hosts         |

---

2. En tu editor de texto, crea un archivo llamado `practica2_subredes.txt` y copia la tabla anterior.

3. Responde las siguientes preguntas antes de continuar:
   - ¿A qué clase pertenece `192.168.10.0`? ¿Es pública o privada?
   - ¿Cuántos hosts totales tiene el bloque `/24` sin subdividir?
   - ¿Por qué se usa VLSM en lugar de dividir el bloque en subredes iguales?

**Salida esperada (respuestas):**

```
Clase: C (primer octeto 192, rango 192–223)
Tipo: Privada (rango RFC 1918: 192.168.0.0/16)
Hosts totales en /24: 2^8 - 2 = 254 hosts utilizables
Razón de VLSM: los segmentos tienen tamaños muy distintos (60, 28, 12, 2);
               usar subredes iguales desperdiciaría direcciones.
```

**Verificación:** Confirma que identificaste correctamente la clase y el tipo de dirección antes de avanzar. Si tienes dudas, revisa la tabla de clases de la Lección 2.1.

---

### Paso A-2: Ordenar los Segmentos de Mayor a Menor

**Objetivo:** Aplicar la regla VLSM de asignación en orden descendente para minimizar el desperdicio de direcciones.

#### Instrucciones

1. **Regla VLSM fundamental:** siempre se asigna primero la subred más grande, luego la siguiente, y así sucesivamente. Esto garantiza que las subredes grandes queden alineadas en los bloques correctos.

2. Ordena los segmentos de mayor a menor número de hosts requeridos:

```
Orden de asignación VLSM:
1°  S1 — Ventas          → 60 hosts requeridos
2°  S2 — Administración  → 28 hosts requeridos
3°  S3 — Servidores      → 12 hosts requeridos
4°  S4 — Enlace WAN      →  2 hosts requeridos
```

3. Para cada segmento, determina la **potencia de 2 mínima** que cubra los hosts requeridos **más 2** (dirección de red + broadcast):

```
S1: necesita 60 hosts → 60 + 2 = 62 → 2^6 = 64 ≥ 62 ✓  → prefijo /26
S2: necesita 28 hosts → 28 + 2 = 30 → 2^5 = 32 ≥ 30 ✓  → prefijo /27
S3: necesita 12 hosts → 12 + 2 = 14 → 2^4 = 16 ≥ 14 ✓  → prefijo /28
S4: necesita  2 hosts →  2 + 2 =  4 → 2^2 =  4 ≥  4 ✓  → prefijo /30
```

4. Registra estos prefijos en tu archivo `practica2_subredes.txt`.

**Salida esperada:**

```
S1 → /26  (64 direcciones totales, 62 hosts utilizables)
S2 → /27  (32 direcciones totales, 30 hosts utilizables)
S3 → /28  (16 direcciones totales, 14 hosts utilizables)
S4 → /30  ( 4 direcciones totales,  2 hosts utilizables)
```

**Verificación:** La suma de todas las direcciones (64 + 32 + 16 + 4 = 116) debe ser menor que 256 (tamaño del bloque /24). Si la suma supera 256, hay un error en los prefijos seleccionados.

---

### Paso A-3: Calcular las Propiedades de S1 (Ventas — /26)

**Objetivo:** Calcular manualmente todas las propiedades de la primera subred.

#### Instrucciones

1. **Dirección de red de S1:** comenzamos desde el inicio del bloque asignado.

```
Dirección de red S1: 192.168.10.0/26
```

2. Convierte la máscara `/26` a notación decimal. Los primeros 26 bits son `1`, los últimos 6 son `0`:

```
Binario:  11111111.11111111.11111111.11000000
Decimal:  255      .255     .255     .192

Máscara S1: 255.255.255.192
```

3. Calcula el **tamaño del bloque** (número de direcciones): `2^(32-26) = 2^6 = 64`

4. Calcula las propiedades:

```
Dirección de red:    192.168.10.0
Primer host válido:  192.168.10.1    (dirección de red + 1)
Último host válido:  192.168.10.62   (broadcast - 1)
Dirección broadcast: 192.168.10.63   (dirección de red + 64 - 1)
Hosts utilizables:   64 - 2 = 62
Prefijo CIDR:        /26
Máscara decimal:     255.255.255.192
```

5. Verifica el cálculo del broadcast en binario:

```
Red:       192.168.10. 00|000000   (los 6 bits de host = 0)
Broadcast: 192.168.10. 00|111111   (los 6 bits de host = 1) = .63
```

**Salida esperada:**

```
S1 — Ventas (192.168.10.0/26)
├── Red:        192.168.10.0
├── Máscara:    255.255.255.192 (/26)
├── 1er host:   192.168.10.1
├── Últ. host:  192.168.10.62
├── Broadcast:  192.168.10.63
└── Hosts útiles: 62
```

**Verificación:** El broadcast de S1 es `.63`. La siguiente subred comenzará en `.64`. Anota este dato.

---

### Paso A-4: Calcular las Propiedades de S2 (Administración — /27)

**Objetivo:** Calcular la segunda subred, comenzando donde terminó la anterior.

#### Instrucciones

1. S2 comienza inmediatamente después del broadcast de S1:

```
Dirección de red S2: 192.168.10.64/27
```

2. Convierte la máscara `/27` a decimal (27 bits en `1`, 5 en `0`):

```
Binario:  11111111.11111111.11111111.11100000
Decimal:  255      .255     .255     .224

Máscara S2: 255.255.255.224
```

3. Tamaño del bloque: `2^(32-27) = 2^5 = 32`

4. Calcula las propiedades:

```
Dirección de red:    192.168.10.64
Primer host válido:  192.168.10.65
Último host válido:  192.168.10.94   (64 + 32 - 2 = 94)
Dirección broadcast: 192.168.10.95   (64 + 32 - 1 = 95)
Hosts utilizables:   32 - 2 = 30
Prefijo CIDR:        /27
Máscara decimal:     255.255.255.224
```

5. Verifica en binario el cuarto octeto:

```
Red:       192.168.10. 010|00000  = 64
Broadcast: 192.168.10. 010|11111  = 95
```

**Salida esperada:**

```
S2 — Administración (192.168.10.64/27)
├── Red:        192.168.10.64
├── Máscara:    255.255.255.224 (/27)
├── 1er host:   192.168.10.65
├── Últ. host:  192.168.10.94
├── Broadcast:  192.168.10.95
└── Hosts útiles: 30
```

**Verificación:** El broadcast de S2 es `.95`. La siguiente subred comienza en `.96`.

---

### Paso A-5: Calcular las Propiedades de S3 (Servidores — /28)

**Objetivo:** Calcular la tercera subred.

#### Instrucciones

1. S3 comienza en `.96`:

```
Dirección de red S3: 192.168.10.96/28
```

2. Convierte `/28` a decimal (28 bits en `1`, 4 en `0`):

```
Binario:  11111111.11111111.11111111.11110000
Decimal:  255      .255     .255     .240

Máscara S3: 255.255.255.240
```

3. Tamaño del bloque: `2^(32-28) = 2^4 = 16`

4. Propiedades:

```
Dirección de red:    192.168.10.96
Primer host válido:  192.168.10.97
Último host válido:  192.168.10.110   (96 + 16 - 2 = 110)
Dirección broadcast: 192.168.10.111   (96 + 16 - 1 = 111)
Hosts utilizables:   16 - 2 = 14
Prefijo CIDR:        /28
Máscara decimal:     255.255.255.240
```

5. Verifica en binario:

```
Red:       192.168.10. 0110|0000  = 96
Broadcast: 192.168.10. 0110|1111  = 111
```

**Salida esperada:**

```
S3 — Servidores (192.168.10.96/28)
├── Red:        192.168.10.96
├── Máscara:    255.255.255.240 (/28)
├── 1er host:   192.168.10.97
├── Últ. host:  192.168.10.110
├── Broadcast:  192.168.10.111
└── Hosts útiles: 14
```

**Verificación:** El broadcast de S3 es `.111`. La siguiente subred comienza en `.112`.

---

### Paso A-6: Calcular las Propiedades de S4 (Enlace WAN — /30)

**Objetivo:** Calcular la subred de enlace punto a punto.

#### Instrucciones

1. S4 comienza en `.112`:

```
Dirección de red S4: 192.168.10.112/30
```

2. Convierte `/30` a decimal (30 bits en `1`, 2 en `0`):

```
Binario:  11111111.11111111.11111111.11111100
Decimal:  255      .255     .255     .252

Máscara S4: 255.255.255.252
```

3. Tamaño del bloque: `2^(32-30) = 2^2 = 4`

4. Propiedades:

```
Dirección de red:    192.168.10.112
Primer host válido:  192.168.10.113  (un extremo del enlace WAN)
Último host válido:  192.168.10.114  (otro extremo del enlace WAN)
Dirección broadcast: 192.168.10.115
Hosts utilizables:   4 - 2 = 2
Prefijo CIDR:        /30
Máscara decimal:     255.255.255.252
```

5. Verifica en binario:

```
Red:       192.168.10. 011100|00  = 112
Broadcast: 192.168.10. 011100|11  = 115
```

**Salida esperada:**

```
S4 — Enlace WAN (192.168.10.112/30)
├── Red:        192.168.10.112
├── Máscara:    255.255.255.252 (/30)
├── 1er host:   192.168.10.113
├── Últ. host:  192.168.10.114
├── Broadcast:  192.168.10.115
└── Hosts útiles: 2
```

**Verificación:** La siguiente dirección disponible sería `.116`. Las direcciones `.116` hasta `.255` quedan sin asignar (disponibles para expansión futura).

---

### Paso A-7: Consolidar la Tabla de Subredes

**Objetivo:** Reunir todos los cálculos en una tabla resumen completa.

#### Instrucciones

1. En tu archivo `practica2_subredes.txt`, construye la siguiente tabla con todos los resultados:

```
╔══════╦══════════════════╦══════════════════╦══════════════╦════════════════╦══════════════════╦══════════════════╦═════════╗
║ Seg. ║ Descripción      ║ Red              ║ Máscara      ║ CIDR ║ 1er Host         ║ Últ. Host        ║ Broadcast        ║ Hosts ║
╠══════╬══════════════════╬══════════════════╬══════════════╬══════╬══════════════════╬══════════════════╬══════════════════╬═══════╣
║  S1  ║ Ventas           ║ 192.168.10.0     ║ 255.255.255.192 ║ /26 ║ 192.168.10.1  ║ 192.168.10.62  ║ 192.168.10.63  ║  62   ║
║  S2  ║ Administración   ║ 192.168.10.64    ║ 255.255.255.224 ║ /27 ║ 192.168.10.65 ║ 192.168.10.94  ║ 192.168.10.95  ║  30   ║
║  S3  ║ Servidores       ║ 192.168.10.96    ║ 255.255.255.240 ║ /28 ║ 192.168.10.97 ║ 192.168.10.110 ║ 192.168.10.111 ║  14   ║
║  S4  ║ Enlace WAN       ║ 192.168.10.112   ║ 255.255.255.252 ║ /30 ║ 192.168.10.113║ 192.168.10.114 ║ 192.168.10.115 ║   2   ║
╚══════╩══════════════════╩══════════════════╩══════════════╩══════╩══════════════════╩══════════════════╩══════════════════╩═════════╝
```

2. Calcula el **total de hosts utilizables** asignados: `62 + 30 + 14 + 2 = 108`

3. Calcula el **espacio no asignado**: `254 - 108 = 146 hosts` (direcciones `.116` a `.255` disponibles para expansión).

**Verificación:** La suma de todos los bloques en número de direcciones totales (incluyendo red y broadcast) debe ser: `64 + 32 + 16 + 4 = 116 direcciones`, lo que equivale al espacio `.0` a `.115`. Confirma que ningún rango se superpone con otro.

---

### Parte B — Validación con Herramienta ipcalc o Calculadora Web

---

### Paso B-1: Validar los Cálculos con `ipcalc` (Linux/macOS)

**Objetivo:** Usar la herramienta `ipcalc` para confirmar que los cálculos manuales son correctos.

#### Instrucciones

1. Abre tu terminal y ejecuta `ipcalc` para cada subred. Observa y compara la salida con tus cálculos manuales:

```bash
# Validar S1 — Ventas
ipcalc 192.168.10.0/26
```

**Salida esperada de `ipcalc`:**

```
Address:   192.168.10.0         11000000.10101000.00001010. 00000000
Netmask:   255.255.255.192 = 26 11111111.11111111.11111111. 11000000
Wildcard:  0.0.0.63             00000000.00000000.00000000. 00111111
=>
Network:   192.168.10.0/26      11000000.10101000.00001010. 00000000
HostMin:   192.168.10.1         11000000.10101000.00001010. 00000001
HostMax:   192.168.10.62        11000000.10101000.00001010. 00111110
Broadcast: 192.168.10.63        11000000.10101000.00001010. 00111111
Hosts/Net: 62                    Class C, Private Internet
```

2. Repite el proceso para las demás subredes:

```bash
# Validar S2 — Administración
ipcalc 192.168.10.64/27

# Validar S3 — Servidores
ipcalc 192.168.10.96/28

# Validar S4 — Enlace WAN
ipcalc 192.168.10.112/30
```

3. Para cada subred, verifica que los valores de `Network`, `HostMin`, `HostMax`, `Broadcast` y `Hosts/Net` coincidan exactamente con tu tabla manual.

**Verificación:** Si algún valor difiere, revisa el cálculo manual correspondiente. Los errores más comunes son en el último host (confundir con el broadcast) o en el inicio de la siguiente subred.

---

### Paso B-2: Validar con Calculadora Web (Windows u Opción Alternativa)

**Objetivo:** Usar una herramienta web para validar los cálculos en entornos Windows o sin `ipcalc`.

#### Instrucciones

1. Abre tu navegador y ve a: `https://cidr.xyz`

2. En el campo de entrada, escribe cada una de las subredes en notación CIDR:

```
192.168.10.0/26
192.168.10.64/27
192.168.10.96/28
192.168.10.112/30
```

3. Para cada entrada, la herramienta mostrará:
   - **Network address** → debe coincidir con tu "Dirección de red"
   - **Broadcast address** → debe coincidir con tu "Broadcast"
   - **First host** → debe coincidir con tu "Primer host válido"
   - **Last host** → debe coincidir con tu "Último host válido"
   - **Number of hosts** → debe coincidir con tu "Hosts utilizables"

4. Toma una captura de pantalla de los resultados de al menos dos subredes y guárdalas como `validacion_S1.png` y `validacion_S2.png`.

**Salida esperada (para 192.168.10.64/27 en cidr.xyz):**

```
Network address:   192.168.10.64
Broadcast address: 192.168.10.95
First host:        192.168.10.65
Last host:         192.168.10.94
Subnet mask:       255.255.255.224
Number of hosts:   30
```

**Verificación:** Todos los valores deben coincidir con tu tabla manual. Si hay discrepancia, el cálculo manual tiene un error que debes corregir antes de continuar.

---

### Parte C — Tabla de Asignación de IPs para Dispositivos

---

### Paso C-1: Asignar Direcciones IP a Dispositivos Específicos

**Objetivo:** Diseñar la asignación de IPs para los dispositivos de la red de *TechPyme S.A.*, justificando las decisiones.

#### Instrucciones

1. Usando las subredes calculadas, asigna direcciones IP a los siguientes dispositivos según las convenciones estándar:

   - **Convención recomendada:** routers y gateways usan el **primer host válido** de la subred; servidores usan los primeros hosts disponibles después del gateway; los hosts de usuarios usan las últimas IPs del rango.

2. Completa la siguiente tabla de asignación en tu archivo de resultados:

```
TABLA DE ASIGNACIÓN DE DIRECCIONES IP — TechPyme S.A.
Bloque base: 192.168.10.0/24

┌─────────────────────────────────────────────────────────────────────────────┐
│ SUBRED S1 — Ventas (192.168.10.0/26)                                        │
├──────────────────────────┬─────────────────┬────────────────────────────────┤
│ Dispositivo              │ IP Asignada     │ Justificación                  │
├──────────────────────────┼─────────────────┼────────────────────────────────┤
│ Router (interfaz Ventas) │ 192.168.10.1    │ Primer host = gateway          │
│ Switch de Ventas         │ 192.168.10.2    │ Segundo host = infraestructura │
│ PC-Ventas-01             │ 192.168.10.10   │ Rango usuarios (10–62)         │
│ PC-Ventas-02             │ 192.168.10.11   │ Rango usuarios                 │
│ PC-Ventas-03             │ 192.168.10.12   │ Rango usuarios                 │
└──────────────────────────┴─────────────────┴────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│ SUBRED S2 — Administración (192.168.10.64/27)                               │
├──────────────────────────┬─────────────────┬────────────────────────────────┤
│ Dispositivo              │ IP Asignada     │ Justificación                  │
├──────────────────────────┼─────────────────┼────────────────────────────────┤
│ Router (interfaz Admin)  │ 192.168.10.65   │ Primer host = gateway          │
│ Switch de Administración │ 192.168.10.66   │ Segundo host = infraestructura │
│ PC-Admin-01              │ 192.168.10.70   │ Rango usuarios (70–94)         │
│ PC-Admin-02              │ 192.168.10.71   │ Rango usuarios                 │
└──────────────────────────┴─────────────────┴────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│ SUBRED S3 — Servidores (192.168.10.96/28)                                   │
├──────────────────────────┬─────────────────┬────────────────────────────────┤
│ Dispositivo              │ IP Asignada     │ Justificación                  │
├──────────────────────────┼─────────────────┼────────────────────────────────┤
│ Router (interfaz Servers)│ 192.168.10.97   │ Primer host = gateway          │
│ Servidor Web             │ 192.168.10.98   │ Servidor crítico, IP fija baja │
│ Servidor DNS             │ 192.168.10.99   │ Servidor crítico, IP fija baja │
│ Servidor de Archivos     │ 192.168.10.100  │ Servidor, IP fija              │
└──────────────────────────┴─────────────────┴────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│ SUBRED S4 — Enlace WAN (192.168.10.112/30)                                  │
├──────────────────────────┬─────────────────┬────────────────────────────────┤
│ Dispositivo              │ IP Asignada     │ Justificación                  │
├──────────────────────────┼─────────────────┼────────────────────────────────┤
│ Router LAN (interfaz WAN)│ 192.168.10.113  │ Extremo local del enlace P2P   │
│ Router ISP (interfaz LAN)│ 192.168.10.114  │ Extremo remoto del enlace P2P  │
└──────────────────────────┴─────────────────┴────────────────────────────────┘
```

3. Responde en tu documento: ¿Por qué no se asigna la dirección `.0` de cada subred a ningún dispositivo? ¿Y la última dirección (broadcast)?

**Respuesta esperada:**

```
La dirección .0 de cada subred es la DIRECCIÓN DE RED (Network Address):
identifica a la subred en sí misma y no puede asignarse a un host.

La última dirección es la DIRECCIÓN DE BROADCAST: se usa para enviar
paquetes a TODOS los hosts de la subred simultáneamente y tampoco puede
asignarse a un dispositivo individual.
```

**Verificación:** Confirma que ninguna IP asignada sea igual a la dirección de red ni al broadcast de su subred respectiva, y que todas las IPs estén dentro del rango válido de su segmento.

---

### Parte D — Análisis de IPv6 y Comparación con IPv4

---

### Paso D-1: Analizar la Estructura de una Dirección IPv6

**Objetivo:** Identificar los componentes de una dirección IPv6 y compararlos con IPv4.

#### Instrucciones

1. Analiza la siguiente dirección IPv6 de ejemplo:

```
2001:0db8:85a3:0000:0000:8a2e:0370:7334/64
```

2. Identifica y registra los siguientes componentes:

```
Dirección IPv6 completa: 2001:0db8:85a3:0000:0000:8a2e:0370:7334
Longitud total:          128 bits
Notación:                Hexadecimal, separada por ":" en 8 grupos de 16 bits

Estructura con prefijo /64:
├── Prefijo de red (primeros 64 bits):  2001:0db8:85a3:0000
│   ├── Prefijo global de ruteo:        2001:0db8:85a3  (primeros 48 bits)
│   └── ID de subred:                   0000            (bits 49–64)
└── ID de interfaz (últimos 64 bits):   0000:8a2e:0370:7334
```

3. Aplica las reglas de **compresión de IPv6** para escribir la dirección en formato abreviado:
   - Regla 1: Eliminar ceros a la izquierda de cada grupo.
   - Regla 2: Reemplazar uno o más grupos de ceros consecutivos con `::` (solo una vez).

```bash
# Forma completa:
2001:0db8:85a3:0000:0000:8a2e:0370:7334

# Paso 1 — Eliminar ceros a la izquierda:
2001:db8:85a3:0:0:8a2e:370:7334

# Paso 2 — Comprimir grupos de ceros consecutivos:
2001:db8:85a3::8a2e:370:7334
```

4. Registra la forma comprimida en tu documento: `2001:db8:85a3::8a2e:370:7334/64`

**Salida esperada:**

```
Dirección IPv6 comprimida: 2001:db8:85a3::8a2e:370:7334/64
Prefijo de red:            2001:db8:85a3::/64
ID de interfaz:            ::8a2e:370:7334
```

**Verificación:** Expande mentalmente la dirección comprimida para confirmar que recuperas los 128 bits originales. El `::` representa exactamente los grupos `0000:0000` que fueron omitidos.

---

### Paso D-2: Comparar IPv4 e IPv6 — Diferencias Fundamentales

**Objetivo:** Articular al menos tres diferencias clave entre IPv4 e IPv6.

#### Instrucciones

1. Completa la siguiente tabla comparativa en tu documento de resultados:

```
TABLA COMPARATIVA: IPv4 vs IPv6
════════════════════════════════════════════════════════════════════════════════
Característica          │ IPv4                          │ IPv6
────────────────────────┼───────────────────────────────┼───────────────────────
Longitud de dirección   │ 32 bits                       │ 128 bits
Notación                │ Decimal punteada (4 octetos)  │ Hexadecimal con ":"
                        │ Ej: 192.168.10.1              │ (8 grupos de 16 bits)
                        │                               │ Ej: 2001:db8::1
────────────────────────┼───────────────────────────────┼───────────────────────
Espacio de direcciones  │ ~4.300 millones (2^32)        │ ~340 undecillones (2^128)
────────────────────────┼───────────────────────────────┼───────────────────────
Configuración automática│ Requiere DHCP o config. manual│ SLAAC (Stateless Address
                        │                               │ Autoconfiguration) nativo
────────────────────────┼───────────────────────────────┼───────────────────────
Broadcast               │ Sí (ej: 255.255.255.255)      │ No existe; usa multicast
                        │                               │ y anycast
────────────────────────┼───────────────────────────────┼───────────────────────
NAT requerido           │ Sí (direcciones privadas)     │ No necesario (suficientes
                        │                               │ IPs públicas para todos)
────────────────────────┼───────────────────────────────┼───────────────────────
Cabecera del paquete    │ Variable (20–60 bytes)        │ Fija (40 bytes), más
                        │                               │ eficiente para routers
────────────────────────┼───────────────────────────────┼───────────────────────
Seguridad integrada     │ IPSec opcional                │ IPSec obligatorio (nativo)
════════════════════════════════════════════════════════════════════════════════
```

2. Responde en tu documento: ¿Por qué el agotamiento de IPv4 fue un problema que IPv6 resuelve? Explica con referencia a los rangos privados RFC 1918 que estudiaste en la Lección 2.1.

**Respuesta esperada:**

```
El espacio de 32 bits de IPv4 ofrece solo ~4.300 millones de direcciones.
Con el crecimiento de internet, dispositivos IoT y móviles, este espacio
se agotó. La solución temporal fue usar rangos privados (RFC 1918) con NAT,
pero esto añade complejidad y limita conectividad extremo a extremo.

IPv6 con 128 bits ofrece 2^128 ≈ 3.4 × 10^38 direcciones, suficientes para
asignar una IP pública única a cada dispositivo en el planeta sin necesidad
de NAT, restaurando la conectividad directa extremo a extremo.
```

**Verificación:** Tu tabla debe incluir al menos **tres** diferencias claramente articuladas. Las más importantes para este nivel son: longitud de dirección, espacio de direccionamiento y eliminación del broadcast.

---

## 7. Validación y Pruebas Finales

Antes de dar por concluida la práctica, completa las siguientes verificaciones:

### Lista de verificación de resultados

```
CHECKLIST DE VALIDACIÓN — Práctica 2
════════════════════════════════════════════════════════════════════
[ ] 1. Las cuatro subredes VLSM están calculadas y no se superponen.
[ ] 2. La suma de bloques (64+32+16+4=116) no excede 256.
[ ] 3. Cada subred tiene su dirección de red, máscara (decimal y CIDR),
       primer host, último host, broadcast y total de hosts.
[ ] 4. Los cálculos manuales fueron validados con ipcalc o calculadora web.
[ ] 5. La tabla de asignación de IPs no contiene direcciones de red
       ni broadcast asignadas a dispositivos.
[ ] 6. La dirección IPv6 fue descomprimida y comprimida correctamente.
[ ] 7. La tabla comparativa IPv4 vs IPv6 tiene al menos 3 diferencias.
[ ] 8. El archivo practica2_subredes.txt está completo y guardado.
════════════════════════════════════════════════════════════════════
```

### Prueba de coherencia VLSM

Verifica que el diseño es coherente respondiendo estas preguntas en tu documento:

```
Pregunta 1: ¿A qué subred pertenece la IP 192.168.10.85?
  → Está en S2 (Administración): rango 192.168.10.65–192.168.10.94 ✓

Pregunta 2: ¿Es 192.168.10.63 un host válido en S1?
  → NO: es la dirección de broadcast de S1 ✗

Pregunta 3: ¿Puede 192.168.10.116 usarse como host en alguna subred diseñada?
  → NO: está fuera de las cuatro subredes asignadas (.116–.255 sin asignar)

Pregunta 4: ¿Cuántas subredes /30 adicionales podrían crearse en el espacio libre?
  → Espacio libre: .116 a .255 = 140 direcciones
  → Subredes /30 posibles: 140 / 4 = 35 subredes /30 adicionales
```

---

## 8. Resolución de Problemas

### Problema 1: El rango de una subred se superpone con la siguiente

**Síntoma:** Al calcular S2, el primer host resulta ser menor que el broadcast de S1, o los rangos se solapan al construir la tabla.

**Causa probable:** El estudiante no respetó la regla de iniciar cada subred inmediatamente después del broadcast de la anterior. Un error frecuente es calcular el inicio de la nueva subred sumando el número de hosts utilizables en lugar del tamaño total del bloque (que incluye red + broadcast).

**Solución:**

```
INCORRECTO:
  S1 termina en broadcast .63
  S2 inicia en .63 + 30 (hosts útiles) = .93  ← ERROR

CORRECTO:
  S1 tiene bloque de 64 direcciones (2^6)
  S1 inicia en .0, por lo tanto S2 inicia en .0 + 64 = .64

Regla: Nueva subred = Dirección de red anterior + Tamaño del bloque
       (no + número de hosts utilizables)
```

Recalcula usando siempre la fórmula: `Inicio siguiente subred = Inicio subred actual + 2^(32 - prefijo)`

---

### Problema 2: `ipcalc` produce resultados distintos a los cálculos manuales

**Síntoma:** Al ejecutar `ipcalc 192.168.10.64/27`, la herramienta muestra una dirección de red diferente a `192.168.10.64` (por ejemplo, muestra `192.168.10.64` pero el estudiante esperaba `192.168.10.65`).

**Causa probable:** El estudiante confundió la **dirección de red** (Network Address) con el **primer host válido** (HostMin). La herramienta `ipcalc` muestra correctamente ambos valores por separado. El error surge al ingresar la IP de un host en lugar de la dirección de red al validar.

**Solución:**

```bash
# INCORRECTO: ingresar la IP de un host para validar la subred
ipcalc 192.168.10.65/27
# Esto calcula correctamente, pero la "Network" mostrada sigue siendo .64
# porque ipcalc identifica a qué red pertenece esa IP

# CORRECTO: ingresar la dirección de red para validar
ipcalc 192.168.10.64/27

# Distinción clave en la salida de ipcalc:
# Network:  192.168.10.64/27   ← Dirección de red (NO asignable a hosts)
# HostMin:  192.168.10.65      ← Primer host válido
# HostMax:  192.168.10.94      ← Último host válido
# Broadcast:192.168.10.95      ← Broadcast (NO asignable a hosts)
```

Si usas calculadora web y obtienes resultados distintos, verifica que ingresaste la dirección en formato correcto (con `/` antes del prefijo, sin espacios).

---

## 9. Limpieza del Entorno

Esta práctica es predominantemente documental y no requiere deshacer configuraciones de red. Sin embargo, realiza las siguientes acciones al finalizar:

```bash
# 1. Guarda y cierra tu archivo de resultados
#    Nombre recomendado: practica2_subredes_[TuNombre].txt

# 2. Si usaste ipcalc en Linux/macOS, no hay nada que desinstalar
#    (es una herramienta de solo consulta, no modifica la configuración)

# 3. Cierra las pestañas del navegador con la calculadora web

# 4. Verifica que tu archivo tiene la siguiente estructura mínima:
#    - Tabla de subredes VLSM (4 subredes)
#    - Capturas de validación con ipcalc o calculadora web
#    - Tabla de asignación de IPs a dispositivos
#    - Análisis de dirección IPv6
#    - Tabla comparativa IPv4 vs IPv6
#    - Respuestas a las preguntas de reflexión
```

**Entregable final:** El archivo `practica2_subredes_[TuNombre].txt` (o `.docx` / `.pdf`) con todos los resultados, tablas y respuestas completadas.

---

## 10. Resumen

### Lo que aprendiste en esta práctica

En esta práctica aplicaste el método VLSM para dividir el bloque `192.168.10.0/24` (Clase C, privada según RFC 1918) en cuatro subredes de tamaño variable, respetando la regla de asignación de mayor a menor. Calculaste manualmente las propiedades fundamentales de cada subred —dirección de red, máscara en notación decimal y CIDR, primer y último host válido, broadcast y total de hosts utilizables— y validaste cada resultado con `ipcalc` o una calculadora web.

Diseñaste un esquema de asignación de IPs para dispositivos reales (router, switches, servidores y hosts), aplicando convenciones profesionales de numeración. Finalmente, analizaste la estructura de una dirección IPv6, practicaste su compresión y construiste una tabla comparativa que articula las diferencias fundamentales entre IPv4 e IPv6, conectando el problema del agotamiento de direcciones con la motivación para adoptar IPv6.

### Conceptos clave consolidados

| Concepto                  | Descripción breve                                                                 |
|---------------------------|-----------------------------------------------------------------------------------|
| **VLSM**                  | Técnica que permite usar máscaras de longitud variable para optimizar el espacio de IPs |
| **Prefijo CIDR**          | Notación `/n` que indica cuántos bits pertenecen a la red                         |
| **Dirección de red**      | Primera dirección del bloque; identifica la subred; no asignable a hosts          |
| **Broadcast**             | Última dirección del bloque; envía a todos los hosts; no asignable                |
| **Hosts utilizables**     | `2^(32-prefijo) - 2`                                                              |
| **RFC 1918**              | Define los rangos privados de IPv4 (10.x, 172.16–31.x, 192.168.x)               |
| **IPv6 /64**              | 64 bits de prefijo de red + 64 bits de ID de interfaz                            |
| **SLAAC**                 | Autoconfiguración de IPv6 sin necesidad de DHCP                                   |

### Recursos adicionales

- [RFC 1918 — Rangos de direcciones privadas (IETF)](https://www.rfc-editor.org/rfc/rfc1918)
- [RFC 4291 — Arquitectura de direccionamiento IPv6 (IETF)](https://www.rfc-editor.org/rfc/rfc4291)
- [Calculadora de subredes CIDR interactiva](https://cidr.xyz)
- [ipcalc — Documentación y uso](http://jodies.de/ipcalc)
- [Cisco Networking Academy — IPv4 Subnetting Practice](https://www.netacad.com)
- [Tanenbaum, A. & Wetherall, D. — Redes de computadoras, 5ª ed., Cap. 5 (Pearson)](https://www.pearson.com/en-us/subject-catalog/p/computer-networks/P200000003508)

---

> **Nota para el instructor:** Esta práctica no requiere Cisco Packet Tracer. Es completamente ejecutable con papel, calculadora y terminal. Los resultados numéricos son deterministas y verificables, lo que facilita la evaluación objetiva. Se recomienda revisar el archivo de entregable verificando especialmente que los rangos no se superpongan y que ninguna IP de la tabla de asignación corresponda a una dirección de red o broadcast.
