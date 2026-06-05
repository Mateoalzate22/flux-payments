# 👥 Matriz de Interesados (Stakeholders)

Para garantizar la correcta alineación y comunicación durante el ciclo de vida de **Flux Payments**, se definen los interesados (stakeholders) clave, clasificándolos por su nivel de poder e interés, y estableciendo estrategias específicas de gestión para cada grupo.

---

## 1. Clasificación y Matriz de Interesados

La siguiente tabla detalla cada uno de los actores involucrados, diferenciando entre internos y externos, detallando sus expectativas e influencia sobre el proyecto.

| Interesado | Tipo | Rol / Contexto | Nivel de Poder (1-5) | Nivel de Interés (1-5) | Expectativa Principal |
| :--- | :--- | :--- | :---: | :---: | :--- |
| **Junta Directiva / PM** | Interno | Dirección del negocio, financiamiento e hitos comerciales. | 5 | 5 | Retorno de inversión, cumplimiento del cronograma y calidad técnica. |
| **Equipo de Ingeniería** | Interno | Backend, Frontend, DevOps y Arquitectos. | 3 | 5 | Arquitectura robusta, código limpio, documentación técnica estable y baja deuda técnica. |
| **Equipo de Operaciones** | Interno | Soporte de transacciones, monitoreo operativo y conciliaciones manuales. | 3 | 4 | Alertas claras de fallos, consolas administrativas simples de operar. |
| **Equipo Comercial B2B** | Interno | Atracción y venta a clientes Fintech y Corporativos. | 2 | 5 | Facilidad en el discurso de venta, rapidez en el onboarding técnico de los clientes. |
| **Fintechs y Marketplaces**| Externo | Clientes principales que realizan cobros y pagos inmediatos. | 4 | 5 | Integración extremadamente rápida, disponibilidad 99.9%, comisiones competitivas. |
| **ERPs Contables** | Externo | Clientes secundarios que automatizan conciliación contable. | 3 | 4 | Estructuras de datos limpias (JSON/CSV) que mapeen fácilmente con sus registros. |
| **Karpay (Proveedor SPI)** | Externo | Proveedor tecnológico tecnológico que procesa y conecta al SPI. | 4 | 3 | Tráfico limpio, cumplimiento de protocolos de red y latencia mínima de respuesta. |
| **Lyra (Proveedor PSE)** | Externo | Canal alternativo para recaudo inmediato. | 3 | 3 | Cumplimiento contractual de flujos transaccionales y comisiones claras. |
| **Banco de la República** | Externo | Regulador y operador del Sistema de Pagos Inmediatos (SPI). | 5 | 2 | Adopción del SPI en Colombia siguiendo estrictamente la normativa financiera. |
| **Bancos Destino / Origen** | Externo | Entidades financieras donde están alojadas las cuentas de los usuarios. | 3 | 2 | Operación libre de fraude, no saturación de canales transaccionales por API. |

---

## 2. Cuadrante de Gestión (Poder vs Interés)

Basado en la evaluación del nivel de poder e interés, estructuramos a los stakeholders en el clásico cuadrante de influencia:

```
      Poder Alto (5)
      ┌───────────────────────────────────────┬───────────────────────────────────────┐
      │                                       │                                       │
      │         GESTIONAR DE CERCA            │         MONITOREAR PROACTIVAMENTE     │
      │   - Fintechs y Marketplaces (Clientes)│   - Banco de la República             │
      │   - Karpay (Proveedor SPI)            │   - Bancos tradicionales              │
      │                                       │                                       │
      ├───────────────────────────────────────┼───────────────────────────────────────┤
      │                                       │                                       │
      │            MANTENER INFORMADO         │               MANEJAR DE CERCA        │
      │   - ERPs Contables                    │   - Junta Directiva / Sponsor         │
      │   - Lyra (Proveedor PSE)              │   - Equipo de Ingeniería y Ops        │
      │                                       │                                       │
      └───────────────────────────────────────┴───────────────────────────────────────┘
      Poder Bajo (1)                         Interés (5)
```

---

## 3. Estrategias de Comunicación y Gestión

Para maximizar el éxito del proyecto, se definen los siguientes canales y frecuencias de comunicación para cada interesado:

*   **Junta Directiva / Sponsor (Poder Alto / Interés Alto):**
    *   *Estrategia:* Reunión de avance y comités de cambio quincenales. Reportes de estatus del cronograma mensual.
*   **Fintechs y Clientes Piloto (Poder Alto / Interés Alto):**
    *   *Estrategia:* Sesiones de co-diseño de API, entregas frecuentes de documentación (Swagger) y soporte a través de un canal exclusivo de Slack.
*   **Equipo de Ingeniería y Operaciones (Poder Medio / Interés Alto):**
    *   *Estrategia:* Daily stands de 15 minutos de lunes a viernes, retrospectivas al final de cada sprint (quincenal) y uso de Jira/GitHub Projects para traqueo técnico.
*   **Karpay & Lyra (Poder Alto / Interés Medio):**
    *   *Estrategia:* Comités de integración técnica semanales durante la Fase de Integración y monitoreo automatizado de disponibilidad en la fase operativa.
*   **Banco de la República (Poder Alto / Interés Bajo):**
    *   *Estrategia:* Auditorías de cumplimiento y reportes regulatorios trimestrales requeridos por la ley colombiana.
