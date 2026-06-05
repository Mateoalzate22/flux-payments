# 📓 Historias de Usuario (User Stories)

Este documento detalla las Historias de Usuario (US) del proyecto **Flux Payments**, siguiendo el enfoque metodológico ágil. Cada historia está diseñada bajo el formato estándar de usuario e incorpora sus respectivos criterios de aceptación bajo el modelo **BDD (Behavior-Driven Development: Dado/Cuando/Entonces)** para guiar de manera precisa el desarrollo y validación de software.

---

## 🗂️ Listado de Historias de Usuario

### 1. US-01: Integración Rápida y Procesamiento de Dispersión (Payouts)
> **Como** desarrollador de una plataforma Fintech de crédito,  
> **Quiero** realizar dispersión de fondos a mis usuarios mediante una sola solicitud a la API de Flux,  
> **Para** desembolsar créditos de forma inmediata 24/7 sin integrarme con la infraestructura compleja de cada banco.

*   **Criterios de Aceptación (BDD):**
    *   **Escenario 1: Desembolso Exitoso en Tiempo Real**
        *   **Dado** que el desarrollador cuenta con credenciales API válidas en producción y fondos suficientes en su balance,
        *   **Cuando** envía una solicitud HTTP POST a `/v1/payouts` con datos válidos de cuenta bancaria destino y monto de $150,000 COP,
        *   **Entonces** el gateway debe procesar, orquestar la transacción con Karpay (SPI) y responder un código `HTTP 201 Created` con estado de transacción `EXITOSO` en menos de 2.0 segundos.
    *   **Escenario 2: Intento de Desembolso con Saldo Insuficiente**
        *   **Dado** que la cuenta del cliente B2B tiene un balance disponible de $50,000 COP,
        *   **Cuando** el desarrollador intenta enviar una orden de pago por $100,000 COP,
        *   **Entonces** la API debe responder inmediatamente con código `HTTP 402 Payment Required` y un mensaje detallado informando la insuficiencia de fondos, bloqueando la transacción antes de enviarla a la red de pagos.

---

### 2. US-02: Recaudo de Pagos a través del Checkout Web
> **Como** administrador de un Marketplace,  
> **Quiero** integrar un componente de Checkout seguro provisto por Flux,  
> **Para** que los compradores de mi plataforma puedan realizar transferencias en tiempo real (SPI o PSE) de forma intuitiva.

*   **Criterios de Aceptación (BDD):**
    *   **Escenario 1: Renderizado Seguro del Checkout**
        *   **Dado** que el comprador hace clic en el botón de pago en la web del Marketplace,
        *   **Cuando** la página web solicita el componente de Checkout enviando la referencia y el monto,
        *   **Entonces** Flux debe renderizar una interfaz responsiva, segura (HTTPS) y precargada con los métodos de pago (Transferencia SPI / PSE) en menos de 1.0 segundo.
    *   **Escenario 2: Notificación Exitosa de Cobro**
        *   **Dado** que el comprador completa con éxito el flujo de autenticación y transferencia en su banco,
        *   **Cuando** la red del SPI confirma a Flux la liquidación del recaudo,
        *   **Entonces** el Checkout debe mostrar una pantalla de confirmación al comprador, y asíncronamente Flux debe enviar un HTTP POST (Webhook) firmado con SHA-256 a la plataforma del Marketplace para actualizar el pedido como pagado.

---

### 3. US-03: Automatización del Cierre Operativo y Conciliación
> **Como** líder del equipo de contabilidad y operaciones en una gran empresa,  
> **Quiero** que la plataforma realice el cruce y la conciliación contable de pagos recibidos contra los reportes de red automáticamente,  
> **Para** eliminar las planillas manuales en Excel y mitigar los errores humanos en el cierre contable diario.

*   **Criterios de Aceptación (BDD):**
    *   **Escenario 1: Ejecución y Cruce Automatizado de Transacciones**
        *   **Dado** que el cron-job de Flux descarga el reporte de liquidación del SFTP externo a la medianoche,
        *   **Cuando** el Motor de Conciliación ejecuta el cruce contra las transacciones registradas en el Core Ledger de Flux,
        *   **Entonces** el sistema debe emparejar y cambiar el estado a `CONCILIADA` de al menos el **80% de los registros** coincidentes, guardando la trazabilidad de auditoría.
    *   **Escenario 2: Manejo de Inconsistencias (Discrepancias)**
        *   **Dado** que existe una transacción en el reporte externo que presenta una diferencia en el monto registrado internamente en Flux,
        *   **Cuando** el motor ejecuta la validación y no encuentra coincidencia exacta,
        *   **Entonces** debe catalogar la transacción con estado `DISCREPANCIA`, registrarla en la bandeja de auditoría contable y enviar una alerta de alta prioridad a la consola administrativa de los operadores de Flux.

---

### 4. US-04: Monitoreo en Tiempo Real desde el Dashboard de Clientes
> **Como** Product Manager de una Fintech integrada,  
> **Quiero** visualizar un tablero gráfico en tiempo real con estadísticas y estados de mis cobros y pagos,  
> **Para** monitorear la salud transaccional de mi operación y tomar decisiones de negocio informadas.

*   **Criterios de Aceptación (BDD):**
    *   **Escenario 1: Carga de Métricas Clave**
        *   **Dado** que el Product Manager ha iniciado sesión de forma segura en el Portal de Clientes,
        *   **Cuando** accede al dashboard principal de estadísticas,
        *   **Entonces** la interfaz debe cargar y refrescar las gráficas de: *Volumen Transaccional Procesado (COP)*, *Tasa de Éxito de Transacciones (%)* e *Historial Reciente de Operaciones* con una latencia de renderizado inferior a 1.5 segundos.
