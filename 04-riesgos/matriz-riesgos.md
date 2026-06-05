# ⚠️ Matriz de Riesgos del Proyecto

Este documento detalla la **Matriz de Riesgos** estructurada para **Flux Payments**. Identificamos, evaluamos y definimos planes de mitigación robustos para riesgos de tipo técnico, operativo, de negocio y regulatorio específicos del mercado Fintech en Colombia.

---

## 1. Escala de Evaluación de Riesgos

El nivel de riesgo se calcula multiplicando la **Probabilidad (1-5)** por el **Impacto (1-5)**:

*   **Riesgo Crítico (15 - 25):** Requiere mitigación inmediata y plan de contingencia activo.
*   **Riesgo Alto (8 - 12):** Requiere atención prioritaria y monitoreo semanal.
*   **Riesgo Medio (4 - 6):** Requiere monitoreo regular y asignación de responsables.
*   **Riesgo Bajo (1 - 3):** Requiere seguimiento general en cada sprint.

---

## 2. Matriz de Riesgos Detallada

La siguiente tabla resume los riesgos identificados, su nivel de exposición y las estrategias de control y mitigación:

| ID | Categoría | Riesgo Identificado | Probabilidad (1-5) | Impacto (1-5) | Nivel de Riesgo (PxI) | Plan de Mitigación / Contingencia |
| :--- | :--- | :--- | :---: | :---: | :---: | :--- |
| **RS-01** | Técnico / Seguridad | **Vulneración de Seguridad / Fuga de Datos:** Exposición de llaves de API o datos transaccionales confidenciales (números de cuenta, cédulas). | 2 | 5 | **10** (Alto) | *Mitigación:* Implementar cifrado AES-256 en reposo, TLS 1.3 en tránsito y validación por firmas SHA-256.<br>*Contingencia:* Rotación automática e inmediata de llaves, aislamiento de red y plan de respuesta ante incidentes cibernéticos (CSIRT). |
| **RS-02** | Operativo | **Inestabilidad del Procesador SPI (Karpay):** Caídas frecuentes del Sandbox o API productiva de Karpay, impidiendo transferencias inmediatas. | 4 | 4 | **16** (Crítico) | *Mitigación:* Diseñar conectores modulares e intercambiables (Patrón Adapter). Integrar colas de mensajería (Kafka) con reintentos automáticos.<br>*Contingencia:* Habilitar failover de recaudo temporal vía PSE (Lyra) e informar al cliente a través de webhooks con estados `PENDIENTE_VERIFICACION`. |
| **RS-03** | Regulatorio | **Cambios en Normativa Financiera:** Modificación de directrices del Banco de la República o de la Superfinanciera de Colombia sobre el SPI. | 2 | 5 | **10** (Alto) | *Mitigación:* Contar con un equipo de cumplimiento especializado en SARLAFT y regulación Fintech en Colombia.<br>*Contingencia:* Participación en mesas de trabajo conjuntas con la asociación *Colombia Fintech* para anticipar cambios normativos. |
| **RS-04** | Negocio | **Baja Adopción del Ecosistema B2B:** Pocas Fintechs integradas al cierre del primer año comercial por desconfianza en la plataforma. | 3 | 3 | **9** (Alto) | *Mitigación:* Ofrecer un Sandbox interactivo autoasistido con documentación impecable, planes piloto gratuitos de 3 meses y SDKs listos para usar.<br>*Contingencia:* Ajuste dinámico de tarifas transaccionales en esquemas escalonados por volumen de uso. |
| **RS-05** | Técnico / Consistencia | **Corrupción de Datos Contables en Base de Datos:** Pérdida de concordancia financiera por caídas del sistema a mitad de una transacción. | 1 | 5 | **5** (Medio) | *Mitigación:* Implementación estricta de partida doble y transacciones ACID en el Core Ledger (Rust) con firmas SHA-256 en cadena.<br>*Contingencia:* Scripts de auditoría diarios automáticos antes de la conciliación que validen la ecuación contable. |

---

## 3. Cuadrante de Impacto vs Probabilidad

El siguiente mapa conceptual ubica los riesgos dentro de la escala de atención de la organización:

```
      Impacto Alto (5)
      ┌───────────────────────────────────────┬───────────────────────────────────────┐
      │                                       │                                       │
      │         [RS-01] Fuga de Datos         │    [RS-02] Caída de Karpay / SPI      │
      │         [RS-03] Regulación SFC        │                                       │
      │                                       │                                       │
      ├───────────────────────────────────────┼───────────────────────────────────────┤
      │                                       │                                       │
      │         [RS-05] Corrupción Ledger     │    [RS-04] Baja Adopción B2B          │
      │                                       │                                       │
      │                                       │                                       │
      └───────────────────────────────────────┴───────────────────────────────────────┘
      Probabilidad Baja (1)                  Probabilidad Alta (5)
```
