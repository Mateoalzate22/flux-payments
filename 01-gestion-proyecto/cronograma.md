# 📅 Cronograma y Plan de Trabajo

El desarrollo de **Flux Payments** se proyecta a un término de **10 meses**, estructurado en 6 fases estratégicas que abarcan desde el diseño conceptual hasta el lanzamiento al mercado nacional.

---

## 📊 Diagrama de Gantt del Proyecto (Mermaid)

El siguiente diagrama representa el plan de trabajo interactivo del proyecto. Las tareas están organizadas cronológicamente por fases de desarrollo e integraciones críticas:

```mermaid
gantt
    title Plan de Trabajo - Flux Payments (10 Meses)
    dateFormat  YYYY-MM-DD
    axisFormat  Mes %m
    
    section Fase 1: Arquitectura y APIs
    Diseño de arquitectura y vistas C4      :active, f1_1, 2026-06-01, 2026-06-30
    Definición de especificación OpenAPI    :f1_2, 2026-06-15, 2026-07-15
    Configuración de infraestructura cloud :f1_3, 2026-07-01, 2026-07-31
    
    section Fase 2: Desarrollo Core
    Desarrollo del API Gateway              :f2_1, 2026-08-01, 2026-09-15
    Desarrollo del Motor de Orquestación   :f2_2, 2026-08-15, 2026-10-15
    Desarrollo del Core Ledger              :f2_3, 2026-09-01, 2026-10-31
    
    section Fase 3: Integración y Portales
    Integración con Karpay (Conector SPI)   :f3_1, 2026-11-01, 2026-12-15
    Integración con PSE (Lyra)             :f3_2, 2026-11-15, 2026-12-31
    Desarrollo de Portales Web (Clientes/Ops):f3_3, 2026-11-01, 2027-01-15
    
    section Fase 4: QA y Seguridad
    Pruebas unitarias e integración interna :f4_1, 2027-01-15, 2027-02-15
    Pruebas de carga e inmutabilidad Ledger :f4_2, 2027-02-01, 2027-02-28
    Auditoría externa de ciberseguridad    :f4_3, 2027-02-10, 2027-02-28
    
    section Fase 5: Piloto Controlado
    Onboarding de 2 Fintechs piloto         :f5_1, 2027-03-01, 2027-03-15
    Operación piloto (Volumen controlado)   :f5_2, 2027-03-15, 2027-03-31
    
    section Fase 6: Ajustes y Lanzamiento
    Ajustes de estabilidad y feedback       :f6_1, 2027-04-01, 2027-04-15
    Lanzamiento comercial y onboarding B2B  :active, f6_2, 2027-04-15, 2027-04-30
```

---

## 🗒️ Desglose de Entregables por Hito y Mes

| Fase | Período Temporal | Entregables Clave | Responsable Principal |
| :--- | :--- | :--- | :--- |
| **Fase 1: Diseño e Infraestructura** | **Mes 1 – Mes 2** | *   Modelado C4 aprobado (C1, C2, C3).<br>*   Contratos API (OpenAPI 3.0 / Swagger JSON).<br>*   Entornos de Sandbox AWS creados (IaC - Terraform). | Arquitecto de Software & DevOps |
| **Fase 2: Desarrollo Core** | **Mes 3 – Mes 5** | *   API Gateway desplegado con filtros de seguridad.<br>*   Core Ledger registrando movimientos financieros.<br>*   Motor de Orquestación manejando transacciones. | Backend Developers |
| **Fase 3: Integraciones** | **Mes 6 – Mes 7** | *   Conectores activos con Sandbox Karpay y Lyra.<br>*   Portal de Clientes permitiendo visualización de transacciones.<br>*   Checkout de prueba funcionando. | Backend & Frontend Developers |
| **Fase 4: QA y Seguridad** | **Mes 8** | *   Cobertura de código unitario > 85%.<br>*   Reporte de Pentesting cerrado.<br>*   Pruebas de latencia transaccional (< 2 segundos). | DevOps & Ingenieros de QA |
| **Fase 5: Onboarding & Piloto** | **Mes 9** | *   2 clientes reales procesando transacciones.<br>*   Monitoreo operativo 24/7 activo.<br>*   Comprobación del 80% de conciliación automática. | PM, DevOps & Operaciones |
| **Fase 6: Lanzamiento Comercial** | **Mes 10** | *   Pase a producción completo.<br>*   Campaña comercial activa.<br>*   Soporte B2B estructurado. | CEO, Ventas & Alianzas |

---

## ⚠️ Dependencias Críticas en el Cronograma

1.  **Conexión externa (Karpay):** El inicio de la Fase 3 depende directamente de la entrega de credenciales estables de Sandbox por parte de Karpay (SPI) en el Mes 6.
2.  **Seguridad (Fase 4):** Ningún cliente puede iniciar el piloto en el Mes 9 si la auditoría de seguridad del Mes 8 encuentra vulnerabilidades con severidad "Crítica" o "Alta".
3.  **Regulación Financiera:** El pase final a producción del Mes 10 está condicionado a la aprobación del marco legal contractual de corresponsalía digital.
