---LAB_START---
LAB_ID: 05-00-01
---MARKDOWN---
# Práctica 5 — Concientización y Prevención de Ingeniería Social

## Metadatos

| Campo            | Detalle                          |
|------------------|----------------------------------|
| **Duración**     | 44 minutos                       |
| **Complejidad**  | Fácil                            |
| **Nivel Bloom**  | Aplicar (Apply)                  |
| **Modalidad**    | Taller analítico / trabajo escrito |
| **Práctica #**   | 5 de 7                           |

---

## Descripción General

Esta práctica es un taller de análisis de casos y simulación de escenarios de ingeniería social. A diferencia de las demás prácticas del curso, **no requiere configuración de equipos de red ni simuladores**; el trabajo se centra en el análisis crítico, la identificación de señales de alerta y la elaboración de materiales de concientización. El estudiante analizará seis casos reales representativos de las técnicas más comunes de ingeniería social, diseñará un correo de concientización para usuarios finales y elaborará un conjunto de políticas de seguridad para una organización ficticia. Esta práctica refuerza el concepto de **defensa en profundidad** estudiado en la Lección 5.1, enfocándose en el eslabón más vulnerable de cualquier cadena de seguridad: el factor humano.

---

## Objetivos de Aprendizaje

Al finalizar esta práctica, el estudiante será capaz de:

- [ ] Identificar y clasificar correctamente las técnicas de ingeniería social (phishing, vishing, smishing, pretexting, baiting) mediante el análisis de casos concretos.
- [ ] Detectar las señales de alerta (*red flags*) presentes en correos electrónicos, mensajes y llamadas fraudulentas, vinculándolas con los principios de la tríada CIA.
- [ ] Aplicar estrategias de mitigación y buenas prácticas de seguridad (incluyendo MFA y políticas de verificación de identidad) para prevenir ataques de ingeniería social.
- [ ] Elaborar un correo de concientización dirigido a usuarios finales que comunique una amenaza específica y proporcione recomendaciones accionables.
- [ ] Construir un listado de políticas de seguridad organizacional básicas orientadas a reducir el riesgo de ingeniería social.

---

## Prerrequisitos

### Conocimientos previos

| Tema | Nivel requerido |
|------|----------------|
| Uso de correo electrónico y navegación web | Básico |
| Conceptos de la tríada CIA (Lección 5.1) | Completado |
| Amenazas comunes de red (Lección 5.1) | Completado |
| Lectura de temas 5.1 y 5.2 del módulo | Obligatorio |

### Acceso y herramientas

| Recurso | Requisito |
|---------|-----------|
| Procesador de textos (Word, LibreOffice Writer, VS Code, Notepad++) | Obligatorio |
| Navegador web moderno (Chrome 110+, Firefox 110+, Edge 110+) | Obligatorio |
| Cisco Packet Tracer | **No requerido** en esta práctica |
| Wireshark | **No requerido** en esta práctica |
| Conexión a internet | Recomendada (para consulta de referencias) |

> **Nota para el instructor:** Esta práctica no tiene dependencias de hardware ni de software especializado. Es ideal como punto de entrada para estudiantes con menos experiencia técnica o como práctica de recuperación. Los entregables son documentos escritos que el estudiante guardará en su computadora.

---

## Entorno de Laboratorio

### Configuración del espacio de trabajo

No se requiere configuración de red. El estudiante debe preparar su entorno de trabajo de la siguiente manera:

1. Abrir el procesador de textos de su preferencia.
2. Crear un nuevo documento con el nombre: `Practica5_Ingenieria_Social_[TuNombre].docx` (o `.odt`, `.txt` según el editor).
3. Configurar el documento con las siguientes secciones vacías (se irán completando durante la práctica):

```
PRÁCTICA 5 — CONCIENTIZACIÓN Y PREVENCIÓN DE INGENIERÍA SOCIAL
Nombre del estudiante: ___________________________
Fecha: ___________________________

PARTE 1: FICHAS DE ANÁLISIS DE CASOS (Casos 1 al 6)
PARTE 2: CORREO DE CONCIENTIZACIÓN
PARTE 3: AUTOEVALUACIÓN DE VULNERABILIDAD
PARTE 4: POLÍTICAS DE SEGURIDAD ORGANIZACIONAL
```

4. Tener a la mano (en papel o en otra ventana) el **material de referencia rápida** que se incluye al inicio de cada paso.

---

## Procedimiento Paso a Paso

---

### Paso 1 — Marco de Referencia: Tipos de Ingeniería Social

**Objetivo:** Establecer el vocabulario y los conceptos necesarios antes de analizar los casos.

**Duración estimada:** 5 minutos

#### Instrucciones

