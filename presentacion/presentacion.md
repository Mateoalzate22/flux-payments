# 📊 Presentación Final de Sustentación - Flux Payments

Este documento contiene la **estructura, contenido y guión detallado de las 10 diapositivas** preparadas para la sustentación final del proyecto **Flux Payments** ante el docente de la materia de Arquitectura de Software Empresarial.

Este contenido está diseñado para ser copiado directamente a PowerPoint o renderizado mediante herramientas de diapositivas en Markdown (como Marp).

---

## 📽️ Diapositiva 1: Portada y Presentación
*   **Título Principal:** ⚡ Flux Payments - Plataforma Fintech IaaS (Integración SPI)
*   **Subtítulo:** Simplificando la integración de pagos en tiempo real en Colombia.
*   **Integrantes:** Mateo Alzate (Grupo 1)
*   **Materia:** Arquitectura de Software Empresarial
*   **Fecha de Sustentación:** 28 de Mayo de 2026
*   **Guión de Apertura (Speaker Notes):**
    > *"Buenos días profesor. Hoy presentamos Flux Payments, una plataforma de infraestructura financiera bajo el modelo Fintech Infrastructure-as-a-Service. Nuestro proyecto busca resolver uno de los cuellos de botella más importantes del ecosistema Fintech en Colombia: la compleja y costosa integración con el nuevo Sistema de Pagos Inmediatos (SPI) del Banco de la República."*

---

## 📽️ Diapositiva 2: El Problema Identificado (Situación AS-IS)
*   **Título:** La Complejidad de la Conexión Financiera en Colombia
*   **Puntos Clave:**
    *   **Procesos Fragmentados y Complejos:** Las empresas deben integrarse individualmente con cada banco mediante protocolos obsoletos (SFTP, archivos planos).
    *   **Tiempos de Implementación Excesivos:** Salir a producción con transferencias bancarias toma entre **6 y 12 meses** por banco.
    *   **Operación Manual Ineficiente:** Conciliación manual en planillas Excel de múltiples extractos diarios al final de la jornada.
    *   **Procesamiento Lento:** La compensación ACH tradicional tarda horas o días, limitando la innovación transaccional 24/7.
*   **Guión (Speaker Notes):**
    > *"Actualmente, las empresas y fintechs colombianas se enfrentan a un panorama de integración sumamente fragmentado. Integrar transferencias directas toma de 6 a 12 meses de arduo trabajo de ingeniería, y al final del día los contadores deben conciliar manualmente planillas Excel descargadas de múltiples bancos. Esto frena la innovación y encarece la operación del ecosistema fintech."*

---

## 📽️ Diapositiva 3: Objetivos SMART del Proyecto
*   **Título:** Metas Estratégicas y Medibles
*   **Los 3 Objetivos:**
    1.  **Reducción del Tiempo de Integración (SMART-01):**
        *   *Línea Base:* 6 a 12 meses ──> *Meta:* **2 a 4 semanas** usando la API de Flux en los primeros 6 meses.
    2.  **Automatización de la Conciliación (SMART-02):**
        *   *Línea Base:* Procesos 100% manuales ──> *Meta:* **80% o más** de conciliaciones automáticas del ledger durante el primer año.
    3.  **Adopción Comercial y Alta Disponibilidad (SMART-03):**
        *   *Línea Base:* 0 clientes ──> *Meta:* **10+ empresas integradas** en el primer año asegurando un Uptime de la API del **99.9%**.
*   **Guión (Speaker Notes):**
    > *"Para guiar el éxito del proyecto, definimos tres objetivos SMART específicos. El primero es reducir el tiempo de integración técnica a un rango de 2 a 4 semanas mediante nuestra API estandarizada. El segundo es automatizar más del 80% del cruce de conciliación financiera diario, y el tercero es asegurar la confianza empresarial adquiriendo 10 clientes corporativos bajo un acuerdo de nivel de servicio de alta disponibilidad del 99.9%."*

---

## 📽️ Diapositiva 4: Alcance de la Solución (Límites del Sistema)
*   **Título:** Delimitación del Gateway IaaS
*   **¿Qué INCLUYE el Proyecto?**
    *   API Gateway de alto rendimiento y Checkout UI responsivo y seguro.
    *   Motor de Orquestación transaccional y Ledger inmutable de partida doble.
    *   Conectores externos para el SPI (Karpay) y recaudos complementarios (PSE Lyra).
    *   Portales Web interactivos de Clientes y Consola de Administración interna.
    *   Motor de Conciliación automática asíncrono.
