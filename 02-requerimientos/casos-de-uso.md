# 🔄 Especificación de Casos de Uso del Sistema

Este documento describe de forma detallada los casos de uso (CU) principales para **Flux Payments**. Cada caso de uso define la secuencia de interacciones entre los actores externos (usuarios o sistemas) y la plataforma para lograr un objetivo de negocio.

---

## 🗺️ Índice de Casos de Uso
1.  **`CU-01`:** Procesar Transacción de Pago Inmediato (Payout) vía SPI.
2.  **`CU-02`:** Conciliación Automática Diaria de Transacciones.
3.  **`CU-03`:** Gestión de Credenciales API en el Portal de Clientes.

---

## 📑 Especificaciones de Casos de Uso

### 1. CU-01: Procesar Transacción de Pago Inmediato (Payout) vía SPI

```
      Fintech Cliente ───( Envía Solicitud )───> [ Flux Payments ] ───( Procesa )───> [ SPI / Karpay ]
```

*   **Identificador:** `CU-01`
*   **Nombre:** Procesar Transacción de Pago Inmediato (Payout) vía SPI.
*   **Descripción:** Permite a una empresa Fintech integrada realizar la transferencia inmediata de fondos desde su cuenta de balance en Flux hacia la cuenta bancaria de un beneficiario a través de la red del SPI.
*   **Actores:** Fintech Cliente (Iniciador), Sistema Core de Flux (Orquestador), Karpay / SPI (Procesador externo).
*   **Precondiciones:**
    1.  La Fintech cliente debe estar autenticada y autorizada con un Token JWT activo.
    2.  La cuenta de balance de la Fintech en Flux debe contar con fondos disponibles iguales o mayores al monto a transferir más la comisión transaccional.
*   **Flujo Principal:**
    1.  La **Fintech Cliente** envía una petición HTTP POST a `/v1/payouts` con el monto, datos de la cuenta destino (banco, número, tipo de cuenta), identificación del beneficiario y referencia única.
    2.  El **API Gateway** valida el token JWT de la petición, aplica rate-limiting y transfiere la solicitud al **Motor de Orquestación**.
    3.  El **Motor de Orquestación** consulta al módulo de Balance y verifica que existan fondos suficientes.
    4.  El sistema realiza un débito temporal en el **Core Ledger** de la cuenta de la Fintech por el monto solicitado, bloqueando los fondos en estado `PENDIENTE_LIQUIDACION`.
    5.  El **Motor de Orquestación** traduce la petición al formato nativo requerido por **Karpay** (firma el mensaje criptográficamente) y envía la orden al conector del SPI.
    6.  El conector del SPI (Karpay) procesa la solicitud en tiempo real con el **Banco de la República** y retorna un código de confirmación de éxito.
    7.  El **Motor de Orquestación** recibe la confirmación exitosa, actualiza el estado de la transacción a `EXITOSA` en el Core Ledger, liberando y transfiriendo definitivamente los fondos a la cuenta transaccional del SPI.
    8.  El sistema responde a la Fintech Cliente con un código `HTTP 201 Created` y el cuerpo JSON confirmando el estado exitoso de la transacción.
*   **Flujos Alternativos e Inconsistencias (Excepciones):**
    *   *Excepción A: Fondos Insuficientes (Paso 3)*
        1. El sistema detecta que el balance disponible de la Fintech es menor al monto solicitado.
        2. El sistema cancela el procesamiento de inmediato.
        3. Retorna un error `HTTP 402 Payment Required` detallando la insuficiencia de fondos. El caso de uso finaliza sin afectación al Ledger.
    *   *Excepción B: Rechazo en la Red de Pagos / Cuenta Inexistente (Paso 6)*
        1. El conector del SPI (Karpay) responde indicando que la cuenta destino no existe o está inactiva.
        2. El **Motor de Orquestación** captura el rechazo y ejecuta una reversión en el **Core Ledger**, acreditando de vuelta el monto debitado temporalmente a la cuenta de balance de la Fintech.
        3. El estado de la transacción se actualiza a `FALLIDA` en el historial.
        4. El sistema responde al cliente con un código `HTTP 422 Unprocessable Entity` detallando el motivo del rechazo del banco destino.
    *   *Excepción C: Timeout del Proveedor Externo (Paso 6)*
        1. La red de Karpay no responde a la petición dentro de los 5 segundos de límite (Timeout).
        2. El **Motor de Orquestación** abre el *Circuit Breaker* y marca la transacción internamente como `EN_PROCESAMIENTO_PENDIENTE_VERIFICACION`.
        3. Responde al cliente con un código `HTTP 202 Accepted` indicando que el resultado es incierto y que se enviará la resolución definitiva asíncronamente.
        4. El sistema encola la transacción en un Worker asíncrono que consultará periódicamente el estado de esa referencia hasta obtener confirmación de liquidación o rechazo.
*   **Postcondiciones:**
    *   *Caso Exitoso:* Los fondos fueron debitados de la Fintech, transferidos al SPI, y la transacción quedó asentada inmutablemente.
    *   *Caso Fallido:* Los fondos de la Fintech quedaron intactos tras la reversión y el Ledger documenta el rechazo.