1. Lee detenidamente la siguiente tabla de referencia. La utilizarás durante todo el taller para clasificar cada caso.

| Técnica | Definición | Canal típico | Principio psicológico explotado |
|---------|-----------|-------------|--------------------------------|
| **Phishing** | Correo electrónico fraudulento que suplanta a una entidad legítima para robar credenciales o datos | Correo electrónico | Urgencia, miedo, autoridad |
| **Vishing** | Llamada telefónica fraudulenta en la que el atacante se hace pasar por soporte técnico, banco u otra entidad | Teléfono / VoIP | Autoridad, urgencia, confianza |
| **Smishing** | Mensaje de texto (SMS) fraudulento con enlaces maliciosos o solicitudes de datos | SMS / Mensajería | Urgencia, curiosidad |
| **Pretexting** | El atacante crea un escenario ficticio (pretexto) elaborado para obtener información o acceso | Presencial / Correo / Teléfono | Confianza, reciprocidad, autoridad |
| **Baiting** | Se deja un dispositivo (USB, CD) o se ofrece algo atractivo (descarga gratuita) para que la víctima lo use y se infecte | Físico / Digital | Curiosidad, codicia |
| **Tailgating / Piggybacking** | Acceso físico no autorizado a una instalación siguiendo a una persona autorizada | Físico | Amabilidad, distracción |

2. Revisa también los **indicadores comunes de alerta** (*red flags*) que aparecen en la mayoría de ataques de ingeniería social:

