# 📜 Acta de Constitución del Proyecto (Project Charter)

> **Proyecto:** Flux Payments - Plataforma Fintech IaaS (Integración SPI)  
> **Fecha:** 22 de Mayo de 2026  
> **Patrocinador Principal:** Junta Directiva - Flux Payments Corp.  
> **Director de Arquitectura & Proyecto:** Mateo Alzate (Grupo 1)  

---

## 1. Justificación y Propósito del Proyecto

La infraestructura de pagos en Colombia se encuentra en una fase de transformación histórica con el lanzamiento del **Sistema de Pagos Inmediatos (SPI)** por parte del Banco de la República. El SPI permite transferencias de dinero en tiempo real, con disponibilidad 24/7 y confirmación instantánea. 

Sin embargo, para la gran mayoría de las empresas (Fintechs de crédito, Marketplaces, ERPs, y grandes distribuidores), la **integración directa con el SPI es inviable** debido a:
*   Procesos de certificación de seguridad y conectividad bancaria sumamente complejos y fragmentados.
*   Costos de ingeniería prohibitivos y prolongados (integraciones que toman de **6 a 12 meses**).
*   Procesos operativos e ineficiencias de conciliación que se realizan de forma manual.

**Flux Payments** nace para solventar esta barrera, posicionándose como un **Gateway o pasarela Fintech Infrastructure-as-a-Service (IaaS)**. A través de una API unificada, simple y estandarizada, Flux abstrae la complejidad y permite a las empresas conectarse al SPI en semanas, en lugar de meses, automatizando la conciliación y el procesamiento seguro de transacciones de pago y recaudo inmediato.

---

## 2. Descripción del Producto y Objetivos

La solución consiste en una plataforma Cloud Native, modular y de alta disponibilidad que expone APIs REST y Webhooks a los clientes y realiza la traducción de protocolos, firma digital y enrutamiento hacia la red del SPI mediante proveedores autorizados (Karpay).

### Objetivos Generales:
1.  **Simplificación Técnica:** Reducir la brecha de integración a SPI de meses a un rango de **2 a 4 semanas**.
2.  **Operación Eficiente:** Automatizar el **80% o más** de los procesos de conciliación contable de pagos recibidos.
3.  **Habilitación de Negocio:** Habilitar a las Fintechs y Marketplaces para realizar dispersión (payouts) y recaudos masivos 24/7/365 en tiempo real.

---

## 3. Equipo y Roles del Proyecto

Para la ejecución exitosa de Flux Payments, se define una estructura organizacional multidisciplinaria dividida en tres pilares clave:

| Equipo | Rol / Cargo | Responsabilidades Clave |
| :--- | :--- | :--- |
| **Tecnología (Ingeniería)** | **Arquitectos de Software** | Diseño conceptual, definición de patrones de integración, seguridad criptográfica y selección de tecnologías. |
| | **Ingenieros Backend** | Desarrollo de servicios core (Ledger, Orquestación, Conectores) y API Gateway. |
| | **Ingenieros Frontend** | Desarrollo del portal de clientes y el portal operativo interno. |
| | **Ingenieros DevOps** | Configuración de infraestructura Cloud, CI/CD, monitoreo de latencia y escalabilidad. |
| **Producto** | **Product Manager** | Definición del roadmap, priorización del backlog de producto y gestión del ciclo de vida de la API. |
| | **UX/UI Designer** | Diseño de la experiencia de integración de desarrolladores (Dev Portal) y flujos del Checkout. |
| **Negocio & Cumplimiento**| **Equipo Comercial B2B** | Adquisición de clientes empresariales, Fintechs y Marketplaces. |
| | **Alianzas Estratégicas** | Negociación con entidades bancarias y proveedores de red (Karpay, Lyra). |
| | **Cumplimiento y Legal** | Monitoreo normativo ante la Superfinanciera de Colombia (SFC) y lavado de activos (SARLAFT). |

---

## 4. Hitos Principales del Proyecto

Se ha definido un cronograma tentativo de 10 meses con los siguientes hitos de control:

```
[M1-M2: Arquitectura y APIs] ──> [M3-M5: Desarrollo Core] ──> [M6-M7: Integración SPI] ──> [M8: QA & Sec] ──> [M9: Piloto] ──> [M10: Lanzamiento]
```

*   **Hito 1 (Mes 2):** Firma de la arquitectura conceptual y especificación OpenAPI final.
*   **Hito 2 (Mes 5):** Finalización del desarrollo del Core Ledger, Motor de Orquestación y API Gateway.
*   **Hito 3 (Mes 7):** Integración técnica y pruebas de conectividad exitosas con Karpay (SPI) y Lyra (PSE).
*   **Hito 4 (Mes 8):** Finalización de auditoría de seguridad y pruebas de carga (cero vulnerabilidades críticas).
*   **Hito 5 (Mes 9):** Cierre exitoso del piloto controlado con 2 clientes Fintech seleccionados.
*   **Hito 6 (Mes 10):** Despliegue comercial y pase al entorno productivo.

---

## 5. Presupuesto y Viabilidad Inicial
El proyecto cuenta con un patrocinio de capital de riesgo y presupuesto asignado para infraestructura cloud (AWS), salarios del equipo técnico y licencias del sandbox transaccional. La viabilidad se fundamenta en la alta demanda del ecosistema Fintech en Colombia (con más de 300 fintechs registradas con necesidad de pagos inmediatos).

---

## 6. Criterios de Aceptación del Proyecto

El patrocinador y el docente evaluarán el éxito del proyecto conforme a:
*   **Completitud Técnica:** Exposición de endpoints API documentados en OpenAPI 3.0 para cobro, pago y conciliación.
*   **Calidad de la Arquitectura:** Arquitectura modular desacoplada con diagramas del modelo C4 y documentación de riesgos.
*   **Velocidad de Procesamiento:** Procesamiento de pagos con latencia inferior a los **2 segundos** de extremo a extremo.
*   **Seguridad:** Uso de firmas SHA-256 para comunicación, autenticación OAuth2 y Ledger inmutable auditado sin discrepancias financieras.
