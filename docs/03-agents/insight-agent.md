# Insight Agent

> Fuente: Master Spec §15. Agente requerido en el MVP (§42).

## Purpose

Transformar evidencia en inteligencia.

## Modelo de transformación

```text
Evidence
 ↓
Pattern
 ↓
Interpretation
 ↓
Insight
```

Un insight **no es** una descripción de datos. Debe explicar:
- Qué ocurre
- Por qué importa
- Qué significa para el negocio

## Inputs

- Evidence producida por Data Agent, Shopper Agent y Research Agent (todos los agentes "upstream")

## Outputs

- `Insight` (ver esquema completo en `04-data/intelligence-model.md`): `statement`, `explanation`, `evidence[]`, `confidence`, `category`, `business relevance`
- `Hypothesis` cuando la interpretación no alcanza el nivel de confianza necesario para ser un Insight validado

## Tools

- `knowledge_retrieval` (para contrastar con conocimiento previo del cliente/organización)
- `structured_extraction`

## Permissions

Lectura de toda la Evidence generada en el Workflow Run activo. No tiene acceso directo a Tools de búsqueda externa (eso es responsabilidad del Research Agent) — si necesita más evidencia, debe señalarlo como gap para que el Orchestrator decida si reactivar Research Agent.

## Trigger

Se activa cuando existe al menos una pieza de Evidence disponible de cualquiera de los agentes upstream (Data, Shopper, Research).

## Dependencies

Depende de al menos un output válido de Data Agent, Shopper Agent o Research Agent (regla dura, ver `02-architecture/orchestrator-architecture.md` §4).

## Validation

- Todo Insight debe referenciar al menos una Evidence.
- Todo Insight con `business relevance` alta debe tener `confidence` documentada explícitamente, no implícita.
- Si la evidencia es insuficiente o contradictoria, el output debe ser una Hypothesis marcada como tal, no un Insight.

## Memory

Lee Project Memory y Client Memory para contexto histórico. Puede sugerir promoción de un Insight recurrente a Client/Organization Memory (decisión final la toma el usuario, no el agente automáticamente).

## Version

Versionado independiente — cambios en la lógica de síntesis de este agente son especialmente sensibles porque afectan directamente la calidad percibida de "inteligencia" del producto.

## Execution Log

Registrado como `AgentRun`. Este es uno de los agentes con mayor prioridad de evaluación (AI Evaluation, ver `06-development/testing-and-evaluation.md`) dado que es el punto donde datos se convierten en interpretación.