```
RED FLAGS COMUNES:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[!] Urgencia extrema: "Actúa AHORA o perderás tu cuenta"
[!] Amenaza de consecuencias graves: "Serás bloqueado / multado"
[!] Solicitud de credenciales o datos sensibles
[!] Remitente desconocido o dominio mal escrito (p.ej. paypa1.com)
[!] URL que no corresponde al dominio oficial
[!] Errores ortográficos o gramaticales notorios
[!] Oferta demasiado buena para ser verdad
[!] Presión para actuar sin consultar a otros
[!] Solicitud de acceso físico o remoto al equipo
[!] Adjuntos no solicitados (.exe, .zip, .docm)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

3. En tu documento, crea un encabezado para la **Parte 1** y deja espacio para las fichas de análisis.

#### Resultado esperado

El estudiante tiene claro el vocabulario y los indicadores de alerta que utilizará en el análisis de los seis casos siguientes.

#### Verificación

Antes de continuar, confirma que puedes responder estas preguntas sin consultar la tabla:
- ¿Cuál es la diferencia entre phishing y smishing?
- ¿Qué principio psicológico explota principalmente el pretexting?
- ¿Qué es el baiting y por qué es efectivo?

---

### Paso 2 — Análisis de Casos: Parte A (Casos 1, 2 y 3)

**Objetivo:** Analizar los primeros tres casos de ingeniería social y completar la ficha de análisis para cada uno.

**Duración estimada:** 12 minutos (4 minutos por caso)

#### Formato de Ficha de Análisis

Para cada caso, completa la siguiente ficha en tu documento:

```
╔══════════════════════════════════════════════════════════════╗
║  FICHA DE ANÁLISIS — CASO #___                               ║
╠══════════════════════════════════════════════════════════════╣
║  Tipo de ataque:                                             ║
║  Técnicas psicológicas utilizadas:                           ║
║  Señales de alerta identificadas (mínimo 3):                 ║
║    1.                                                        ║
║    2.                                                        ║
║    3.                                                        ║
║  Principio CIA afectado:  □ Confidencialidad                 ║
║                           □ Integridad                       ║
║                           □ Disponibilidad                   ║
║  Impacto potencial en la red/organización:                   ║
║  Medidas de mitigación aplicables (mínimo 2):                ║
║    1.                                                        ║
║    2.                                                        ║
╚══════════════════════════════════════════════════════════════╝
```

---

#### CASO 1 — El Correo del Banco

> **Escenario:** Laura, empleada de contabilidad de la empresa TechCorp S.A., recibe el siguiente correo electrónico un lunes por la mañana:
>
> ---
> **De:** seguridad@bancoseguro-alertas.net
> **Para:** laura.gomez@techcorp.com
> **Asunto:** ⚠️ URGENTE: Su cuenta ha sido comprometida — Acción requerida en 24 horas
>
> Estimado cliente,
>
> Hemos detectado actividad inusual en su cuenta bancaria número ****4521. Por su seguridad, su acceso ha sido temporalmente suspendido.
>
> Para reactivar su cuenta y evitar el bloqueo permanente, debe verificar su identidad INMEDIATAMENTE haciendo clic en el siguiente enlace:
>
> **→ http://bancoseguro-verificacion.net/reactivar?id=48291**
>
> Si no completa este proceso en las próximas 24 horas, su cuenta será cerrada definitivamente y se iniciarán acciones legales.
>
> Atentamente,
> Departamento de Seguridad
> Banco Seguro Nacional
>
> ---

#### Instrucciones para el Caso 1

1. Lee el correo detenidamente.
2. Identifica el tipo de ataque.
3. Anota **todas** las señales de alerta que encuentres (hay al menos 6 en este correo).
4. Determina qué principio de la tríada CIA se vería comprometido si Laura hace clic en el enlace.
5. Completa la ficha de análisis del Caso 1 en tu documento.

> **Pista de análisis:** Examina el dominio del remitente (`bancoseguro-alertas.net`) y compáralo con el dominio del enlace (`bancoseguro-verificacion.net`). ¿Son el mismo? ¿Corresponden al dominio oficial de un banco real? Analiza también el lenguaje utilizado: ¿qué emociones intenta provocar?

---

#### CASO 2 — La Llamada del Soporte Técnico

> **Escenario:** Carlos trabaja en el departamento de TI de una empresa mediana. Recibe una llamada telefónica:
>
> ---
> **Llamada entrante — Número desconocido**
>
> *Voz:* "Buenos días, le llamo de Microsoft Support. Hemos detectado que su computadora está enviando señales de error crítico a nuestros servidores. Su equipo puede estar infectado con un virus peligroso que está robando sus datos bancarios. Necesitamos acceder remotamente a su computadora ahora mismo para solucionar el problema antes de que se propague a toda su red corporativa. ¿Puede abrir su navegador e ir a la página support-remote-fix.com para instalar nuestra herramienta de diagnóstico?"
>
> *Carlos:* "¿Cómo saben que mi computadora tiene problemas?"
>
> *Voz:* "Señor, somos Microsoft, tenemos acceso a los registros de error de todos los equipos con Windows. No tenemos mucho tiempo, cada minuto que pasa el virus se está propagando. ¿Puede hacer lo que le pido o necesito escalar esto a su jefe directamente?"
>
> ---

#### Instrucciones para el Caso 2

1. Identifica el tipo de ataque (hay más de uno aplicable; elige el principal).
2. Enumera las técnicas psicológicas que el atacante utiliza en la llamada.
3. Identifica las señales de alerta presentes.
4. Describe el impacto potencial si Carlos sigue las instrucciones: ¿qué podría instalar en su computadora? ¿Qué tipo de amenaza de la Lección 5.1 se materializaría?
5. Completa la ficha de análisis del Caso 2.

> **Pista de análisis:** Microsoft (ni ninguna empresa de software legítima) contacta proactivamente a usuarios para reportar infecciones. Este es un dato clave. Analiza también la frase final: "¿o necesito escalar esto a su jefe?" — ¿qué técnica psicológica representa?

---

#### CASO 3 — El USB en el Estacionamiento

> **Escenario:** Al llegar a la oficina un martes, Ana encuentra en el estacionamiento de la empresa tres memorias USB con la etiqueta impresa: **"CONFIDENCIAL — Nóminas Q4 2024 — RR.HH."**. Ana recoge una de ellas y, con curiosidad, la conecta a su computadora de trabajo para ver qué contiene antes de entregarla al departamento de TI.
>
> Al conectarla, Windows muestra brevemente una ventana de "instalando dispositivo" y luego parece no pasar nada. Ana asume que el USB está vacío o dañado y lo deja en su cajón.
>
> Tres semanas después, el equipo de seguridad detecta que desde la computadora de Ana se están enviando datos al exterior de forma encubierta.

#### Instrucciones para el Caso 3

1. Identifica el tipo de ataque. ¿Quién colocó los USB allí y con qué propósito?
2. ¿Qué sucedió cuando Ana conectó el USB? ¿Qué tipo de malware (de los estudiados en la Lección 5.1) probablemente se instaló?
3. ¿Qué principios de la tríada CIA fueron comprometidos? (Puede ser más de uno.)
4. ¿Por qué la etiqueta "CONFIDENCIAL — Nóminas" fue una elección deliberada del atacante?
5. Propón al menos **tres** medidas de mitigación: una técnica, una de política organizacional y una de concientización.
6. Completa la ficha de análisis del Caso 3.

---

#### Resultado esperado (Paso 2)

Al finalizar este paso, el estudiante habrá completado tres fichas de análisis en su documento, identificando correctamente:
- **Caso 1:** Phishing con al menos 6 red flags; afecta Confidencialidad.
- **Caso 2:** Vishing con técnicas de urgencia, autoridad e intimidación; riesgo de instalación de troyano/RAT.
- **Caso 3:** Baiting con instalación silenciosa de spyware/troyano; afecta Confidencialidad e Integridad.

#### Verificación

Revisa que cada ficha contenga:
- [ ] Tipo de ataque identificado correctamente.
- [ ] Al menos 3 señales de alerta por caso.
- [ ] Al menos un principio CIA marcado.
- [ ] Al menos 2 medidas de mitigación por caso.

---

### Paso 3 — Análisis de Casos: Parte B (Casos 4, 5 y 6)

**Objetivo:** Analizar tres casos adicionales de mayor complejidad, incluyendo pretexting corporativo y smishing.

**Duración estimada:** 12 minutos (4 minutos por caso)

Usa el mismo formato de ficha de análisis del Paso 2.

---

#### CASO 4 — El Nuevo Proveedor

> **Escenario:** El gerente de operaciones de DataFlow Inc. recibe un correo de alguien que se presenta como representante de un proveedor de software con el que trabajan habitualmente:
>
> ---
> **De:** j.ramirez@cloudserv-solutions.net
> **Para:** gerente.operaciones@dataflow.com
> **Asunto:** Actualización urgente de credenciales de acceso al portal — Vence hoy
>
> Estimado Roberto,
>
> Como parte de nuestra migración de plataforma programada para esta noche, necesitamos que nos confirme las credenciales de acceso al portal de administración de su empresa para realizar la transferencia de configuraciones sin interrumpir el servicio.
>
> El proveedor real usa el dominio @cloudserv.com. Este correo proviene de @cloudserv-solutions.net.
>
> Por favor envíe usuario y contraseña al responder este correo o llame al +1-800-555-0199 antes de las 5:00 PM de hoy.
>
> Gracias por su colaboración,
> Juan Ramírez
> Ejecutivo de Cuentas — CloudServ Solutions
>
> ---

#### Instrucciones para el Caso 4

1. Este caso combina **dos técnicas** de ingeniería social. Identifica ambas.
2. El atacante ya investigó a la empresa antes de enviar el correo (sabe el nombre del gerente y el proveedor real). ¿Cómo pudo haber obtenido esta información? (Piensa en LinkedIn, página web corporativa, etc.)
3. ¿Por qué solicitar las credenciales por correo es una señal de alerta absoluta, independientemente de quién sea el remitente?
4. Propón una política organizacional específica que hubiera prevenido este ataque.
5. Completa la ficha de análisis del Caso 4.

---

#### CASO 5 — El SMS del Paquete

> **Escenario:** Marta recibe el siguiente SMS en su teléfono personal, que también usa para trabajar (BYOD):
>
> ---
> **SMS — Número desconocido (+52-55-1234-5678)**
>
> "DHL Express: Su paquete #MX8821934 no pudo ser entregado. Pague $49 MXN de cargo de aduana para reprogramar entrega: http://dhl-mx-entrega.xyz/pago"
>
> ---
>
> Marta efectivamente está esperando un paquete. Hace clic en el enlace, que abre una página muy similar al sitio de DHL con un formulario de pago. Ingresa su tarjeta de crédito.

#### Instrucciones para el Caso 5

1. Identifica el tipo de ataque.
2. Analiza la URL `http://dhl-mx-entrega.xyz/pago`. Identifica al menos **tres** características que la delatan como fraudulenta.
3. ¿Por qué el hecho de que Marta "efectivamente estuviera esperando un paquete" hace este ataque más efectivo? ¿Qué principio psicológico explota esto?
4. ¿Qué datos comprometió Marta? ¿Qué principio CIA fue afectado?
5. ¿Cómo el uso de dispositivos personales para trabajo (BYOD) amplifica el riesgo de este tipo de ataque?
6. Completa la ficha de análisis del Caso 5.