*   **¿Qué EXCLUYE el Proyecto?**
    *   El desarrollo del SPI nacional del Banco de la República.
    *   Modificaciones en los Cores Bancarios internos de los bancos tradicionales.
    *   La integración customizada en el ERP contable privado de cada cliente.
    *   Trámites de licenciamiento financiero directo (Flux es un habilitador tecnológico).
*   **Guión (Speaker Notes):**
    > *"El alcance de Flux Payments está claramente delimitado para garantizar viabilidad y foco. Proporcionamos toda la capa intermedia de software: API Gateway, motor de orquestación, portales de usuario, conciliador diario y un ledger contable. Quedan explícitamente excluidos el desarrollo de la red SPI nacional, la contabilidad interna de cada cliente y la captación directa de depósitos bancarios, ya que operamos como un habilitador puramente tecnológico."*

---

## 📽️ Diapositiva 5: Requerimientos Clave (ISO 25010)
*   **Título:** Requerimientos Funcionales y Estándares de Calidad
*   **Requerimientos Funcionales (RF Core):**
    *   *RF-01:* Procesamiento de Pagos Inmediatos (Payouts) en tiempo real vía SPI.
    *   *RF-04:* Autenticación y firmas HTTPS seguras en Gateway.
    *   *RF-06:* Registro inmutable de partida doble en Core Ledger.
    *   *RF-08:* Cruce y conciliación automática desatendida diaria del 80%+.
*   **Requerimientos No Funcionales (RNF - ISO 25010):**
    *   *Seguridad (RNF-01/02):* Autenticación OAuth2/JWT y cifrado de bases de datos AES-256.
    *   *Rendimiento (RNF-04):* Latencia transaccional de extremo a extremo **menor a 2.0 segundos**.
    *   *Escalabilidad (RNF-05):* Soporte concurrente de hasta **500 TPS** sin degradación.
*   **Guión (Speaker Notes):**
    > *"Nuestros requerimientos funcionales core modelan el ciclo transaccional completo: API Gateway, orquestación, libro diario en Ledger y conciliador. En los requerimientos no funcionales, nos alineamos estrictamente a la norma de calidad ISO 25010, garantizando cifrado fuerte TLS 1.3, latencias de procesamiento menores a 2 segundos y capacidad para autoescalar hasta 500 transacciones por segundo."*

---

## 📽️ Diapositiva 6: Arquitectura Propuesta y Decisiones de Diseño (ADRs)
*   **Título:** Patrones Arquitectónicos y Elecciones Tecnológicas
*   **Arquitectura Propuesta:**
    *   Patrón de **Microservicios desacoplados** Cloud-Native en AWS.
    *   **Arquitectura Orientada a Eventos (EDA)** con Apache Kafka para alta resiliencia.
    *   **Arquitectura Hexagonal (Puertos y Adaptadores)** para aislar la lógica de dominio.
*   **Architecture Decision Records (ADRs Clave):**
    *   **ADR-001 (Event-Driven Gateway):** Adopción de eventos asíncronos en Kafka para desacoplar el procesamiento transaccional de las fallas de red externas.
    *   **ADR-002 (Core Ledger in Rust):** Construcción del libro contable en Rust con base de datos TimescaleDB, garantizando inmutabilidad criptográfica y latencias <10ms.
*   **Guión (Speaker Notes):**
    > *"La arquitectura propuesta se fundamenta en dos decisiones técnicas críticas documentadas en nuestros ADRs. En el ADR-001 optamos por una arquitectura orientada a eventos con Apache Kafka para encolar transacciones de forma segura y evitar pérdida de fondos ante caídas de red externas. En el ADR-002, decidimos programar el Core Ledger en Rust sobre una base de datos TimescaleDB, garantizando consistencia inmutable por partida doble con latencias de microsegundos."*

---

## 📽️ Diapositiva 7: Diagrama de Contexto C1 y Contenedores C2
*   **Título:** Vistas de Arquitectura - Modelo C4
*   **Nivel 1: Contexto C1**
    *   Muestra a Flux Payments en el centro, interactuando con los Clientes (Fintechs/Marketplaces), los operadores internos y los proveedores externos autorizados de red (Karpay para SPI y Lyra para PSE).
*   **Nivel 2: Contenedores C2**
    *   Separación clara de responsabilidades:
        *   *API Gateway (Kong)* como primer filtro.
        *   *Portales Frontend (React)* para la interacción de usuario.
        *   *Microservicios especializados:* Orquestador (Spring Boot/Java), Core Ledger (Rust) y Conciliador (Python).
        *   *Datastores dedicados* para mitigar contención de disco (PostgreSQL y TimescaleDB).
