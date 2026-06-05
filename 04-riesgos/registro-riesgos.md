# 📓 Registro de Riesgos y Bitácora de Seguimiento

Este documento representa el **Registro de Riesgos (Risk Register)** vivo de **Flux Payments**. Su propósito es dar un seguimiento continuo al estado de los riesgos a lo largo del ciclo de vida del proyecto, asignando dueños, identificando disparadores (triggers) y registrando la bitácora de incidentes.

---

## 1. Plan de Monitoreo de Riesgos

La revisión y actualización de esta bitácora se realiza durante los **comités de retrospectiva al final de cada Sprint (quincenal)**. El Líder de Arquitectura y el Product Manager son co-responsables del mantenimiento de este registro.

---

## 2. Bitácora Activa de Seguimiento de Riesgos

### 🔴 RS-02: Inestabilidad del Procesador SPI (Karpay)
*   **Estado Actual:** `Monitoreo Activo`
*   **Dueño del Riesgo:** DevOps Lead & Arquitecto de Software
*   **Disparador (Trigger):** Latencia superior a **4.5 segundos** en el Sandbox de Karpay por más de 3 peticiones consecutivas, o timeouts continuos (Error 504 Gateway Timeout).
*   **Acciones Preventivas Activas:**
    1.  Implementado el patrón de tolerancia a fallos *Circuit Breaker* en el Motor de Orquestación.
    2.  Habilitadas colas de reintento transaccional asíncrono con *Backoff Exponencial* en Kafka.
*   **Registro de Bitácora:**
    *   *Fecha: 15-Nov-2026:* Durante las pruebas de integración de la Fase 3, el Sandbox de Karpay presentó inestabilidad general de red de 2 horas. Se activó exitosamente el *Circuit Breaker*, encolando 45 transacciones de prueba en Kafka. Una vez restablecido el servicio de Karpay, las transacciones se liquidaron automáticamente sin duplicar cargos ni corromper balances en el Core Ledger.

---

### 🟡 RS-01: Vulneración de Seguridad / Fuga de Datos
*   **Estado Actual:** `Mitigado (Preventivo)`
*   **Dueño del Riesgo:** Lead Security Engineer & Tech Lead
*   **Disparador (Trigger):** Intentos fallidos de autenticación en la API superiores a 1,000 peticiones en 1 minuto desde una misma dirección IP (posible ataque de fuerza bruta), o reportes de dependencias desactualizadas con CVE críticos en la tubería de CI/CD.
*   **Acciones Preventivas Activas:**
    1.  Configurado el componente de Rate-Limiting en el API Gateway (Kong), limitando el tráfico a un máximo de 10 peticiones por segundo por dirección IP para endpoints públicos.
    2.  Integración de herramientas SAST/DAST (SonarQube y OWASP Dependency Check) automáticas en cada Merge Request.
*   **Registro de Bitácora:**
    *   *Fecha: 08-Dic-2026:* El escaneo automático de dependencias en el pipeline de CI/CD reportó una vulnerabilidad crítica de inyección SQL en un subcomponente de Node.js. Se bloqueó el merge a la rama principal (Main) de forma inmediata y se procedió a actualizar la dependencia a la versión estable libre del bug. El riesgo fue contenido en la etapa pre-productiva.

---

### 🟡 RS-03: Cambios en la Regulación Financiera Nacional
*   **Estado Actual:** `Monitoreo Pasivo`
*   **Dueño del Riesgo:** Oficial de Cumplimiento & PM
*   **Disparador (Trigger):** Publicación de proyectos de ley o resoluciones de carácter técnico-financiero por parte del Banco de la República o la SFC en su boletín de prensa oficial.
*   **Acciones Preventivas Activas:**
    1.  Participación bimensual en los comités regulatorios del gremio de *Colombia Fintech*.
    2.  Arquitectura modular flexible (diseño de APIs abstractas) para que cualquier cambio de protocolo del SPI se asimile en el conector (Karpay adapter) sin alterar la lógica core del ledger.
*   **Registro de Bitácora:**
    *   *Fecha: 05-Feb-2027:* El Banco de la República publicó las nuevas especificaciones técnicas para la mensajería de confirmación inmediata bajo estándar ISO 20022. El equipo del conector Karpay notificó la compatibilidad de su Sandbox. Se procedió a actualizar los esquemas de validación de datos en el Orquestador sin impacto en las capas de Ledger ni portales Web.

---

### 🟢 RS-05: Corrupción de Datos Contables en el Core Ledger
*   **Estado Actual:** `Mitigado (Preventivo)`
*   **Dueño del Riesgo:** Arquitecto de Software
*   **Disparador (Trigger):** Alertas en los scripts diarios de conciliación del sistema que reporten una diferencia distinta a cero (`Debito != Credito`) en la suma total del día transaccional.
*   **Acciones Preventivas Activas:**
    1.  Manejo estricto de Transacciones de Base de Datos ACID a nivel de código de Rust (Axum).
    2.  Uso de hashes criptográficos encadenados (blockchain-style ledger) para cada transacción.
*   **Registro de Bitácora:**
    *   *Fecha:* Sin registros de activación. El sistema se mantiene estable sin discrepancias en las sumas del ledger transaccional.