> **Pista técnica para la URL:** Evalúa: protocolo (HTTP vs HTTPS), dominio raíz (`.xyz` vs `.com.mx`), estructura del subdominio, y si el dominio contiene el nombre de la marca real.

---

#### CASO 6 — El Técnico de Mantenimiento

> **Escenario:** Un hombre con uniforme azul, casco de seguridad y una tableta llega a las instalaciones de una empresa de manufactura. Se dirige a la recepcionista y dice:
>
> *"Buenos días, soy Ing. Torres, de ServitecRed. Vengo a realizar el mantenimiento programado de los switches del cuarto de servidores. Ya coordiné con el área de TI la semana pasada."*
>
> La recepcionista, ocupada con varias llamadas, no encuentra el nombre en la lista de visitas pero asume que fue un error administrativo. El "técnico" luce profesional y confiado. Ella lo deja pasar.
>
> Una vez en el cuarto de servidores, el individuo conecta un pequeño dispositivo a uno de los switches y abandona las instalaciones 20 minutos después. El dispositivo conectado es un pequeño hardware que captura y reenvía todo el tráfico de la red a un servidor externo.

#### Instrucciones para el Caso 6

1. Identifica el tipo principal de ataque. ¿Hay alguna técnica secundaria presente?
2. El atacante utilizó múltiples elementos para parecer legítimo. Enuméralos y explica qué principio psicológico activa cada uno.
3. ¿Qué tipo de amenaza técnica (de la Lección 5.1) se materializó con el dispositivo conectado al switch?
4. La recepcionista cometió un error de procedimiento. ¿Cuál fue? ¿Qué política debería existir para evitarlo?
5. Propón un control técnico y un control físico para prevenir este tipo de ataque.
6. Completa la ficha de análisis del Caso 6.

