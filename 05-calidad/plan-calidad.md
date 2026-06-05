# 🧪 Plan de Aseguramiento de Calidad (QA Plan)

Este documento detalla el **Plan de Aseguramiento de Calidad (QA)** para la plataforma **Flux Payments**. Define el ciclo de vida de pruebas, la estrategia de testing multinivel, los ambientes, la cobertura mínima aceptable y las herramientas utilizadas para asegurar que la solución opere de forma impecable en el mercado financiero real.

---

## 1. Estrategia de Pruebas Multinivel

Para certificar la robustez y consistencia transaccional del sistema, implementamos una estrategia de pruebas que cubre desde la base de código hasta la experiencia de integración de extremo a extremo:

```
    ┌─────────────────────────────────────────────────────────────┐
    │ E2E & UAT: Pruebas con Clientes Piloto                      │
    ├─────────────────────────────────────────────────────────────┤
    │ Seguridad & Carga: Pentesting & Latencia (< 2s a 500 TPS)   │
    ├─────────────────────────────────────────────────────────────┤
    │ Integración: Mock de Sandbox Karpay y Lyra (PSE)            │
    ├─────────────────────────────────────────────────────────────┤
    │ Unitarias: Cobertura >85% en Rust y Spring Boot             │
    └─────────────────────────────────────────────────────────────┘
```

---

## 2. Tipos de Pruebas y Coberturas Metodológicas

### A. Pruebas Unitarias (Unit Testing)
*   **Objetivo:** Validar de forma aislada e independiente el comportamiento de funciones, validadores de formato API, motores de cálculo y mapeadores de datos.
*   **Métrica / Criterio:** Cobertura de código mínima del **85%** en todas las clases y funciones de negocio.
*   **Herramientas:** JUnit 5 (Java/Spring), Cargo Test (Rust/Axum), PyTest (Python).

### B. Pruebas de Integración (Integration Testing)
*   **Objetivo:** Verificar la correcta comunicación entre microservicios, bases de datos (TimescaleDB) y el bus de mensajería (Kafka).
*   **Métrica / Criterio:** Cobertura del 100% de los flujos de lectura/escritura del Ledger y orquestación con mock de proveedores externos.
*   **Herramientas:** Testcontainers (para instanciar bases de datos PostgreSQL y Kafka reales en contenedores efímeros de Docker durante las pruebas), Postman/Newman.

### C. Pruebas No Funcionales (Rendimiento, Carga y Seguridad)
*   **Objetivo:** Certificar la latencia, concurrencia extrema e inmutabilidad.
*   **Métricas / Criterios:**
    *   *Rendimiento:* Latencia transaccional promedio inferior a **2.0 segundos** (Percentil 95).
    *   *Estrés:* Carga sostenida de **500 Transacciones por Segundo (TPS)** por 1 hora sin degradación física.
    *   *Seguridad:* Escaneo de dependencias en tubería CI/CD con **cero (0)** vulnerabilidades con severidad crítica/alta detectadas.
*   **Herramientas:** Apache JMeter (Carga), OWASP ZAP (Pentesting).

### D. Pruebas de Aceptación de Usuario (UAT)
*   **Objetivo:** Validar que los desarrolladores externos y contadores del cliente experimenten un proceso fluido y simple de integración y conciliación.
*   **Métrica / Criterio:** Completar exitosamente el ciclo de vida del piloto con 2 Fintechs integradas, registrando un tiempo de primera integración inferior a 4 semanas.

---

## 3. Ambientes de Prueba y Promoción

Para mitigar riesgos operativos, la infraestructura de Flux se segmenta en tres entornos completamente aislados:

1.  **Desarrollo (Local / Dev):** Utilizado por los desarrolladores para codificación diaria. Base de datos con datos sintéticos efímeros.
2.  **Pruebas e Integración (Sandbox):** Réplica exacta de la arquitectura productiva pero de bajo coste físico. Integrado con los entornos Sandbox mock de Karpay (SPI) y Lyra (PSE). Este ambiente se expone a los clientes B2B para sus pruebas técnicas iniciales.
3.  **Producción (Prod):** Entorno de alta disponibilidad, con escalado automático y monitoreo estricto 24/7/365 en nube de AWS Colombia.
