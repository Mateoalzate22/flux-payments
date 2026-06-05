# 🛑 Diagrama de Flujo AS-IS (Situación Actual)

Este documento modela el estado actual (**AS-IS**) de los procesos y la infraestructura tecnológica que enfrentan las empresas (Fintechs, Marketplaces, ERPs) en Colombia para conectarse a los sistemas bancarios tradicionales y realizar transferencias.

---

## 1. Descripción de la Problemática AS-IS
En el modelo tradicional, cada empresa que requiera realizar pagos electrónicos o recaudar masivamente fondos debe interactuar de forma aislada con la banca. Las ineficiencias de esta situación incluyen:
1.  **Conexión fragmentada:** No existe un estándar API unificado. Las empresas deben construir integraciones a la medida de la API o protocolo (ej: SFTP, AS2, Web Services legacy) de cada entidad bancaria individualmente.
2.  **Tiempos excesivos:** Cada proceso de integración, homologación bancaria y certificación de seguridad toma entre **6 y 12 meses** por banco.
3.  **Procesamiento asíncrono diferido:** Las transferencias bancarias ACH tradicionales no ocurren en tiempo real (pueden tardar horas o incluso días si se inician en fin de semana o días festivos).
4.  **Operación manual extrema:** Al final de la jornada bancaria, el equipo de contabilidad debe descargar de forma manual extractos en formatos plano (Excel, TXT) de múltiples bancos, cruzándolos uno a uno contra sus sistemas contables internos, lo que genera demoras operativas y descuadres de balance recurrentes.

---

## 2. Diagrama de Flujo AS-IS (Mermaid)

El siguiente flujo transaccional visualiza la complejidad y lentitud de los procesos contables y técnicos tradicionales:

```mermaid
flowchart TD
    classDef banco fill:#f9f,stroke:#333,stroke-width:2px;
    classDef manual fill:#ffcccc,stroke:#cc0000,stroke-width:2px;
    classDef cliente fill:#d1e7dd,stroke:#0f5132,stroke-width:2px;

    Inicio([Cliente solicita desembolso o pago]) :::cliente --> Op1[Desarrollar integración customizada para el Banco A]
    Op1 --> Op2[Iniciar proceso de certificación de seguridad con Banco A <br/> 6 a 12 Meses] :::manual
    
    Op2 --> Op3{¿Aprobado?}
    Op3 -- No --> Op1
    Op3 -- Sí --> Transaccion[Ejecutar Transacción ACH] :::cliente
    
    Transaccion --> Banco[Infraestructura Banco Origen] :::banco
    Banco -->|Espera compensación ACH de 4 a 24 horas| Compensacion{¿Es día hábil y horario bancario?}
    
    Compensacion -- No --> Espera[Transacción encolada hasta el próximo día hábil] :::manual
    Compensacion -- Sí --> BancoDestino[Banco Destino acredita fondos al beneficiario] :::banco
    
    BancoDestino --> Reporte[Bancos generan extractos manuales al final del día]
    Reporte --> Conciliacion[Equipo Contable descarga extractos de múltiples bancos] :::manual
    Conciliacion --> Excel[Cruce manual en planillas Excel de registros vs contabilidad] :::manual
    Excel --> Fin([Operación cerrada con descuadres y retraso operativo]) :::manual

    class Banco,BancoDestino banco;
    class Op2,Espera,Conciliacion,Excel,Fin manual;
```

---

## 3. Principales Puntos de Dolor Identificados

*   **Poder Tecnológico Limitado:** Incapacidad de ofrecer servicios instantáneos 24/7/365, afectando la competitividad frente a pasarelas de pago globales.
*   **Altos Costos de Ingeniería:** Mantener múltiples conectores legacy (cada uno con su propio esquema de errores, lógica de autenticación y protocolos) consume hasta el 70% de la capacidad del equipo de ingeniería.
*   **Falta de Trazabilidad:** Ante una transacción fallida en el procesamiento batch, el cliente final no recibe retroalimentación inmediata, obligando a generar tickets de soporte técnico e incrementar el gasto en backoffice.
