# 📋 Catálogo de Requerimientos Funcionales (RF)

Este documento detalla los requerimientos funcionales del sistema **Flux Payments**, los cuales definen el comportamiento y las capacidades que la plataforma debe ofrecer a los diferentes actores del ecosistema.

---

## 1. Mapeo de Actores del Sistema
*   **Empresa Cliente (Fintech/Marketplace/ERP):** Entidad integrada por API que inicia transacciones de pago/recaudo y consume la data operativa.
*   **Usuario Final (Comprador/Beneficiario):** Persona natural o jurídica que realiza el pago en el Checkout o recibe una dispersión (payout) de fondos.
*   **Operador Flux (Equipo Interno):** Personal administrativo que monitorea y gestiona la plataforma técnica.
*   **Sistema de Pagos Inmediatos (SPI/Karpay):** Entidad externa procesadora de la red transaccional inmediata en Colombia.

---

## 2. Catálogo Detallado de Requerimientos

### 📂 A. Módulo de Gestión Transaccional (Pagos y Recaudos)

| ID | Nombre | Descripción | Prioridad | Actor | Criterios de Aceptación (Gherkin BDD) |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **RF-01** | Procesamiento de Pago Inmediato (Payout) | El sistema debe permitir a una empresa cliente enviar una orden de pago en tiempo real hacia una cuenta bancaria destino vía SPI. | **Alta** | Empresa Cliente / SPI | **Dado** que la Fintech cliente está autenticada y tiene fondos suficientes en su balance,<br>**Cuando** envía una solicitud POST a `/v1/payouts` con los datos del beneficiario,<br>**Entonces** el sistema valida el formato, enruta la transacción al SPI (Karpay) y responde con estado `PROCESANDO` en menos de 1 segundo. |
| **RF-02** | Recaudo de Fondos (Pay-in) | El sistema debe procesar recaudos en tiempo real mediante el débito de cuentas utilizando el SPI o conectores alternativos (PSE). | **Alta** | Empresa Cliente / Usuario Final | **Dado** que el usuario final está en el Checkout de cobro de Flux,<br>**Cuando** selecciona su banco y autoriza la transacción SPI/PSE,<br>**Entonces** el sistema debita la cuenta origen, actualiza el estado a `EXITOSO` y lo registra en el Ledger. |
| **RF-03** | Cancelación / Reversión | El sistema debe permitir reversar transacciones fallidas de forma automática para restaurar los balances en el Core Ledger. | **Alta** | Sistema de Orquestación | **Dado** que una transacción de pago fue enviada al SPI y este responde con un error de "Cuenta Inexistente",<br>**Cuando** el Motor de Orquestación captura el rechazo,<br>**Entonces** realiza la reversión del saldo debitado en el Core Ledger y marca la transacción como `FALLIDA`. |

### 📂 B. Módulo de Integración API y Webhooks

| ID | Nombre | Descripción | Prioridad | Actor | Criterios de Aceptación (Gherkin BDD) |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **RF-04** | Autenticación y Autorización API | El sistema debe autenticar de forma segura todas las peticiones a la API utilizando llaves de API (API Keys) en Sandbox y tokens JWT/OAuth2 en Producción. | **Alta** | Empresa Cliente | **Dado** que una empresa cliente realiza una llamada API,<br>**Cuando** la petición no contiene la cabecera `Authorization` o la firma es inválida,<br>**Entonces** el API Gateway debe bloquear la petición inmediatamente respondiendo con un error `HTTP 401 Unauthorized`. |
| **RF-05** | Notificación por Webhooks | El sistema debe notificar asíncronamente y en tiempo real a las empresas clientes sobre cambios en los estados de sus transacciones. | **Alta** | Empresa Cliente / Orquestador | **Dado** que el SPI confirma que un pago ha sido liquidado con éxito,<br>**Cuando** el orquestador actualiza la base de datos,<br>**Entonces** dispara un evento HTTP POST a la URL de webhook registrada por el cliente con la firma SHA-256 en la cabecera para validar autenticidad. |

### 📂 C. Módulo de Registro y Ledger Financiero

| ID | Nombre | Descripción | Prioridad | Actor | Criterios de Aceptación (Gherkin BDD) |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **RF-06** | Registro de Partida Doble en Core Ledger | El sistema debe registrar cada transacción financiera afectando cuentas débito y crédito correspondientes para asegurar consistencia e inmutabilidad. | **Alta** | Sistema Core | **Dado** que se aprueba una transacción de pago,<br>**Cuando** el ledger procesa la operación,<br>**Entonces** crea un asiento contable con dos entradas (Débito a Cuenta Flux Cliente, Crédito a Cuenta Tránsito SPI) garantizando que la suma neta sea cero y que el registro sea inalterable. |
| **RF-07** | Historial y Auditoría Ledger | El sistema debe permitir a los auditores y clientes consultar la traza histórica y la firma de inmutabilidad de cualquier movimiento. | **Media** | Empresa Cliente / Operador Flux | **Dado** que el cliente solicita el historial de saldos en su portal,<br>**Cuando** filtra por rango de fechas,<br>**Entonces** el sistema retorna la lista de asientos contables con sus respectivas marcas de tiempo y el hash criptográfico SHA-256 de verificación. |

### 📂 D. Módulo de Conciliación Automática

| ID | Nombre | Descripción | Prioridad | Actor | Criterios de Aceptación (Gherkin BDD) |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **RF-08** | Motor de Conciliación Automática | El sistema debe procesar diariamente los archivos de compensación y liquidación bancaria del SPI y cruzarlos contra el Core Ledger. | **Alta** | Sistema Core / Operador | **Dado** que se recibe el archivo de conciliación diario del SPI (Karpay),<br>**Cuando** el Motor de Conciliación ejecuta el cruce transaccional,<br>**Entonces** empareja automáticamente las transacciones con estado exitoso en un **80% o más**, y marca las diferencias para auditoría manual. |

### 📂 E. Módulo de Interfaces Web (Portales)

| ID | Nombre | Descripción | Prioridad | Actor | Criterios de Aceptación (Gherkin BDD) |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **RF-09** | Portal de Clientes (Developer Dashboard) | El sistema debe proveer una interfaz web para que los clientes gestionen sus API Keys, monitoreen transacciones y descarguen reportes de conciliación. | **Media** | Empresa Cliente | **Dado** que el desarrollador de la Fintech inicia sesión en el portal,<br>**Cuando** navega a la sección "Credenciales",<br>**Entonces** puede generar, rotar o revocar llaves de API tanto para el entorno de Sandbox como de Producción. |
| **RF-10** | Portal Administrativo (Admin Console) | El sistema debe proveer una consola para el equipo interno de Flux que permita monitorear transacciones totales, gestionar el onboarding de empresas y supervisar alertas del motor de conciliación. | **Media** | Operador Flux | **Dado** que el operador de operaciones está autenticado en la consola,<br>**Cuando** el motor de conciliación detecta una inconsistencia financiera en una transacción,<br>**Entonces** la consola administrativa resalta la transacción con una alerta visual en rojo y permite el ajuste manual documentado. |
