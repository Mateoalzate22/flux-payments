# 📝 Registro de Decisiones de Arquitectura (ADRs)

Este documento registra las decisiones de arquitectura de software más trascendentales tomadas durante el proceso de diseño técnico de **Flux Payments**. Cada registro sigue el formato estándar propuesto por Michael Nygard, detallando el contexto, las alternativas evaluadas, la decisión final y las consecuencias técnicas y operativas derivadas.

---

## 🗺️ Índice de ADRs
1.  **`ADR-001`:** Adopción de Arquitectura Orientada a Eventos para la Orquestación de Transacciones.
2.  **`ADR-002`:** Implementación de Core Ledger Inmutable de Doble Entrada en Rust con TimescaleDB.

---

## 📑 Decisiones de Arquitectura

### 1. ADR-001: Arquitectura Orientada a Eventos para la Orquestación de Transacciones

*   **Identificador:** `ADR-001`
*   **Título:** Adopción de Arquitectura Orientada a Eventos para la Orquestación de Transacciones.
*   **Estado:** **Aceptado**
*   **Contexto:**
    La plataforma Flux Payments debe procesar un alto volumen de pagos y recaudos 24/7/365 en tiempo real. Las integraciones con entidades financieras externas (SPI vía Karpay, PSE vía Lyra) presentan latencias variables y riesgo de caídas o bloqueos de red constantes.
    Si utilizáramos una arquitectura puramente síncrona (ej: REST llamadas directas encadenadas en HTTP), la caída temporal del Sandbox de Karpay causaría que las peticiones de los clientes se acumularan en hilos del servidor backend, saturando la memoria del microservicio, aumentando la latencia y corrompiendo potencialmente la data de balance al no poder recuperar el estado ante un fallo de red a mitad del flujo.
*   **Decisión:**
    Decidimos implementar una **Arquitectura Orientada a Eventos (EDA - Event-Driven Architecture)** acoplada con el patrón **Saga Coreografiado/Orquestado** y el uso de **Apache Kafka** como bus de eventos transaccionales de alta velocidad.
    El flujo síncrono HTTP solo se mantendrá en la frontera (API Gateway y primer contacto en el controlador REST del Orquestador). Una vez que la transacción es validada e ingresa al ledger, los pasos de compensación, reintentos y notificaciones se orquestarán mediante la publicación y suscripción de eventos asíncronos en colas persistentes.
*   **Consecuencias:**
    *   *Positivas (Beneficios):*
        *   **Alta Resiliencia:** Si Karpay (SPI) se cae, la transacción no se pierde; se encola de forma segura en Kafka y el motor de conciliación reintenta la liquidación de forma asíncrona una vez restablecido el servicio.
        *   **Escalabilidad:** Desacopla la capa transaccional core de las capas accesorias (como la visualización en portales web, el motor de fraude o el sistema de notificaciones webhooks).
        *   **Baja Latencia de Entrada:** Permite responder al cliente B2B un estado de aceptación `HTTP 202 Accepted` de forma inmediata mientras la transacción se compensa en background.
    *   *Negativas / Desafíos:*
        *   **Consistencia Eventual:** La consistencia del estado transaccional en la interfaz de usuario se vuelve eventual.
        *   **Complejidad de Depuración:** Rastrear el flujo de un pago requiere el uso de herramientas de trazabilidad distribuida (ej. OpenTelemetry / Jaeger) y correlación de IDs.

---

### 2. ADR-002: Core Ledger Inmutable de Doble Entrada en Rust con TimescaleDB

*   **Identificador:** `ADR-002`
*   **Título:** Implementación de Core Ledger Inmutable de Doble Entrada en Rust con TimescaleDB.
*   **Estado:** **Aceptado**
*   **Contexto:**
    Para operar como Gateway de infraestructura Fintech (IaaS) en Colombia, es un requisito regulatorio y de auditoría ineludible garantizar que los saldos y balances de las Fintechs integradas sean consistentes a nivel centavo y libres de cualquier alteración maliciosa o fallo transaccional concurrente.
    El modelo tradicional de almacenar balances como una sola columna (ej. `balance = balance - monto`) en una tabla relacional común sufre de problemas de concurrencia (condiciones de carrera) y carece de historial de auditoría físico. Necesitamos una estructura de datos inmutable que registre cada débito y crédito por partida doble (Double-Entry Bookkeeping).
*   **Decisión:**
    Decidimos construir el microservicio de **Core Ledger** utilizando el lenguaje de programación **Rust** (con el framework Axum) y almacenar los datos de asientos contables en una base de datos **TimescaleDB** (una extensión de **PostgreSQL** optimizada para series de tiempo e inmutabilidad física por particionado).
    *   Rust se elige por su rendimiento a nivel C, ausencia de recolector de basura ( Garbage Collector ), eliminando pausas de latencia y garantizando la inmutabilidad física de objetos a nivel de compilación.
    *   Se implementa el patrón de contabilidad de partida doble: cada transacción financiera genera obligatoriamente un Asiento Contable (Journal Entry) que contiene mínimo dos Entradas (Entries): un Débito en una cuenta y un Crédito equivalente en otra. La ecuación contable siempre debe cuadrar (`Debito = Credito`).
    *   Se aplica un hash criptográfico encadenado SHA-256 en cada fila para asegurar el no repudio y detectar cualquier alteración manual directa de la base de datos.
*   **Consecuencias:**
    *   *Positivas (Beneficios):*
        *   **Consistencia Matemática Absoluta:** Al aplicar partida doble, es matemáticamente imposible que un saldo "desaparezca" o se duplique por error de software.
        *   **Auditoría y Cumplimiento Regulatorio:** El historial contable es 100% auditable y resiste ataques de manipulación interna de base de datos debido a la firma hash.
        *   **Alto Rendimiento:** Rust proporciona latencias inferiores a **10 milisegundos** para las operaciones de balance y escritura, incluso bajo alta concurrencia de transacciones.
    *   *Negativas / Desafíos:*
        *   **Mayor Curva de Aprendizaje:** Rust requiere que el equipo de desarrollo cuente con habilidades técnicas muy específicas en comparación con lenguajes como Javascript o Python.
        *   **Volumen de Almacenamiento:** Al no actualizar balances directamente sino guardar cada movimiento histórico por partida doble, la base de datos crece de forma exponencial, requiriendo estrategias proactivas de archivado e indexación.
