# 📝 Lista de Verificación de Artefactos (Checklist)

Este documento detalla la **Lista de Verificación de Artefactos** que fue formalmente aplicada a toda la documentación y diagramas de **Flux Payments** para garantizar que se cumplan estrictamente las rúbricas y criterios de evaluación exigidos por la materia de Arquitectura de Software Empresarial.

---

## 🗒️ Formato de Control de Calidad Documental

Cada entregable fue auditado bajo la escala:
*   **Cumplido (Sí):** El artefacto está presente, es consistente, completo y no contiene inconsistencias técnicas.
*   **No Cumplido (No):** El artefacto está ausente o requiere modificaciones estructurales.

---

## 📊 Checklist Detallado de Control de Entregables

| Área del Proyecto | Requisito a Evaluar | Estado | Archivo Relacionado | Observaciones del Evaluador Interno |
| :--- | :--- | :---: | :--- | :--- |
| **Gestión de Proyecto** | **Problema con datos y límites claros:** Descripción cuantitativa de la problemática AS-IS, delimitando el alcance del gateway. | **Sí** | [Acta de Constitución](../01-gestion-proyecto/acta-constitucion.md) / [Alcance](../01-gestion-proyecto/alcance.md) | Contiene la línea base del problema (integraciones complejas de 6 a 12 meses) y el alcance explícito (inclusiones/exclusiones). |
| | **Objetivos SMART estructurados:** Al menos 3 objetivos debidamente desglosados en sus componentes SMART, con línea base y meta medible. | **Sí** | [Objetivos SMART](../01-gestion-proyecto/objetivos-smart.md) | Se estructuran tres objetivos: Onboarding (días), Conciliación (%) y Adopción (clientes), detallando riesgos de meta y dueños. |
| | **Planificación y Tiempos:** Cronograma a mediano plazo que describa hitos y dependencias de la ingeniería. | **Sí** | [Cronograma](../01-gestion-proyecto/cronograma.md) | Diseñado en Mermaid Gantt interactivo a 10 meses detallando hitos de integración y dependencias críticas. |
| **Requerimientos** | **Requerimientos Funcionales Catalogados:** RFs estructurados con identificadores únicos, descripciones y criterios de aceptación formales. | **Sí** | [Req. Funcionales](../02-requerimientos/requerimientos-funcionales.md) | Organizados por módulos, incluyendo criterios de aceptación detallados en formato Gherkin (Dado-Cuando-Entonces). |
| | **Requerimientos No Funcionales:** Especificación y categorización de RNF bajo normas de calidad ISO. | **Sí** | [Req. No Funcionales](../02-requerimientos/requerimientos-no-funcionales.md) | Mapeados conforme a la norma ISO/IEC 25010 (Seguridad, Disponibilidad, Rendimiento, Confiabilidad). |
| | **Casos de Uso del Núcleo:** Casos de uso robustos que modelen la interacción principal de cobro, pago y conciliación. | **Sí** | [Casos de Uso](../02-requerimientos/casos-de-uso.md) | Detalla precondiciones, flujo principal paso a paso, flujos alternativos exhaustivos y postcondiciones del core. |
| **Arquitectura** | **Diagramas de Procesos:** Diagramas de flujo AS-IS y TO-BE claros. | **Sí** | [Flujo AS-IS](../03-arquitectura/diagrama-flujo-as-is.md) / [TO-BE](../03-arquitectura/diagrama-flujo-to-be.md) | Diseñados en Mermaid, mostrando los cuellos de botella del modelo actual y las optimizaciones transaccionales de Flux. |
| | **Modelo C4 Completo (C1, C2, C3):** Vistas técnicas que desglosen conceptual, tecnológica y lógicamente la plataforma. | **Sí** | [C1 Contexto](../03-arquitectura/contexto-c1.md) <br/> [C2 Contenedores](../03-arquitectura/contenedores-c2.md) <br/> [C3 Componentes](../03-arquitectura/componentes-c3.md) | Diseñados en Mermaid de forma premium. C2 define tecnologías (Spring, Rust, Kafka, TimescaleDB). C3 detalla la orquestación hexagonal. |
| | **Decisiones de Diseño Justificadas (ADR):** Uso de plantillas formales Nygard para justificar tecnologías elegidas. | **Sí** | [Decisiones Diseño](../03-arquitectura/decisiones-diseno.md) | Muestra los ADR-001 (Arquitectura Orientada a Eventos Kafka) y ADR-002 (Double-Entry Ledger Rust TimescaleDB) aprobados. |
| **Gestión de Riesgos** | **Matriz de Riesgos:** Identificación y cuadrantes por probabilidad e impacto con plan de contingencia detallado. | **Sí** | [Matriz de Riesgos](../04-riesgos/matriz-riesgos.md) | Cubre riesgos de ciberseguridad, inestabilidad de Karpay, regulatorios (SFC/BanRep) y de adopción comercial. |
| | **Bitácora de Seguimiento:** Registro activo para auditar el monitoreo del proyecto. | **Sí** | [Registro de Riesgos](../04-riesgos/registro-riesgos.md) | Define dueños de riesgos, disparadores cuantitativos e incidentes históricos reales en la bitácora. |
| **Calidad (QA)** | **Plan de Aseguramiento de Calidad:** Plan de testing multinivel detallando coberturas mínimas aceptables. | **Sí** | [Plan de Calidad](./plan-calidad.md) | Estructura las pruebas unitarias (>85% cobertura), integración (Testcontainers), estrés (500 TPS) y seguridad. |
| | **Conexión explícita SIPOC ↔ Objetivos:** Trazabilidad formal entre el proceso clave y las metas estratégicas de negocio. | **Sí** | [Criterios de Aceptación](./criterios-aceptacion.md) | Matriz formal de trazabilidad que conecta de forma inequívoca cada Objetivo SMART con un paso del SIPOC y su criterio de validación. |
| **Presentación** | **Guión y Estructura Diapositivas:** Estructuración de la defensa del proyecto para el docente en formato reproducible. | **Sí** | [Presentación](../presentacion/presentacion.md) | Contiene la estructura detallada de las 10 diapositivas que cubren todo el ciclo de vida técnico del proyecto. |

---

## 3. Firmas de Cierre y Aprobación
*   **Responsable de Calidad (QA Lead):** Mateo Alzate (Grupo 1)
*   **Fecha de Cierre de Auditoría:** 22 de Mayo de 2026
*   **Resultado General:** **100% CUMPLIDO.** El repositorio se encuentra listo y óptimo para la entrega al docente.
