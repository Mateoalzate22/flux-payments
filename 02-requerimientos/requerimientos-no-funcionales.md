# 🛡️ Catálogo de Requerimientos No Funcionales (RNF)

Este documento contiene los requerimientos no funcionales (RNF) del sistema **Flux Payments**, clasificados según la norma internacional de calidad de software **ISO/IEC 25010**. Estos requerimientos definen las restricciones de rendimiento, seguridad, confiabilidad, mantenibilidad y usabilidad indispensables para operar en el sector financiero de alta transaccionalidad.

---

## 1. Clasificación ISO/IEC 25010

```
                   ┌───────────────────────────────────────────────┐
                   │        REQUERIMIENTOS NO FUNCIONALES          │
                   └───────────────────────┬───────────────────────┘
         ┌───────────────────┬─────────────┴──────┬───────────────────┐
  ┌──────┴──────┐     ┌──────┴──────┐      ┌──────┴──────┐     ┌──────┴──────┐
  │  Seguridad  │     │ Rendimiento │      │Confiabilidad│     │  Usabilidad │
  └─────────────┘     └─────────────┘      └─────────────┘     └─────────────┘
```

---

## 2. Requerimientos de Seguridad (Security)

### RNF-01: Autenticación y Autorización Robusta
*   **Descripción:** Toda solicitud dirigida a las APIs públicas de Flux Payments en entorno productivo debe estar protegida.
*   **Métrica / Límite:** Autenticación de múltiples factores (MFA) para accesos a los portales web. Uso de tokens **OAuth 2.0 / JWT** con expiración máxima de **15 minutos** y llaves de API (`X-API-KEY`) firmadas criptográficamente para consumo entre servidores (M2M).
*   **Método de Verificación:** Auditoría de código en el API Gateway y pruebas de penetración automatizadas (Pentesting).

### RNF-02: Cifrado de Datos
*   **Descripción:** Protección de información financiera y de identificación del usuario tanto en reposo como en tránsito.
*   **Métrica / Límite:**
    *   *En tránsito:* Uso exclusivo de protocolos **TLS 1.3** con suites de cifrado fuertes.
    *   *En reposo:* Cifrado de bases de datos utilizando el estándar **AES-256** (Advanced Encryption Standard).
*   **Método de Verificación:** Pruebas de configuración de red SSL Labs (calificación mínima requerida: **A+**) y revisión de configuraciones de infraestructura cloud.

### RNF-03: Auditoría y No Repudio
*   **Descripción:** Cada inserción transaccional debe ser inmutable y auditable mediante criptografía.
*   **Métrica / Límite:** Firma digital de registros transaccionales con hashes en cadena (**SHA-256** de la transacción actual + hash del registro inmediatamente anterior), asegurando que cualquier manipulación de la base de datos rompa la cadena de auditoría instantáneamente.
*   **Método de Verificación:** Ejecución de pruebas de integridad de base de datos alterando registros sintéticos y verificando la activación automática de alertas de corrupción.

---

## 3. Requerimientos de Comportamiento Temporal y Rendimiento (Performance)

### RNF-04: Latencia en Procesamiento de Pagos
*   **Descripción:** El sistema debe responder a las solicitudes de procesamiento de pagos de forma inmediata para alinearse al estándar del SPI.
*   **Métrica / Límite:** Latencia de extremo a extremo (desde que el cliente envía la petición REST `/v1/payouts` hasta que el gateway recibe la confirmación o el timeout de Karpay) inferior a **2.0 segundos** para el 95% de las transacciones (Percentil 95).
*   **Método de Verificación:** Pruebas de carga e inyección de tráfico sintético en Sandbox simulando 100 transacciones concurrentes por segundo.

### RNF-05: Escalabilidad Horizontal y Concurrencia
*   **Descripción:** La infraestructura de Flux debe autoescalar para soportar picos transaccionales típicos de días de pago (quincenas) y eventos de descuento comercial (Cyberlunes).
*   **Métrica / Límite:** Capacidad de procesar hasta **500 transacciones por segundo (TPS)** concurrentes sin degradación del servicio ni incremento de latencia por encima de 3 segundos.
*   **Método de Verificación:** Pruebas de estrés y volumen destructivas utilizando herramientas Cloud de simulación de carga (ej. JMeter / Locust).

---

## 4. Requerimientos de Fiabilidad y Disponibilidad (Reliability)

### RNF-06: Alta Disponibilidad (Uptime)
*   **Descripción:** La plataforma debe operar de forma continua, 24 horas al día, 7 días a la semana, debido a la naturaleza en tiempo real del SPI.
*   **Métrica / Límite:** Disponibilidad operativa garantizada de la API de **99.9%** anual (equivalente a un tiempo máximo acumulado de inactividad no planificada inferior a 8.76 horas al año).
*   **Método de Verificación:** Reportes de uptime generados mensualmente por herramientas de monitoreo externo de red (ej. Datadog / New Relic).

### RNF-07: Resiliencia y Tolerancia a Fallos
*   **Descripción:** El sistema debe tolerar la caída temporal de proveedores externos (SPI Karpay o PSE Lyra) sin corromper el estado financiero de los clientes.
*   **Métrica / Límite:** Implementación del patrón de diseño **Circuit Breaker** (Disyuntor) con failover automático. En caso de timeout continuo de Karpay por más de 5 peticiones, el circuito se abre y el sistema almacena las solicitudes pendientes en colas de mensajería asíncronas seguras para su reprocesamiento posterior.
*   **Método de Verificación:** Simulación de caída del Sandbox de Karpay durante pruebas de integración y validación del encolado automático de transacciones.

---

## 5. Requerimientos de Usabilidad (Usability)

### RNF-08: Facilidad de Integración (Developer Experience)
*   **Descripción:** Proporcionar herramientas claras para reducir el tiempo de integración de los desarrolladores externos.
*   **Métrica / Límite:**
    *   Portal de Desarrolladores con el 100% de la API documentada bajo el estándar **OpenAPI 3.0 / Swagger** interactivo.
    *   Disponibilidad de entornos de pruebas (Sandbox) aislados que emulen las respuestas del SPI de forma instantánea.
*   **Método de Verificación:** Encuestas y pruebas de usabilidad técnica con desarrolladores externos (meta: tiempo de primera transacción exitosa en Sandbox inferior a 2 horas).