---

### 2. CU-02: Conciliación Automática Diaria de Transacciones

*   **Identificador:** `CU-02`
*   **Nombre:** Conciliación Automática Diaria de Transacciones.
*   **Descripción:** Ejecuta el cruce masivo de información financiera entre los registros internos de Flux (Core Ledger) y el reporte diario de compensación proveído por el procesador del SPI (Karpay).
*   **Actores:** Motor de Conciliación (Sistema), Operador Flux (Soporte).
*   **Precondiciones:**
    1.  Ha concluido el día bancario y el procesador Karpay ha depositado el reporte consolidado transaccional en el servidor SFTP seguro de Flux.
*   **Flujo Principal:**
    1.  A las **00:15 AM**, el cron-job de Flux activa el **Motor de Conciliación**.
    2.  El sistema descarga el archivo consolidado (en formato estándar de compensación bancaria) desde el SFTP de Karpay de forma segura.
    3.  El motor procesa el archivo registro por registro, abstrayendo montos, referencias únicas y marcas de tiempo.
    4.  El motor realiza una consulta masiva al **Core Ledger** buscando transacciones correspondientes al mismo día calendario.
    5.  El motor cruza y empareja de forma automática las transacciones que coinciden exactamente en referencia única, monto de transacción y estados operativos.
    6.  Las transacciones exitosamente emparejadas son marcadas en el ledger con la bandera `CONCILIADA_SISTEMA`.
    7.  El sistema genera un reporte final de conciliación diaria con la tasa de éxito (ej. 98% de transacciones automatizadas) y lo almacena para consulta de auditoría.
*   **Flujos Alternativos e Inconsistencias (Excepciones):**
    *   *Excepción A: Discrepancia Transaccional (Paso 5)*
        1. El motor encuentra una transacción registrada en el SPI que no existe en el Core Ledger o que presenta una diferencia en el monto centavos.
        2. El motor marca la transacción con la bandera `DISCREPANCIA_PENDIENTE_REVISION`.
        3. El sistema genera una alerta crítica de operaciones y la publica en el **Portal Administrativo** con un código de error visual en rojo.
        4. El **Operador Flux** recibe la alerta al iniciar su turno, revisa las trazas manuales en el Core Ledger y el SPI, y realiza un asiento manual de ajuste con justificación en el Ledger.
    *   *Excepción B: Archivo Incompleto o SFTP Caído (Paso 2)*
        1. El sistema no puede conectarse al SFTP de Karpay o el archivo no ha sido depositado a tiempo.
        2. El motor envía una alerta al equipo DevOps y pospone la tarea por 1 hora, realizando hasta 3 intentos automáticos de conexión.
*   **Postcondiciones:**
    *   Las transacciones del día quedan marcadas como conciliadas y se emiten los reportes operativos de discrepancias.

---

### 3. CU-03: Gestión de Credenciales API en el Portal de Clientes

*   **Identificador:** `CU-03`
*   **Nombre:** Gestión de Credenciales API en el Portal de Clientes.
*   **Descripción:** Permite a los desarrolladores de la Fintech integrada generar, visualizar y rotar llaves de API (API Keys) seguras para interactuar con la plataforma tanto en el Sandbox de pruebas como en el entorno productivo.
*   **Actores:** Empresa Cliente (Desarrollador / Admin).
*   **Precondiciones:**
    1.  El usuario de la Fintech debe estar registrado e iniciar sesión con MFA activo en el Portal de Clientes de Flux.
*   **Flujo Principal:**
    1.  El desarrollador inicia sesión en el **Portal de Clientes** de Flux.
    2.  Navega a la sección **"Developer / Credenciales"** en el panel lateral.
    3.  El sistema despliega una interfaz que lista las credenciales vigentes (API Keys) y su estado (Activa / Revocada).
    4.  El usuario hace clic en el botón **"Generar Nueva API Key"**.
    5.  El portal solicita la confirmación mediante autenticación de segundo factor (MFA/OTP).
    6.  El usuario ingresa el código OTP válido.
    7.  El sistema genera un par de llaves: un `Client ID` (público) y un `Client Secret` (privado, hasheado en base de datos con algoritmo fuerte).
    8.  El portal muestra el `Client Secret` en pantalla **por una única vez**, advirtiendo al usuario que debe guardarlo de forma segura.
    9.  El sistema asienta la creación en el log de auditoría del portal indicando fecha, hora, usuario e IP del creador.
*   **Flujos Alternativos e Inconsistencias (Excepciones):**
    *   *Excepción A: Código OTP Inválido (Paso 6)*
        1. El usuario ingresa un código OTP incorrecto o expirado.
        2. El sistema deniega la creación de credenciales, despliega un mensaje de error y aumenta el contador de intentos fallidos.
        3. Si se superan 3 intentos fallidos, el usuario es bloqueado temporalmente por seguridad.
*   **Postcondiciones:**
    *   El cliente dispone de una nueva credencial transaccional para realizar solicitudes HTTPS seguras a la API de Flux.
