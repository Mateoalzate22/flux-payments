# 🎯 Criterios de Aceptación del Sistema (UAT)

Este documento define los **Criterios de Aceptación del Sistema** que deben cumplirse estrictamente antes de autorizar el paso a producción (Go-Live) de **Flux Payments**. Estos criterios se encuentran explícitamente enlazados con los **Objetivos SMART** de negocio y los pasos del **SIPOC transaccional** para garantizar trazabilidad absoluta.

---

## 1. Matriz de Trazabilidad: Objetivos SMART ↔ SIPOC ↔ Criterios de Aceptación

Para asegurar el éxito del proyecto, enlazamos cada elemento estratégico con sus respectivos requerimientos técnicos y criterios de validación:

| Objetivo SMART | Paso Relacionado (SIPOC) | Requerimientos (RF / RNF) | Criterios de Aceptación del Sistema | Método de Validación |
| :--- | :--- | :--- | :--- | :--- |
| **SMART-01:** Reducir tiempo de integración a 2-4 semanas. | **Paso 1:** El cliente envía la solicitud a la API de Flux. | `RF-04`, `RF-09`, `RNF-08` | *   El 100% de los endpoints públicos del gateway debe estar documentados de forma interactiva en Swagger OpenAPI 3.0.<br>*   El Sandbox debe responder mocks estables emulando Karpay de manera instantánea.<br>*   El Onboarding del Sandbox debe ser completamente autoasistido en el Portal de Clientes. | Inspección visual del Portal de Clientes y prueba manual de integración de un cliente sintético (tiempo menor a 4 semanas). |
| **SMART-02:** Automatizar conciliación al 80%+. | **Paso 3 y 5:** Registro contable en Ledger y actualización del estado de pago. | `RF-06`, `RF-08`, `RNF-03` | *   El Motor de Conciliación debe descargar automáticamente el archivo de compensación SFTP cada noche y procesarlo.<br>*   El sistema debe lograr conciliar automáticamente de forma desatendida **más del 80% de los registros** coincidentes.<br>*   Cualquier diferencia debe registrarse inmediatamente en estado `DISCREPANCIA` con alerta visual en la consola Admin. | Ejecución de prueba de conciliación masiva en Sandbox con 10,000 transacciones y 1% de discrepancias inducidas. |
| **SMART-03:** Escalar adopción y asegurar alta disponibilidad 99.9%. | **Paso 4:** Procesamiento transaccional SPI. | `RF-01`, `RNF-04`, `RNF-06` | *   La latencia promedio del procesamiento de pago (desde POST `/v1/payouts` a respuesta exitosa) debe ser **menor a 2.0 segundos**.<br>*   El sistema debe soportar un volumen sostenido de **500 TPS** sin caídas.<br>*   La disponibilidad anual de la API debe mantenerse en **99.9%** (Uptime). | Pruebas de estrés y volumen destructivas inyectando 500 TPS concurrentes y auditoría mensual de reportes de uptime. |

---

## 2. Criterios de Aceptación Técnicos Específicos

Además del mapeo estratégico, se definen las siguientes condiciones de calidad obligatorias:

### A. Consistencia Financiera (Criterio Ledger)
*   **Condición:** No se permite ninguna transacción en estado `EXITOSA` en el Core Ledger que no cumpla con la ecuación contable de partida doble (`Debito = Credito`).
*   **Tolerancia:** Cero (0) pesos de discrepancia interna.
*   **Validación:** Ejecución automática de script de auditoría que valida los balances de todas las cuentas al final de cada ciclo de compensación diaria.

### B. Seguridad y Cumplimiento (SARLAFT)
*   **Condición:** Cada transacción que supere el umbral monetario de alerta establecido por el Oficial de Cumplimiento (ej. $10,000,000 COP en una sola transacción) debe ser analizada asíncronamente por el Motor de Fraude y reportada en la Consola Admin si presenta patrones sospechosos.
*   **Tolerancia:** Cero (0) fallas en el reporte de alertas de montos elevados.
*   **Validación:** Prueba unitaria inyectando solicitudes transaccionales con montos mayores al límite establecido y confirmando la creación de la alerta de fraude.

### C. No Repudio (Criptográfico)
*   **Condición:** Cada asiento contable debe contar con el hash SHA-256 encadenado y firmado criptográficamente por la clave privada del Core Ledger Service.
*   **Validación:** Ejecución de pruebas manuales de alteración de base de datos directamente en el motor PostgreSQL, comprobando que el sistema reporte la base de datos como corrompida de inmediato.
