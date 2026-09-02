# Opportunity Agent

> Fuente: Master Spec §16. Agente requerido en el MVP (§42).

## Purpose

Convertir insights en oportunidades de negocio.

## Modelo de transformación

```text
Insight
 ↓
Business Implication
 ↓
Opportunity
```

Debe priorizar oportunidades según criterios definidos (impacto potencial, factibilidad — ver esquema de Opportunity en `04-data/intelligence-model.md`).

## Inputs

- `Insight` (validados, no Hypotheses sin resolver)

## Outputs

- `Opportunity`: `title`, `description`, `source insights[]`, `potential impact`, `feasibility`, `priority`, `recommended next action`
- Categorías posibles (Master Spec §9.9): comercial, shopper, canal, portafolio, comunicación, experiencia

## Tools

- `knowledge_retrieval`
- `calculations` (para scoring de priorización — ver Scoring Logic en `02-architecture/observability-and-versioning.md` §4, debe ser versionable)

## Permissions

Lectura de Insights del Workflow Run activo y de Project/Client Memory para contexto de oportunidades previamente identificadas (evitar duplicar oportunidades ya trabajadas).

## Trigger

Se activa cuando existe al menos un Insight con `business relevance` suficiente.

## Dependencies

Depende de Insight Agent.

## Validation

- Toda Opportunity debe referenciar al menos un Insight de origen (`source insights`).
- Oportunidades de `potential impact` alto deben pasar por revisión humana antes de avanzar a Recommendation (ver `02-architecture/functional-architecture.md` §5).

## Memory

Lee Project/Client Memory para evitar duplicar oportunidades ya identificadas en ciclos anteriores del mismo cliente.

## Version

Versionado independiente, particularmente la lógica de scoring/priorización (debe poder auditarse por qué una oportunidad quedó priorizada por encima de otra).

## Execution Log

Registrado como `AgentRun`, incluyendo el criterio de priorización aplicado como parte del output para trazabilidad.
