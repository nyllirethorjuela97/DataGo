# Agent Principles

> Fuente: Master Spec §10, §11.

## 1. Topología conceptual de agentes

```text
                       ORCHESTRATOR
                            │
          ┌─────────────────┼─────────────────┐
          ↓                 ↓                 ↓
      DATA AGENT       SHOPPER AGENT    RESEARCH AGENT
          │                 │                 │
          └─────────────────┼─────────────────┘
                            ↓
                      INSIGHT AGENT
                            ↓
                    OPPORTUNITY AGENT
                            ↓
                       TRADE AGENT
                            ↓
                     STRATEGY AGENT
                            ↓
                     SOLUTION AGENT
                            ↓
                    PROPOSAL AGENT
```

Este es el **modelo conceptual**, no un pipeline obligatorio. El Orchestrator decide, por Challenge, qué subconjunto de este grafo ejecutar (ver `02-architecture/orchestrator-architecture.md` §4).

## 2. Propiedades obligatorias de todo agente (Master Spec §11)

Cada agente debe ser:

- **Especializado** — responsabilidad única y acotada (Rule 8, §49).
- **Modular** — reemplazable/actualizable sin romper a otros agentes.
- **Trazable** — toda ejecución queda registrada (Rule 5).
- **Controlable** — el Orchestrator puede pausar/reintentar/detener su ejecución.
- **Versionable** — ver `02-architecture/observability-and-versioning.md` §4.
- **Evaluable** — ver `06-development/testing-and-evaluation.md` §AI Evaluation.
- **Limitado por permisos** — solo accede a las Tools que tiene explícitamente asignadas (`02-architecture/tool-architecture.md`).

## 3. Contrato obligatorio de definición de un agente

```text
Agent
 ├── Purpose
 ├── Inputs
 ├── Outputs
 ├── Tools
 ├── Permissions
 ├── Trigger
 ├── Dependencies
 ├── Validation
 ├── Memory
 ├── Version
 └── Execution Log
```

Cada uno de los documentos `data-agent.md`, `shopper-agent.md`, etc. en esta carpeta completa este contrato para su agente correspondiente. Ningún agente puede incorporarse al Agent Registry (Agent Hub, §9.6) sin completar todos estos campos.

## 4. Clasificación por rol funcional

| Rol | Agentes | Responsabilidad general |
|---|---|---|
| **Recolección/Análisis primario** | Data, Shopper, Research | Producen Evidence a partir de datos e investigación |
| **Síntesis** | Insight | Convierte Evidence en Insight (patrón → interpretación → significado) |
| **Traducción a negocio** | Opportunity, Trade | Convierten Insight en implicación comercial y oportunidad priorizada |
| **Dirección** | Strategy, Solution | Convierten oportunidades en dirección estratégica y soluciones concretas (post-MVP) |
| **Entrega** | Proposal | Convierte soluciones/recomendaciones en documentos entregables (post-MVP en su forma completa) |

## 5. Principio de responsabilidad única (Rule 8)

Un agente no debe absorber responsabilidades de otro para "ahorrar un paso". Por ejemplo, el Data Agent no debe generar oportunidades de negocio directamente — eso rompe la trazabilidad Evidence → Insight → Opportunity y viola la separación epistémica de §50. Si un caso de uso parece requerir esto, es señal de que falta un agente intermedio o que el Challenge está mal descompuesto, no una excusa para fusionar responsabilidades.

## 6. Memoria por agente

Cada agente puede leer (nunca escribir directamente, salvo a través del Orchestrator/Result Store) los niveles de memoria relevantes a su tarea: Project Memory siempre; Client/Organization Memory solo si la Tool `knowledge_retrieval` se lo permite y el nivel de compartición está habilitado (ver `04-data/project-memory.md`).

## 7. Regla de no invención de datos (Rule 9)

> La IA no debe inventar datos.

Aplicado a nivel de agente: ningún agente puede producir un Insight, Opportunity o Recommendation sin que su output declare de qué Evidence específica proviene. Un agente que no encuentra evidencia suficiente debe declarar explícitamente incertidumbre (`confidence` bajo, o directamente abstenerse de producir un output afirmativo) en vez de completar el vacío con contenido plausible pero no verificado.
