# 🧩 Diagrama de Componentes C3 - Modelo C4

Este documento detalla el **Nivel 3: Diagrama de Componentes** del modelo **C4** para el contenedor **Motor de Orquestación (Spring Boot / Java 21)**. Su propósito es desglosar la estructura lógica interna de este microservicio, detallando la interacción de sus componentes para el procesamiento en tiempo real de transacciones SPI.

---

## 1. Descripción de los Componentes Internos del Motor

El Motor de Orquestación se implementa bajo los principios de **Arquitectura Hexagonal (Puertos y Adaptadores)** para asegurar que la lógica de negocio esté completamente aislada de la infraestructura externa (base de datos, proveedores, etc.):

*   **TransactionController (Controlador REST):** Componente controlador de API que expone los endpoints transaccionales (como `/v1/payouts`). Deserializa, mapea y valida de forma estricta los datos JSON recibidos.
*   **SecurityFilter (Filtro de Seguridad):** Filtro que intercepta la petición a nivel de Servlet para validar la firma de la petición, verificar que las credenciales no estén bloqueadas y aplicar rate-limiting.
*   **OrchestrationEngine (Lógica de Dominio):** El cerebro del servicio. Implementa la máquina de estados de la transacción (`PENDIENTE`, `RESERVADO`, `ENVIADO`, `EXITOSO`, `REVERSADO`). Ejecuta el flujo transaccional y maneja el control transaccional de reintentos.
*   **LedgerClient (Adaptador Ledger):** Cliente HTTP/gRPC de alto rendimiento encargado de comunicarse con el microservicio externo **Core Ledger Service** para realizar afectación de balances.
*   **KarpayClient (Adaptador SPI):** Adaptador de infraestructura que traduce el modelo de datos interno de Flux al formato específico (ISO 20022 o JSON firmado) requerido por el Sandbox/API de **Karpay** para conectarse al SPI.
*   **KafkaEventPublisher (Adaptador Eventos):** Componente encargado de serializar e inyectar eventos de dominio en tópicos dedicados de **Apache Kafka** (como `transaction-completed` o `transaction-failed`).

---

## 2. Diagrama de Componentes C3 (Mermaid)

El siguiente diagrama ilustra la arquitectura interna del microservicio de orquestación y sus dependencias perimetrales:

```mermaid
graph TD
    classDef port fill:#eceff1,stroke:#607d8b,stroke-width:2px;
    classDef comp fill:#e8f4fd,stroke:#1e88e5,stroke-width:2px;
    classDef ext fill:#fafafa,stroke:#9e9e9e,stroke-width:2px;

    %% Fronteras y Peticiones Externas
    Gateway[API Gateway]<br/>-- Envía Solicitud Ruteada --:::ext
    
    subgraph Orchestration Service Container
        %% Controlador y Filtros (Drivers)
        SecFilter[SecurityFilter<br/>-- Spring Security --<br/>Valida autenticación y firmas] :::comp
        TxController[TransactionController<br/>-- Spring REST Controller --<br/>Maneja endpoints transaccionales] :::comp

        %% Núcleo de Lógica (Domain Core)
        Engine[OrchestrationEngine<br/>-- Domain Service --<br/>Controlador de Máquina de Estados] :::comp

        %% Adaptadores de Salida (Driven)
        LedgerClient[LedgerClientAdapter<br/>-- Feign Client gRPC --<br/>Comunica con Core Ledger Service] :::comp
        KarpayClient[KarpayClientAdapter<br/>-- WebClient HTTP --<br/>Comunica con Karpay SPI] :::comp
        EventPub[KafkaEventPublisher<br/>-- Spring Kafka Template --<br/>Publica eventos transaccionales] :::comp
    end

    %% Componentes Externos al Microservicio
    LedgerService[Core Ledger Service API] :::ext
    KarpayAPI[Karpay API Sandbox] :::ext
    KafkaTopic[Kafka Topic: transaction-events] :::ext

    %% Relaciones y Direcciones
    Gateway -->|1. Petición HTTPS POST| SecFilter
    SecFilter -->|2. Petición Validada| TxController
    
    TxController -->|3. Crea comando de transacción| Engine
    
    Engine -->|4. Reserva fondos en balance| LedgerClient
    LedgerClient -->|gRPC call| LedgerService
    
    Engine -->|5. Envía orden de pago inmediato| KarpayClient
    KarpayClient -->|HTTPS POST| KarpayAPI
    
    Engine -->|6. Emite evento transaccional| EventPub
    EventPub -->|Produce Mensaje| KafkaTopic

    class SecFilter,TxController,Engine,LedgerClient,KarpayClient,EventPub comp;
    class Gateway,LedgerService,KarpayAPI,KafkaTopic ext;
```

---

## 3. Flujo Lógico de un Pago Inmediato (Payout)

1.  **Recepción:** El `TransactionController` recibe el POST en `/v1/payouts` e invoca la validación de formato.
2.  **Autorización:** El `SecurityFilter` valida que el `Client-ID` y la firma de la petición coincidan con la configuración del cliente B2B.
3.  **Inicio de Estado:** Se crea la transacción en base de datos local con estado `PENDIENTE`.
4.  **Reserva en el Ledger:** El `OrchestrationEngine` invoca al `LedgerClient` vía gRPC. Este se comunica con el `Core Ledger Service` para restar el saldo de la Fintech y sumarlo a una cuenta transaccional temporal (débito de balance). El balance del cliente queda disminuido y a salvo.
5.  **Envío a Red:** El `OrchestrationEngine` invoca al `KarpayClient` pasando los datos. Si Karpay responde con éxito en menos de 2 segundos, el estado cambia a `COMPLETADO`.
6.  **Confirmación y Evento:** Se invoca al `LedgerClient` para asentar definitivamente el movimiento contable (cierre de cuenta transaccional temporal) y el `EventPublisher` publica el evento en Apache Kafka para notificar al cliente vía webhook.
