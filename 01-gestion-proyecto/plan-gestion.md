# ⚙️ Plan de Gestión del Proyecto

Este documento describe la metodología, procesos y controles organizacionales que se utilizarán para administrar el proyecto **Flux Payments** de principio a fin, asegurando que se entregue a tiempo, dentro del alcance y cumpliendo con los estándares de calidad definidos por la materia.

---

## 1. Metodología de Trabajo

El proyecto se gestionará bajo un enfoque **híbrido**:
*   **Fase de Diseño y Arquitectura (Mes 1–2):** Enfoque tradicional (Waterfall) para asegurar que las definiciones técnicas de la base de datos (Ledger), los contratos de las APIs y la estructura C4 estén completamente definidos e inmutables antes de la codificación.
*   **Fase de Desarrollo e Integraciones (Mes 3–8):** Metodología ágil (Scrum) estructurada en **sprints de 2 semanas**, con planeación, daily standups, revisiones y retrospectivas.
*   **Fase de Pruebas y Lanzamiento (Mes 9–10):** Enfoque Kanban, priorizando la corrección de bugs críticos, las pruebas de seguridad (Pentesting) y el onboarding controlado de los clientes piloto.

---

## 2. Gestión y Control de Alcance

Cualquier cambio propuesto al alcance definido en [Alcance del Proyecto](./alcance.md) debe pasar por el siguiente proceso formal para evitar el *Scope Creep* (corrupción del alcance):

1.  **Solicitud de Cambio:** El proponente (PM, Arquitecto o Sponsor) debe radicar una solicitud que explique: descripción del cambio, justificación empresarial, e impacto estimado.
2.  **Análisis de Impacto:** El Arquitecto de Software y el DevOps Lead evaluarán el impacto técnico en la infraestructura, latencia, base de datos y seguridad, así como el impacto en el cronograma.
3.  **Aprobación/Rechazo:** El Comité de Cambios (PM, Sponsor y Tech Lead) evaluará si el cambio es viable. Si se aprueba, se replanificarán los Sprints y se actualizará el cronograma oficial.

---

## 3. Plan de Gestión de las Comunicaciones

Para mantener al equipo sincronizado, se definen los siguientes canales y herramientas de comunicación oficiales:

| Canal / Herramienta | Objetivo | Participantes | Frecuencia |
| :--- | :--- | :--- | :--- |
| **Jira / GitHub Projects** | Tablero kanban para el seguimiento de tareas técnicas e incidentes. | Todo el equipo | Diario (actualización activa) |
| **Slack / Microsoft Teams** | Comunicación rápida, notificaciones de CI/CD e integraciones. | Todo el equipo | Diario (tiempo real) |
| **Comités Técnicos** | Revisión de decisiones de arquitectura (ADR) y diseño de base de datos. | Arquitectos y Tech Leads | Semanal |
| **Daily Standup** | Reuniones rápidas de sincronización para remover impedimentos. | Equipo de Desarrollo, PM | Diario (15 mins) |
| **Sprint Review & Demo** | Presentación de software funcional a los stakeholders y feedback. | Desarrolladores, PM, Sponsors | Quincenal (Fin de Sprint) |

---

## 4. Gestión del Cronograma y Desviaciones

Para garantizar la entrega puntual al **28 de Mayo de 2026**:
*   **Seguimiento del Avance:** Se medirá a través del *Velocity Chart* y el *Burn-down Chart* en los sprints de Scrum.
*   **Gestión de Desviaciones:** 
    *   *Desviación < 10% (retraso menor a 1 sprint):* El equipo de desarrollo reasignará tareas dentro del Sprint actual o el PM priorizará las historias de usuario de prioridad "Media" y "Baja" al siguiente backlog para no mover la fecha final.
    *   *Desviación > 10% (retraso mayor a 2 semanas):* Se activará un plan de contingencia inmediata. Esto incluye la redistribución de recursos, incremento del personal de soporte o la reducción de alcance (trasladando requerimientos no esenciales como el Portal de Ops avanzado a una fase posterior al lanzamiento oficial).

---

## 5. Gestión del Riesgo

La gestión de riesgos es transversal al proyecto y se controla a través del seguimiento semanal de la [Matriz de Riesgos](../04-riesgos/matriz-riesgos.md). Cada semana en la reunión de sincronización, el Tech Lead revisará si algún disparador de riesgo (trigger) se ha activado para desplegar los planes de mitigación inmediatamente.