*   **Guión (Speaker Notes):**
    > *"El Nivel 1 del Modelo C4 expone las fronteras de nuestro ecosistema, donde Flux actúa como gateway hacia los procesadores externos Karpay y Lyra. En el Nivel 2 desglosamos los contenedores tecnológicos del sistema, destacando la separación física y lógica entre el microservicio de orquestación en Java, el de conciliación en Python y el ledger contable en Rust, garantizando que el tráfico de reportes no sature las escrituras transaccionales core."*

---

## 📽️ Diapositiva 8: Orquestación del Flujo Transaccional C3
*   **Título:** Nivel 3 - Componentes y Orquestación Hexagonal
*   **Estructura Interna del Orquestador:**
    *   *TransactionController:* Recibe y valida JSON HTTP.
    *   *SecurityFilter:* Aplica Rate-Limiting en cabeceras.
    *   *OrchestrationEngine:* Coordina la máquina de estados de la transacción (`PENDIENTE`, `RESERVADO`, `EXITOSO`).
    *   *LedgerClient & KarpayClient:* Adaptadores que comunican con el Ledger gRPC y el Sandbox externo de Karpay.
*   **El Flujo Feliz en Tiempo Real:**
    ```
    API POST ──> Auth Filter ──> Orquestador ──> Débito gRPC Ledger ──> Karpay SPI (Karpay) ──> Confirmación ──> Cierre Ledger ──> Webhook Cliente
    ```
*   **Guión (Speaker Notes):**
    > *"En el Nivel 3 de Componentes, observamos la arquitectura hexagonal aplicada al motor de orquestación. Cuando una Fintech envía una solicitud de desembolso, el controlador la recibe, el filtro de seguridad la valida y el motor de orquestación realiza un débito temporal en el Core Ledger mediante gRPC. Posteriormente, traduce el mensaje e inicia la transferencia real en la red SPI mediante Karpay en menos de 2 segundos."*

---

## 📽️ Diapositiva 9: Gestión de Riesgos y Mitigaciones
*   **Título:** Exposición Operativa y Planes de Acción
*   **Principales Riesgos Identificados:**
    *   **Inestabilidad de Karpay / SPI (Crítico - R: 16):** Caídas de red del proveedor externo.  
        *Mitigación:* Patrón *Circuit Breaker* en el orquestador y colas asíncronas seguras en Kafka.
    *   **Fuga de Datos o Claves (Alto - R: 10):** Exposición de llaves de API.  
        *Mitigación:* Autenticación OAuth2 robusta, cifrado AES-256 y rate-limiting en Gateway Kong.
    *   **Cambios Regulatorios SFC/BanRep (Alto - R: 10):** Nuevas normas de compensación transaccional.  
        *Mitigación:* Arquitectura de adaptadores desacoplados e intercambiables.
*   **Guión (Speaker Notes):**
    > *"Operar en la industria de pagos implica gestionar riesgos críticos. El riesgo más relevante es la inestabilidad de la red externa de pagos inmediatos. Lo mitigamos mediante el patrón Circuit Breaker y encolamiento resiliente en Kafka, permitiendo al sistema retener y procesar las órdenes una vez restablecida la red sin fallar. También aplicamos cifrado estricto AES-256 para contener fugas de datos y rate-limiting para mitigar denegación de servicio."*

---

## 📽️ Diapositiva 10: Conclusiones y Lecciones Aprendidas
*   **Título:** Síntesis y Cierre de Proyecto
*   **Conclusiones Principales:**
    *   **Viabilidad Tecnológica:** Demostramos que mediante un Gateway IaaS (Flux Payments) es posible reducir la barrera técnica del SPI en Colombia, bajando la integración de meses a solo semanas.
    *   **Consistencia Criptográfica:** El uso de Rust y partida doble en el Core Ledger asegura que la plataforma resista auditorías reguladoras y garantice consistencia financiera inalterable.
    *   **Tolerancia a Fallos:** La arquitectura orientada a eventos con Kafka y el desacoplamiento mediante adaptadores modulares protegen la operación ante fallos externos de red.
*   **Lección Aprendida:** *En sistemas financieros, el rendimiento y la seguridad no pueden ser agregados a posteriori; deben ser la base del diseño de arquitectura de software empresarial.*
*   **Guión (Speaker Notes):**
    > *"Para concluir, Flux Payments demuestra que es posible democratizar el acceso a la infraestructura financiera de pagos inmediatos en Colombia. El uso de Rust y partida doble no solo responde a una necesidad de rendimiento, sino de consistencia inmutable para cumplir con la regulación de la Superfinanciera. La mayor lección aprendida es que en sistemas financieros de alta transaccionalidad, la seguridad y el rendimiento deben ser transversales a la arquitectura y estar embebidos desde la primera línea de código. Quedamos abiertos a sus preguntas. Muchas gracias."*