---

#### Resultado esperado (Paso 3)

Al finalizar, el estudiante habrá completado seis fichas de análisis en total. Los casos 4-6 deben revelar:
- **Caso 4:** Phishing + Pretexting; importancia de políticas de no compartir credenciales por correo.
- **Caso 5:** Smishing; análisis de URL fraudulenta; riesgo BYOD; afecta Confidencialidad.
- **Caso 6:** Pretexting + Tailgating físico; instalación de dispositivo de sniffing; afecta Confidencialidad e Integridad.

#### Verificación

Confirma que en tus seis fichas aparezca al menos una vez cada uno de los siguientes tipos de ataque:
- [ ] Phishing
- [ ] Vishing
- [ ] Smishing
- [ ] Pretexting
- [ ] Baiting

---

### Paso 4 — Diseño del Correo de Concientización

**Objetivo:** Aplicar los conocimientos adquiridos para crear un comunicado de seguridad dirigido a usuarios finales de una organización ficticia.

**Duración estimada:** 10 minutos

#### Instrucciones

1. En tu documento, crea la sección **Parte 2: Correo de Concientización**.

2. Usa el siguiente contexto ficticio:

```
CONTEXTO:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Empresa:     TechCorp S.A. de C.V.
Tu rol:      Analista de Seguridad del Departamento de TI
Destinatarios: Todos los empleados (usuarios no técnicos)
Amenaza a comunicar: Campaña activa de phishing detectada
                     en los últimos 3 días, suplantando al
                     banco corporativo de la empresa.
Objetivo del correo: Alertar, educar y dar instrucciones
                     claras de qué hacer y qué NO hacer.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

3. Redacta el correo de concientización siguiendo esta estructura obligatoria:

```
ESTRUCTURA DEL CORREO DE CONCIENTIZACIÓN:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[A] ASUNTO: Claro, informativo, sin generar pánico
[B] SALUDO: Inclusivo y profesional
[C] DESCRIPCIÓN DE LA AMENAZA: En lenguaje simple (no técnico)
    - ¿Qué está pasando?
    - ¿Cómo se ve el correo fraudulento? (describe las señales)
[D] LO QUE NO DEBES HACER: Lista concreta de 3-5 puntos
[E] LO QUE SÍ DEBES HACER: Lista concreta de 3-5 puntos
    (incluir cómo reportar el incidente)
[F] CÓMO VERIFICAR SI RECIBISTE UN CORREO SOSPECHOSO:
    Instrucciones simples y visuales (sin tecnicismos)
[G] CONTACTO DE REPORTE: A quién llamar / escribir
[H] FIRMA: Tu nombre, rol y datos de contacto
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

4. El correo debe cumplir los siguientes criterios de calidad:
   - **Lenguaje claro y accesible** para usuarios sin conocimientos técnicos.
   - **Tono profesional pero no alarmista** — el objetivo es informar, no paralizar.
   - **Instrucciones específicas y accionables** — evita frases genéricas como "ten cuidado".
   - **Longitud apropiada** — no debe ser tan largo que nadie lo lea (máximo 400 palabras).
   - **Mencionar MFA** como medida de protección adicional.

5. Al final del correo, agrega una nota entre corchetes `[NOTA TÉCNICA PARA EL INSTRUCTOR]` indicando qué técnica de ingeniería social describiste y cuál es el principal red flag que destacaste.

#### Ejemplo de fragmento (solo como referencia de tono y estilo)

```
EJEMPLO DE SECCIÓN [D] — LO QUE NO DEBES HACER:

❌ NO hagas clic en enlaces de correos que pidan tus credenciales,
   aunque el correo parezca legítimo.
❌ NO respondas correos con tu contraseña, número de cuenta
   o cualquier dato personal, sin importar quién lo solicite.
❌ NO abras archivos adjuntos que no esperabas recibir,
   especialmente si tienen extensiones como .exe, .zip o .docm.
```

