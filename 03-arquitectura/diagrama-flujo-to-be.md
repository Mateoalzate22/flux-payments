# ⚡ Diagrama de Flujo TO-BE (Situación Propuesta)

Este documento modela el estado objetivo (**TO-BE**) optimizado y habilitado por la plataforma **Flux Payments** para el procesamiento inmediato de pagos y conciliación automatizada en Colombia.

---

## 1. Descripción de la Solución Propuesta TO-BE
El ecosistema propuesto por Flux Payments transforma radicalmente la experiencia transaccional y operativa de las empresas:
1.  **API Unificada:** En lugar de realizar integraciones separadas, la Fintech se conecta una sola vez a la API REST de Flux en un período de **2 a 4 semanas**.
2.  **Operación 24/7 en Tiempo Real:** Las transferencias y recaudos se ejecutan sobre la red de Pagos Inmediatos (SPI) a través del procesador Karpay, logrando liquidaciones en menos de **2 segundos** sin importar días feriados o fines de semana.
3.  **Registro Consistente (Core Ledger):** Cada transacción queda registrada instantáneamente en un libro contable inmutable de doble entrada que garantiza la inmutabilidad física y lógica de la data.
4.  **Cierre Automático:** El Motor de Conciliación se ejecuta en segundo plano cruzando archivos de Karpay de forma desatendida, logrando conciliar de forma automática **más del 80%** de los movimientos y notificando inmediatamente las discrepancias al panel operativo de Flux.

---

## 2. Diagrama de Flujo TO-BE (Mermaid)

El siguiente flujo demuestra la agilidad, automatización e inmediatez de la solución transaccional de Flux:

```mermaid
flowchart TD
    classDef flux fill:#bbdefb,stroke:#0d47a1,stroke-width:2px;
    classDef spi fill:#fff9c4,stroke:#fbc02d,stroke-width:2px;
    classDef cliente fill:#d1e7dd,stroke:#0f5132,stroke-width:2px;

    Inicio([Cliente solicita desembolso o pago inmediato]) :::cliente --> API[Fintech envía POST /v1/payouts a Flux Payments] :::cliente
    
    API --> GW[API Gateway: Valida firma & autentica credenciales] :::flux
    GW --> ORQ[Motor de Orquestación: Valida balance en Core Ledger] :::flux
    
    ORQ -->|Fondos Suficientes| LedgerPending[Registra débito transaccional temporal en Core Ledger] :::flux
    
    LedgerPending --> SPICon[Conector SPI traduce y firma mensaje transaccional] :::flux
    SPICon --> Karpay[Procesador Karpay envía transacción a la Red SPI] :::spi
    
    Karpay -->|Liquidación en tiempo real < 2s| ResSPI{¿Liquidado con éxito?} :::spi
    
    ResSPI -- Sí --> FinalizeLedger[Core Ledger asienta saldo definitivo como EXITOSO] :::flux
    FinalizeLedger --> Webhook[Notifica asíncronamente al Cliente vía Webhook y API] :::flux
    Webhook --> FinExito([Beneficiario recibe dinero y Cliente es notificado]) :::cliente
    
    ResSPI -- No --> RollbackLedger[Core Ledger reversa fondos bloqueados al balance del cliente] :::flux
    RollbackLedger --> NotifyFail[API responde con error detallado de la red] :::flux
    NotifyFail --> FinFallo([Transacción declinada con saldo a salvo]) :::cliente

    %% Proceso de Conciliación en Paralelo Asíncrono
    Cron[Cron Diario 00:15 AM] :::flux --> Reconciler[Motor de Conciliación descarga reporte SPI y cruza con Ledger] :::flux
    Reconciler -->|Cruce exitoso del 80%+| AutoConcile[Marca transacciones como CONCILIADAS automáticamente] :::flux
    Reconciler -->|Discrepancias encontradas| AlertOps[Publica alertas en consola Admin para soporte] :::flux

    class GW,ORQ,LedgerPending,SPICon,FinalizeLedger,Webhook,RollbackLedger,NotifyFail,Cron,Reconciler,AutoConcile,AlertOps flux;
    class Karpay,ResSPI spi;
```

---

## 3. Beneficios y Valor Agregado Habilitado
*   **Eficiencia en Costos:** Al automatizar la conciliación transaccional y el control de balance por Ledger, el equipo financiero del cliente reduce en un 90% el tiempo dedicado a actividades manuales repetitivas.
*   **Velocidad de Comercialización (Time-to-Market):** Las Fintechs pueden lanzar sus productos de crédito o cobro de manera inmediata al mercado al integrarse en pocas semanas a un Sandbox estable y completamente documentado.
*   **Resiliencia Operativa:** El desacoplamiento mediante colas de mensajería asíncronas y el patrón de reversión del Ledger garantizan la consistencia absoluta de los fondos de los clientes ante cualquier inestabilidad de la red financiera nacional.
