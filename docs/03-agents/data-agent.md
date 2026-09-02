# Data Agent

> Fuente: Master Spec §12. Agente requerido en el MVP (§42).

## Purpose

Analizar y estructurar la información disponible dentro del proyecto.

## Responsibilities

- Limpieza de datos
- Estructuración
- Clasificación
- Identificación de patrones
- Análisis descriptivo
- Preparación de datos para agentes posteriores (Shopper, Insight)

## Inputs

- `datasets` (subidos vía Data Hub)
- `files`
- `structured data` ya existente en el proyecto

## Outputs

- `structured data` (normalizada, lista para consumo de otros agentes)
- `data summaries`
- `patterns`
- `signals` (candidatos a Evidence)

## Tools

- `data_analysis`
- `file_parsing`
- `calculations`
- `structured_extraction`

## Permissions

Acceso de solo lectura a `DataSources`/`Files`/`Datasets` del proyecto activo. No tiene permiso de escritura sobre Opportunities ni Recommendations (fuera de su responsabilidad, ver `agent-principles.md` §5).

## Trigger

Se activa cuando el Workflow Planner determina que existen fuentes de datos sin procesar relevantes para el Challenge (típicamente el primer agente en ejecutarse dentro de un Workflow Run).

## Dependencies

Ninguna dependencia de otro agente — puede correr en paralelo con Shopper Agent y Research Agent (ver `02-architecture/orchestrator-architecture.md` §4).

## Validation

- El output de `structured data` debe ser trazable al `DataSource`/`File` original (referencia explícita).
- Todo `pattern`/`signal` reportado debe incluir referencia a los datos que lo sustentan, para poder convertirse en Evidence.

## Memory

Lee Project Memory (contexto del challenge, decisiones previas sobre qué datos priorizar). No escribe memoria directamente.

## Version

Versionado independiente del resto de agentes (ver `02-architecture/observability-and-versioning.md` §4).

## Execution Log

Cada ejecución se registra como `AgentRun` con input (referencias a datasets), output (resumen estructurado + patrones), duración, tokens y estado de validación.