#### Resultado esperado

Un correo de concientización completo, en lenguaje claro, que cubra las ocho secciones indicadas y que un empleado no técnico pueda entender y aplicar de inmediato.

#### Verificación

Revisa tu correo con esta lista:
- [ ] Asunto claro y no alarmista.
- [ ] Descripción de la amenaza en lenguaje no técnico.
- [ ] Al menos 3 puntos en "Lo que NO debes hacer".
- [ ] Al menos 3 puntos en "Lo que SÍ debes hacer".
- [ ] Mención explícita de MFA o verificación en dos pasos.
- [ ] Instrucciones de reporte incluidas.
- [ ] Longitud máxima de 400 palabras.

---

### Paso 5 — Autoevaluación de Vulnerabilidad y Políticas de Seguridad

**Objetivo:** Reflexionar sobre las propias vulnerabilidades ante ataques de ingeniería social y elaborar un conjunto de políticas preventivas para la organización ficticia.

**Duración estimada:** 5 minutos

#### Instrucciones — Parte A: Autoevaluación

1. En tu documento, crea la sección **Parte 3: Autoevaluación de Vulnerabilidad Personal**.

2. Responde honestamente las siguientes preguntas (las respuestas son privadas; no serán calificadas por su contenido, sino por su reflexión):

```
CUESTIONARIO DE AUTOEVALUACIÓN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. ¿Utilizas la misma contraseña en múltiples servicios (correo,
   redes sociales, banco)? Sí / No / A veces
   → Reflexión: ¿Qué riesgo implica esto según lo visto en
     la Lección 5.1 (ataques a contraseñas / credential stuffing)?

2. ¿Has hecho clic alguna vez en un enlace de un correo o SMS
   sin verificar el remitente primero? Sí / No / No recuerdo
   → Reflexión: ¿Cuál de los casos analizados se parece más
     a una situación que podrías haber enfrentado?

3. ¿Tienes activada la autenticación multifactor (MFA) en tu
   correo electrónico principal? Sí / No / No sé cómo hacerlo
   → Reflexión: ¿Por qué MFA reduce significativamente el
     impacto de un ataque de phishing exitoso?

4. Si alguien que dice ser del soporte técnico de tu empresa
   te llama y pide tu contraseña para "resolver un problema",
   ¿qué harías? Describe tu respuesta en 2-3 oraciones.

5. ¿Conectarías un USB encontrado en la calle o en tu lugar
   de trabajo si tuviera una etiqueta llamativa? Sí / No
   → Reflexión: ¿Qué aprendiste del Caso 3 que cambiaría
     tu comportamiento?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

#### Instrucciones — Parte B: Políticas de Seguridad Organizacional

1. En tu documento, crea la sección **Parte 4: Políticas de Seguridad para TechCorp S.A.**

2. Elabora un listado de **mínimo 8 políticas de seguridad** orientadas a reducir el riesgo de ingeniería social. Para cada política, incluye:
   - **Nombre de la política**
   - **Descripción breve** (1-2 oraciones)
   - **Técnica de ingeniería social que mitiga** (referencia a los casos analizados)

3. Usa la siguiente tabla como formato:

```
POLÍTICAS DE SEGURIDAD — TechCorp S.A.
Elaborado por: [Tu nombre]   Fecha: [Fecha actual]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
#  | Nombre de la Política         | Descripción          | Mitiga
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1  |                               |                      |
2  |                               |                      |
...
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

4. **Políticas sugeridas** (debes incluirlas si no se te ocurren otras; puedes agregar más):

| # | Nombre sugerido | Técnica que mitiga |
|---|----------------|-------------------|
| 1 | Política de contraseñas robustas y no reutilización | Ataques a contraseñas, credential stuffing |
| 2 | Autenticación multifactor obligatoria (MFA) para todos los sistemas | Phishing, vishing |
| 3 | Prohibición de compartir credenciales por cualquier medio | Phishing, pretexting, vishing |
| 4 | Verificación de identidad de visitantes externos | Pretexting, tailgating |
| 5 | Política de dispositivos USB — solo USBs autorizados y registrados | Baiting |
| 6 | Capacitación anual obligatoria en concientización de seguridad | Todas las técnicas |
| 7 | Protocolo de reporte de incidentes de seguridad (canal claro y accesible) | Todas las técnicas |
| 8 | Política de verificación fuera de banda para solicitudes urgentes | Phishing, vishing, pretexting |

