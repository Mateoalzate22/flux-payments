# 📦 Diagrama de Contenedores C2 - Modelo C4

Este documento detalla el **Nivel 2: Diagrama de Contenedores** del modelo **C4** para la plataforma **Flux Payments**. Su propósito es desglosar el sistema en sus componentes de software ejecutables independientes (contenedores), detallando las tecnologías elegidas para cada uno y sus flujos de datos.

---

## 1. Desglose Tecnológico de Contenedores

La arquitectura de Flux Payments adopta un patrón **Cloud-Native basado en Microservicios desacoplados y orientados a eventos** para garantizar alta disponibilidad, baja latencia y mantenibilidad:

### A. Interfaces de Usuario (Frontend)
*   **Portal de Clientes (React / Next.js):** Dashboard web responsivo que permite a las Fintechs monitorear transacciones, descargar reportes de conciliación y administrar llaves de API.
*   **Portal de Administración (React / Vite):** Interfaz para el equipo interno de soporte y conciliadores de Flux Payments.

### B. Capa de Enrutamiento y Seguridad
*   **API Gateway (Kong / Spring Cloud Gateway):** Punto de contacto único para las Fintechs integradas. Maneja el control de tasa de peticiones (rate-limiting), terminación de SSL/TLS 1.3, validación inicial de cabeceras y enrutamiento inteligente.

### C. Contenedores de Backend (Servicios)
*   **Security & Auth Service (Node.js / Express):** Encargado de la validación de tokens JWT, firma digital de respuestas y la rotación segura de credenciales de API.
*   **Motor de Orquestación (Spring Boot / Java 21):** Núcleo lógico del negocio. Controla el flujo transaccional, maneja la concurrencia y orquesta los pasos asíncronos y síncronos de cobro y pago.
*   **Core Ledger Service (Rust / Axum):** Microservicio de alto rendimiento encargado de registrar todas las operaciones contables bajo el principio de partida doble. Garantiza la consistencia inmutable de los balances.
*   **Motor de Conciliación (Python / FastAPI):** Servicio asíncrono optimizado para procesamiento de datos masivo. Descarga, mapea y cruza los archivos de compensación diarios contra el Ledger.

### D. Capa de Almacenamiento y Colas
*   **Bandeja de Mensajería (Apache Kafka):** Bus de eventos de alta velocidad utilizado para la comunicación asíncrona entre microservicios (por ejemplo, disparar webhooks o encolar tareas de conciliación).
*   **Base de Datos Operativa (PostgreSQL):** Almacén relacional para la configuración de clientes, portales, auditoría y bitácora operativa.
*   **Core Ledger Database (PostgreSQL + TimescaleDB):** Base de datos relacional optimizada para series temporales y particionamiento, utilizada para registrar los asientos contables de doble entrada inmutables.

---

## 2. Diagrama de Contenedores C2 (Mermaid)

El siguiente diagrama muestra los límites tecnológicos y de datos dentro del sistema Flux Payments:

```mermaid
graph TB
    classDef ui fill:#e8f5e9,stroke:#4caf50,stroke-width:2px;
    classDef gw fill:#efebe9,stroke:#795548,stroke-width:2px;
    classDef ms fill:#e3f2fd,stroke:#2196f3,stroke-width:2px;
    classDef db fill:#fff3e0,stroke:#ff9800,stroke-width:2px;
    classDef ext fill:#fafafa,stroke:#9e9e9e,stroke-width:2px;

    %% Usuarios Externos
    Fintech[Fintechs / Marketplaces<br/>-- API Consumers --] :::ext
    AdminOps[Operador Flux<br/>-- Soporte / Finanzas --] :::ext

    subgraph Flux Payments Boundary
        %% Gateways y Frontends
        PortalCli[Portal Cliente<br/>-- React / Next.js --<br/>Dashboard y Dev Portal] :::ui
        PortalAdm[Portal Admin<br/>-- React / Vite --<br/>Consola Operaciones] :::ui
        
        Gateway[API Gateway<br/>-- Kong / Gateway --<br/>Enrutamiento y Rate Limiting] :::gw

        %% Microservicios de Aplicación
        AuthService[Security & Auth Service<br/>-- Node.js / Express --<br/>Generación JWT y API Keys] :::ms
        OrchService[Motor de Orquestación<br/>-- Spring Boot / Java --<br/>Orquestador de Transacciones] :::ms
        LedgerService[Core Ledger Service<br/>-- Rust / Axum --<br/>Contabilidad Partida Doble] :::ms
        ReconService[Motor de Conciliación<br/>-- Python / FastAPI --<br/>Conciliador Asíncrono] :::ms

        %% Mensajería y Datastores
        Kafka[Apache Kafka<br/>-- Event Bus --<br/>Eventos de transacciones y estados] :::gw
        DB_Ops[(PostgreSQL Operativo<br/>-- Config & Portales --)] :::db
        DB_Ledger[(PostgreSQL + TimescaleDB<br/>-- Ledger Inmutable --)] :::db
    end

    %% Proveedores de Red
    Karpay[Procesador Karpay SPI<br/>-- API Externa --] :::ext
    Lyra[Procesador Lyra PSE<br/>-- API Externa --] :::ext

    %% Flujos y Conexiones
    Fintech -->|HTTPS REST| Gateway
    AdminOps -->|HTTPS| PortalAdm
    Fintech -->|HTTPS| PortalCli
    
    PortalCli -.->|HTTPS REST| Gateway
    PortalAdm -.->|HTTPS REST| Gateway

    Gateway -->|1. Valida API Keys| AuthService
    Gateway -->|2. Rutea /v1/payouts| OrchService
    Gateway -->|3. Rutea reportes / config| DB_Ops

    OrchService -->|Escribe Asiento Contable| LedgerService
    OrchService -->|Envía órdenes transaccionales| Karpay
    OrchService -->|Envía órdenes de recaudo| Lyra
    OrchService -->|Publica eventos| Kafka

    LedgerService -->|Persistencia Partida Doble| DB_Ledger
    
    ReconService -->|Consume eventos y lee reportes SFTP| Kafka
    ReconService -->|Consulta estados transaccionales| DB_Ledger
    ReconService -->|Escribe resultados y marcas| DB_Ops

    class PortalCli,PortalAdm ui;
    class Gateway,Kafka gw;
    class AuthService,OrchService,LedgerService,ReconService ms;
    class DB_Ops,DB_Ledger db;
    class Fintech,AdminOps,Karpay,Lyra ext;
```

---

## 3. Estrategia de Desacoplamiento y Resiliencia
*   **Rust para Core Ledger:** Se selecciona Rust debido a su altísimo rendimiento, seguridad de memoria y ausencia de recolector de basura (garbage collector), garantizando tiempos de respuesta ultrabajos e inmutabilidad garantizada a nivel de hilo de ejecución.
*   **Kafka para Event-Driven Flow:** La publicación de eventos de cambio de estado (ej: `PAYMENT_LIQUIDATED`) en Kafka permite que otros sistemas (como el de webhooks y el portal web) reaccionen asíncronamente en tiempo real sin bloquear el flujo principal de cobro.
*   **Base de datos segregada (CQRS):** La base de datos operativa está físicamente separada del Core Ledger transaccional para evitar la contención de disco y asegurar que las consultas masivas de los portales no afecten la latencia de procesamiento de pagos.
