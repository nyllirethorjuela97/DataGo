# Functional Architecture

> Fuente: Master Spec §6, §7, §8, §40. Este documento detalla el modelo funcional conceptual con estados, entradas/salidas y puntos de validación — sin entrar en stack técnico (ver `technical-architecture.md`).

## 1. Principio rector

DataGo se organiza alrededor de **Challenge Driven Intelligence**: el usuario no pide un reporte, plantea un reto. El sistema decide qué necesita para resolverlo.

```text
Challenge → Data → Research → Agent Orchestration → Analysis → Evidence
   → Insight → Hypothesis → Opportunity → Recommendation → Strategy
   → Solution → Deliverable
```

Ningún módulo debe poder saltarse este flujo ni presentarse como una función aislada (ver Master Spec §5, "Differentiation").

## 2. Pipeline funcional con estados

Cada flecha del modelo conceptual (§7 del spec) corresponde a una transición de estado observable y trazable:

| Etapa | Estado de entrada | Acción | Estado de salida | Quién ejecuta |
|---|---|---|---|---|
| 1. Challenge | `draft` | Usuario completa Challenge Builder | `challenge.defined` | Usuario |
| 2. Understanding | `challenge.defined` | Orchestrator interpreta el reto y determina requerimientos de datos/agentes | `challenge.analyzed` | Orchestrator |
| 3. Data Sources | `challenge.analyzed` | Usuario/sistema adjunta o conecta fuentes | `data.ready` (o `data.insufficient`) | Usuario + Data Hub |
| 4. Agent Selection | `data.ready` | Orchestrator arma el plan de agentes (workflow) | `workflow.planned` | Orchestrator |
| 5. Orchestration | `workflow.planned` | Ejecución secuencial/paralela de agentes según dependencias | `workflow.running` → `workflow.completed` \| `workflow.failed` | Orchestrator + Agentes |
| 6. Analysis | por cada Agent Run | Agente procesa inputs con tools permitidas | `agent_run.completed` | Agente |
| 7. Evidence | `agent_run.completed` | Se registran hallazgos con fuente y confianza | `evidence.recorded` | Agente + Orchestrator |
| 8. Intelligence (Insight) | `evidence.recorded` (≥1) | Insight Agent sintetiza evidencia en insights | `insight.created` | Insight Agent |
| 9. Hypothesis | insight con baja confianza o interpretación no verificable | Se marca explícitamente como hipótesis | `hypothesis.open` | Insight/Research Agent |
| 10. Opportunities | `insight.created` | Opportunity Agent traduce insight → implicación de negocio → oportunidad | `opportunity.identified` | Opportunity Agent |
| 11. Recommendations | `opportunity.identified` | Se vincula una recomendación accionable a la oportunidad | `recommendation.drafted` | Opportunity/Trade Agent |
| 12. Strategy / Solution | (post-MVP) | Strategy/Solution Agent | `strategy.drafted` | Futuro |
| 13. Deliverable | recomendaciones + resumen ejecutivo aprobados | Generación de salida exportable | `deliverable.generated` | Proposal Agent / sistema |

Este es el **contrato funcional** que el diseño técnico (Convex schema, orquestador) debe implementar. Cada fila es candidata directa a un valor de enum `status` en las tablas correspondientes (ver `04-data/data-model.md`).

## 3. Experiencia de usuario end-to-end (mapeo funcional)

```text
LOGIN → WORKSPACE → PROJECT → NEW CHALLENGE → DATA SOURCES
   → ORCHESTRATOR → AGENTS → ANALYSIS → INTELLIGENCE BOARD
   → OPPORTUNITIES → RECOMMENDATIONS → ACTION → DELIVERABLES
```

Cada paso de este journey corresponde 1:1 a una pantalla dentro de un Project (ver `05-ux/navigation-map.md`) y a una etapa de la tabla anterior.

## 4. Regla de "no todos los agentes siempre"

El Orchestrator decide dinámicamente qué agentes correr según el Challenge (Master Spec §10, §40). Esto implica funcionalmente:

- El plan de workflow **no es estático**; se genera por ejecución (`workflow_plan` = lista ordenada de `agent_id` + dependencias, calculada en tiempo de planificación).
- Un proyecto puede tener múltiples Agent Runs de un mismo agente (reintentos, refinamientos) — no hay relación 1:1 fija entre Project y Agent.
- La ausencia de un agente en el plan debe quedar documentada (razón: "no aplica", "sin datos suficientes", "fuera de alcance del challenge") para trazabilidad (Regla 5 y 6 del Master Spec §49).

## 5. Human-in-the-loop como estado funcional, no como excepción

Master Spec §23 exige puntos de intervención humana. Funcionalmente esto se modela como un estado intermedio obligatorio para ciertos artefactos:

```text
AI Recommendation (status: pending_review)
        ↓
Human Review
        ↓
   ┌────┴────┬─────────┐
Approve   Modify    Reject
   ↓          ↓         ↓
status:    status:   status:
approved   modified  rejected
   ↓          ↓
   └────┬─────┘
 Continue Workflow
```

**Disparadores obligatorios de revisión humana** (no opcionales en el MVP):
- Hipótesis marcada como crítica por el Insight Agent.
- Confianza (`confidence`) de un insight por debajo de un umbral configurable.
- Conflicto entre evidencias de distintas fuentes sobre el mismo punto.
- Cualquier Opportunity con `potential impact` clasificado como alto antes de pasar a Recommendation.

## 6. Separación FACT / INTERPRETATION / HYPOTHESIS / RECOMMENDATION

Master Spec §50 — esta separación es funcional, no solo de UI. Cada Evidence, Insight y Opportunity debe portar un campo `epistemic_type` con uno de estos cuatro valores, y el frontend **nunca** debe mostrarlos con el mismo tratamiento visual ni redactarlos con el mismo nivel de certeza lingüística. Ver detalle de campos en `04-data/intelligence-model.md`.

## 7. Qué NO hace el modelo funcional (alcance negativo)

- No ejecuta agentes automáticamente sin que exista un Challenge estructurado y validado.
- No genera un Deliverable sin al menos una Opportunity con evidencia asociada.
- No mezcla memoria de un proyecto con la de otro (ver `04-data/project-memory.md` y `02-architecture/multi-tenancy-architecture.md`).
- No presenta una Hypothesis como Insight validado.
