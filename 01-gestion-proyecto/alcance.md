# 🎯 Alcance del Proyecto

Este documento establece de forma clara y delimitada el alcance del proyecto **Flux Payments**, especificando qué elementos y funcionalidades están incluidos dentro del desarrollo del Gateway, y cuáles quedan explícitamente excluidos.

---

## 1. Declaración del Alcance

El proyecto comprende el diseño de la arquitectura de software, el desarrollo de las APIs, la lógica de orquestación, el registro financiero inmutable (Ledger) y las interfaces de usuario (portales web) para proporcionar una plataforma de infraestructura financiera (Fintech IaaS) que simplifique la conexión con el Sistema de Pagos Inmediatos (SPI) en Colombia.

---

## 2. Elementos Incluidos (In-Scope)

El desarrollo del sistema comprende los siguientes componentes tecnológicos y funcionales:

### A. Capa de Integración y API
*   **API Gateway:** Punto de entrada único para las solicitudes de los clientes. Incluye enrutamiento, rate-limiting, y validación de tokens de autenticación segura.
*   **API de Clientes (REST & Webhooks):** Endpoints normalizados en formato JSON para la creación de pagos, consultas de saldo, estado de transacciones y registro de webhooks de notificación inmediata.
*   **Checkout UI:** Componente de interfaz web interactivo y seguro para el procesamiento de cobros (recaudos) embebible en los sitios de los clientes.

### B. Capa de Lógica de Negocio y Orquestación
*   **Motor de Orquestación:** Lógica encargada de coordinar los pasos transaccionales (validación de fondos, autorización, envío de comandos, reintentos en caso de caídas de red y rollback de operaciones fallidas).
*   **Motor de Conciliación Automática:** Algoritmo que compara periódicamente los estados de transacciones internas del Core Ledger contra los reportes transaccionales de la red SPI/Karpay para automatizar el cierre contable diario.
*   **Servicios de Seguridad y Fraude:** Módulo que ejecuta análisis básicos de patrones de transacciones sospechosas y gestiona el cifrado de datos sensibles (números de cuenta, identificaciones).

### C. Capa de Registro (Ledger)
*   **Core Ledger:** Sistema de registro financiero inmutable de tipo doble entrada (double-entry bookkeeping) que almacena la traza de todas las transacciones de pago y cobro con marcas de tiempo y firma criptográfica para evitar alteraciones de datos.

### D. Interfaces de Usuario (Portales Web)
*   **Portal de Clientes (Fintech Portal):** Dashboard web interactivo para desarrolladores y administradores de las Fintechs donde pueden visualizar transacciones en tiempo real, descargar reportes de conciliación, configurar Webhooks y gestionar sus credenciales de API (Sandbox y Producción).
*   **Portal de Operaciones (Admin Portal):** Consola para el equipo de soporte y operaciones internas de Flux Payments para monitorear el estado de salud de la plataforma, gestionar clientes B2B, y dar soporte manual ante disputas de transacciones.

### E. Integraciones Externas
*   **Conector SPI (Karpay):** Implementación de los protocolos de comunicación y mensajería requeridos para comunicarse con el procesador Karpay, quien realiza el puente hacia la red SPI del Banco de la República.
*   **Integración PSE:** Mecanismo complementario de recaudo que interactúa con la pasarela PSE (proveedor Lyra) para transferencias directas desde cuentas de ahorro/corriente bancarias tradicionales.

---

## 3. Elementos Excluidos (Out-of-Scope)

Los siguientes componentes, sistemas y procesos quedan fuera del alcance de este proyecto y su desarrollo no será ejecutado por el equipo:

*   **Desarrollo de la Red SPI del Banco de la República:** Flux es un gateway de conexión; no tiene injerencia en el desarrollo, políticas o arquitectura del núcleo del SPI nacional.
*   **Infraestructura de Red y Servidores Físicos de los Bancos:** No se incluye la modificación ni el mantenimiento de los sistemas legacy de las entidades financieras destino.
*   **Sistemas de Core Bancario Interno:** Flux no implementa cuentas bancarias reales, solo realiza el ruteo e intermediación tecnológica. Los fondos reales reposan en las entidades financieras del cliente.
*   **Sistemas Contables de los Clientes:** La exportación de datos de conciliación se entregará en formatos estándar (CSV/JSON), pero la integración directa en el software ERP/contable interno de cada cliente (ej. SAP, Oracle, Siigo) corre por cuenta de cada empresa.
*   **Regulación y Licenciamiento Financiero Directo:** El proyecto no comprende el trámite legal de obtención de licencias de entidad financiera (ej. SEDPE o Banco) ante la Superfinanciera de Colombia. Flux opera bajo el modelo tecnológico de corresponsalía digital e infraestructura IaaS.

---

## 4. Criterios de Aceptación del Alcance
El alcance se considerará completamente cubierto una vez se verifique el paso a paso del flujo de pago en tiempo real (SIPOC) y se cuente con la documentación correspondiente de arquitectura C4 y planes de contingencia ante fallos externos.
