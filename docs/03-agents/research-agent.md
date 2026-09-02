# Research Agent

> Fuente: Master Spec §14. Agente requerido en el MVP (§42).

## Purpose

Investigar la información externa necesaria para responder el Challenge.

## Responsibilities

- Investigación de mercado/categoría
- Búsqueda de información externa
- Análisis de fuentes
- Identificación de señales externas relevantes

## Regla explícita del spec

Debe diferenciar claramente:

- Información encontrada (FACT)
- Interpretación (INTERPRETATION)
- Hipótesis (HYPOTHESIS)

Esta separación no es opcional — es la aplicación directa de §50 al trabajo de este agente en particular, dado que es el agente más expuesto a mezclar hechos externos con interpretación propia.

## Inputs

- Contexto del Challenge (categoría, mercado, horizonte temporal)
- Consultas específicas generadas por el Orchestrator a partir del Challenge Analyzer

## Outputs

- `evidence` externa (con `source`, `date`, `relevance`)
- Señales de mercado/categoría
- Hipótesis externas a validar por Insight Agent

## Tools

- `search`
- `structured_extraction`
- `knowledge_retrieval`

## Permissions

Acceso a la Tool `search` (fuentes externas). No tiene acceso de escritura a datos internos del cliente/proyecto más allá de registrar su propio output.

## Trigger

Se activa cuando el Challenge Analyzer determina que la información interna disponible es insuficiente para responder el Challenge, o cuando el Challenge explícitamente requiere contexto de mercado/categoría externo.

## Dependencies

Puede correr en paralelo con Data Agent y Shopper Agent.

## Validation

- Toda pieza de `evidence` externa debe incluir `source` verificable.
- Todo enunciado que no provenga directamente de una fuente debe marcarse como `interpretation` o `hypothesis`, nunca como `fact`.

## Memory

Puede escribir a Organization Memory hallazgos de mercado/categoría reutilizables entre proyectos (sujeto a aprobación del nivel de compartición, ver `04-data/project-memory.md`).

## Version

Versionado independiente.

## Execution Log

Registrado como `AgentRun`, incluyendo el listado de fuentes consultadas como parte del output (para trazabilidad de Evidence).