> **Nota:** La "verificación fuera de banda" (Política #8) significa que si recibes una solicitud urgente por correo, la verificas llamando directamente al solicitante usando un número conocido — no el que aparece en el correo.

#### Resultado esperado

- Autoevaluación completa con reflexiones escritas para las 5 preguntas.
- Tabla de políticas con al menos 8 entradas, cada una con nombre, descripción y técnica mitigada.

#### Verificación

- [ ] Las 5 preguntas de autoevaluación están respondidas con reflexión genuina.
- [ ] La tabla de políticas tiene al menos 8 entradas completas.
- [ ] Cada política menciona explícitamente qué técnica de ingeniería social mitiga.
- [ ] Al menos una política hace referencia directa a MFA.
- [ ] Al menos una política aborda controles físicos (no solo digitales).

---

## Validación y Pruebas

Al completar todos los pasos, verifica que tu documento de entrega cumpla con la siguiente lista de verificación integral:

### Lista de Verificación Final del Entregable

```
CHECKLIST DE ENTREGABLE — PRÁCTICA 5
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PARTE 1 — FICHAS DE ANÁLISIS
□ 6 fichas completadas (una por cada caso)
□ Cada ficha identifica el tipo de ataque correctamente
□ Cada ficha lista al menos 3 señales de alerta
□ Cada ficha tiene al menos un principio CIA marcado
□ Cada ficha tiene al menos 2 medidas de mitigación
□ Los 5 tipos de ataque (phishing, vishing, smishing,
  pretexting, baiting) están representados en el conjunto

PARTE 2 — CORREO DE CONCIENTIZACIÓN
□ Las 8 secciones obligatorias están presentes
□ Lenguaje claro y accesible (no técnico)
□ Mención de MFA incluida
□ Instrucciones de reporte incluidas
□ Longitud ≤ 400 palabras
□ Nota técnica para el instructor incluida

PARTE 3 — AUTOEVALUACIÓN
□ 5 preguntas respondidas con reflexión escrita
□ Cada respuesta conecta con conceptos del curso

PARTE 4 — POLÍTICAS DE SEGURIDAD
□ Mínimo 8 políticas en la tabla
□ Cada política tiene nombre, descripción y técnica mitigada
□ Se incluyen controles tanto digitales como físicos
□ MFA está presente como política explícita
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
TOTAL: ___/20 ítems completados
```

### Autoevaluación de Comprensión

Antes de entregar, responde mentalmente estas preguntas de verificación:

1. ¿Puedes explicar la diferencia entre phishing y pretexting con un ejemplo propio?
2. ¿Por qué MFA no elimina completamente el riesgo de phishing, pero sí lo reduce significativamente?
3. ¿Qué harías si mañana recibieras un correo similar al del Caso 1 en tu cuenta personal?
4. ¿Cuál de los seis casos te pareció el más peligroso y por qué?

---

## Solución de Problemas

### Problema 1 — Dificultad para identificar el tipo de ataque en casos que combinan técnicas

**Síntoma:** El estudiante no puede decidir si clasificar el Caso 4 como "phishing" o "pretexting", o el Caso 6 como "pretexting" o "tailgating".

**Causa:** Algunos ataques reales combinan múltiples técnicas de ingeniería social. La clasificación no siempre es mutuamente excluyente.

**Solución:**
1. Identifica la técnica **primaria** (la que inicia o sostiene el ataque) y las técnicas **secundarias** (las que lo complementan).
2. Usa esta guía de decisión:
   - Si el ataque inicia con un correo electrónico → la técnica primaria es **phishing**.
   - Si el ataque requiere que el atacante construya una historia elaborada para obtener confianza → la técnica primaria es **pretexting**.
   - Si el acceso es físico no autorizado siguiendo a alguien → la técnica primaria es **tailgating**.
3. En tu ficha, puedes escribir: "Tipo de ataque: Pretexting (con elementos de phishing como vector inicial)".
4. Lo importante es que justifiques tu clasificación, no que exista una única respuesta correcta.

---

### Problema 2 — Dificultad para redactar el correo de concientización en lenguaje no técnico

**Síntoma:** El estudiante redacta el correo de concientización usando términos como "vector de ataque", "payload", "header spoofing" o "DNS poisoning", que los usuarios finales no entenderán.

**Causa:** Es natural que quien estudia redes y seguridad tienda a usar vocabulario técnico. Sin embargo, la concientización efectiva requiere adaptarse al nivel del receptor.

**Solución:**
1. Aplica la regla del "abuelo/abuela": si tu familiar sin conocimientos técnicos no entendería la frase, reescríbela.
2. Usa estas sustituciones de lenguaje:

```
VOCABULARIO TÉCNICO → LENGUAJE SIMPLE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
"vector de ataque"     → "forma en que el atacante llega a ti"
"payload malicioso"    → "archivo o programa dañino"
"dominio suplantado"   → "dirección web falsa que imita a una real"
"credenciales"         → "usuario y contraseña"
"autenticación MFA"    → "verificación en dos pasos"
"phishing"             → "correo falso que roba tus datos"
"header spoofing"      → "el correo parece venir de alguien de confianza,
                          pero en realidad no"
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

3. Después de redactar cada párrafo, pregúntate: "¿Qué debe hacer el lector con esta información?" Si no hay una acción clara, reescribe el párrafo orientándolo a la acción.

---

## Limpieza y Cierre

Al finalizar la práctica, realiza las siguientes acciones:

1. **Guarda tu documento** con el nombre final: `Practica5_Ingenieria_Social_[TuNombre]_[Fecha].docx`
2. **Crea una copia de respaldo** en una ubicación diferente (nube, USB personal, correo propio).
3. **Revisa que el documento esté completo** usando el checklist de validación del paso anterior.
4. **No compartas tus respuestas de autoevaluación** (Parte 3) en foros públicos o grupos de chat del curso, ya que contienen información personal sobre tus hábitos de seguridad.
5. Si usaste un navegador para consultar referencias durante la práctica, cierra las pestañas abiertas.

> **Nota de privacidad:** Las respuestas de la autoevaluación (Parte 3) son para reflexión personal. El instructor puede pedirte que entregues el documento completo, pero las respuestas de autoevaluación no serán evaluadas por su contenido específico, sino por la profundidad de la reflexión.

---

## Resumen

### Conceptos Aplicados en Esta Práctica

En esta práctica aplicaste los conceptos de la Lección 5.1 y 5.2 en un contexto práctico y analítico:

| Concepto | Dónde se aplicó |
|----------|----------------|
| Tríada CIA (Confidencialidad, Integridad, Disponibilidad) | Fichas de análisis — campo "Principio CIA afectado" |
| Tipos de malware (troyano, spyware, ransomware) | Casos 2, 3 y 6 — impacto técnico del ataque |
| Sniffing / interceptación de tráfico | Caso 6 — dispositivo conectado al switch |
| Ataques a contraseñas / credential stuffing | Autoevaluación y políticas de seguridad |
| Autenticación multifactor (MFA) | Correo de concientización y políticas |
| Defensa en profundidad | Políticas de seguridad — combinación de controles |
| Ingeniería social (phishing, vishing, smishing, pretexting, baiting) | Todos los casos y el correo de concientización |

### Lecciones Clave

- La **ingeniería social** es el vector de ataque más efectivo porque explota el comportamiento humano, no las vulnerabilidades tecnológicas. Ningún firewall puede bloquear a un empleado que voluntariamente entrega su contraseña.
- Las **señales de alerta** son consistentes entre técnicas: urgencia artificial, solicitudes de credenciales, dominios mal escritos y presión para actuar sin consultar a otros.
- La **concientización** no es un evento único; es un proceso continuo. Un correo de alerta bien redactado puede prevenir cientos de incidentes.
- La **MFA** es la medida de mitigación con mejor relación costo-beneficio contra phishing: aunque el atacante obtenga la contraseña, no puede acceder sin el segundo factor.
- Las **políticas organizacionales** son tan importantes como los controles técnicos. Un empleado que conoce el procedimiento correcto (verificar fuera de banda, reportar incidentes) es el mejor sistema de detección temprana.

### Recursos de Referencia

- [CISA — Recursos de concientización en ciberseguridad (en inglés)](https://www.cisa.gov/topics/cybersecurity-best-practices)
- [NIST — Guía de concientización y capacitación en seguridad (SP 800-50)](https://csrc.nist.gov/publications/detail/sp/800-50/final)
- [Anti-Phishing Working Group (APWG) — Reportes de tendencias de phishing](https://apwg.org/trendsreports/)
- [Google — Prueba de reconocimiento de phishing (Phishing Quiz)](https://phishingquiz.withgoogle.com/)
- [Microsoft — Cómo activar la verificación en dos pasos](https://support.microsoft.com/es-es/account-billing/c%C3%B3mo-usar-la-verificaci%C3%B3n-en-dos-pasos-con-tu-cuenta-de-microsoft-c7910146-672f-01e9-50a0-93b4b2a24d3e)
- [Have I Been Pwned — Verificar si tu correo fue comprometido en una brecha](https://haveibeenpwned.com/)

---

> **Conexión con la Práctica 6:** Los conocimientos de diagnóstico de red que aplicarás en la Práctica 6 (herramientas de línea de comandos, análisis de tráfico) te permitirán detectar en la red los síntomas técnicos de algunos de los ataques analizados hoy — como el tráfico anómalo generado por el spyware del Caso 3 o el sniffing del Caso 6.

---
LAB_END---
