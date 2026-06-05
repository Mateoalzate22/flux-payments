# 🌐 Diagrama de Contexto C1 - Modelo C4

Este documento detalla el **Nivel 1: Diagrama de Contexto** del modelo **C4** para la plataforma **Flux Payments**. Su propósito es mostrar la frontera del sistema, los usuarios principales que interactúan con él y las dependencias con plataformas o redes financieras externas bajo una perspectiva de alto nivel.

---

## 1. Descripción de Actores y Sistemas del Ecosistema

*   **Flux Payments (Sistema Core):** El sistema transaccional objeto de diseño. Actúa como gateway Fintech IaaS unificado, proveyendo endpoints API seguros, motor de orquestación, portales de usuario y motor de conciliación automática.
*   **Empresas Clientes (Fintechs, Marketplaces, ERPs):** Sistemas de software de terceros integrados vía API que gatillan flujos transaccionales (dispersión de créditos, cobros recurrentes, solicitudes de conciliación).
*   **Operador Flux (Usuario Interno):** Equipo de soporte y operaciones contables de Flux Payments que supervisa transacciones y gestiona el onboarding empresarial.
*   **Karpay - Procesador SPI (Sistema Externo):** Proveedor tecnológico que actúa como gateway directo hacia el Sistema de Pagos Inmediatos (SPI) del Banco de la República.
*   **Lyra - Procesador PSE (Sistema Externo):** Entidad pasarela utilizada para procesar recaudos en tiempo real mediante transferencias desde cuentas de ahorro/corriente tradicionales.
*   **Bancos del Sistema Financiero (Sistemas Externos):** Entidades financieras comerciales en Colombia (Bancolombia, Davivienda, etc.) receptoras finales o emisoras de los fondos del usuario.

---

## 2. Diagrama de Contexto C1 (Mermaid)

El siguiente diagrama modela la relación de alto nivel del sistema en su entorno transaccional:

```mermaid
graph TD
    classDef cliente fill:#c8e6c9,stroke:#2e7d32,stroke-width:2px;
    classDef core fill:#bbdefb,stroke:#0d47a1,stroke-width:2px;
    classDef ext fill:#fff9c4,stroke:#fbc02d,stroke-width:2px;

    %% Actores y Usuarios
    Fintech[Fintechs, Marketplaces & ERPs<br/>-- Clientes B2B via API --] :::cliente
    UserOps[Operadores de Flux Payments<br/>-- Personal Interno --] :::cliente
    
    %% Sistema Objeto de Estudio
    FluxSystem[⚡ FLUX PAYMENTS<br/>-- Plataforma Fintech IaaS --<br/>API Gateway, Ledger, Orquestador y Conciliador] :::core
    
    %% Sistemas Externos
    Karpay[Karpay SPI Gateway<br/>-- Proveedor de Red SPI --] :::ext
    Lyra[Lyra PSE Gateway<br/>-- Proveedor de Red PSE --] :::ext
    Bancos[Bancos Colombianos<br/>-- Core Bancario Destino --] :::ext

    %% Relaciones y Direcciones de Datos
    Fintech -->|1. Consume API REST / Recibe Webhooks| FluxSystem
    UserOps -->|2. Monitorea transacciones y aprueba conciliaciones| FluxSystem
    
    FluxSystem -->|3. Envía órdenes de pago inmediato| Karpay
    FluxSystem -->|4. Procesa cobros y recaudos batch/línea| Lyra
    
    Karpay -->|5. Compensa y liquida transferencias 24/7| Bancos
    Lyra -->|6. Realiza débitos a cuentas bancarias| Bancos

    class Fintech,UserOps cliente;
    class FluxSystem core;
    class Karpay,Lyra,Bancos ext;
```

---

## 3. Límites e Interacciones Clave

1.  **Frontera Tecnológica:** Flux Payments no administra cuentas de depósito bancario reales para los usuarios finales. Delega la custodia de los fondos de compensación en fideicomisos bancarios autorizados y se enfoca estrictamente en la **capa de software de orquestación, Ledger de control e integraciones**.
2.  **Seguridad Perimetral:** Toda la comunicación entre los clientes B2B y Flux está encriptada y restringida por un API Gateway dedicado, protegiendo las redes internas de ataques de denegación de servicio.
3.  **Trazabilidad Extremo a Extremo:** La traza transaccional que entra desde las Fintechs se mapea con una referencia de transacción única del SPI para garantizar el rastreo en el Banco de la República.
