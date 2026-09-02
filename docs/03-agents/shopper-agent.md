# Shopper Agent

> Fuente: Master Spec §13. Agente requerido en el MVP (§42).

## Purpose

Analizar comportamiento, necesidades y oportunidades relacionadas con el shopper.

## Responsibilities

- Comportamiento de compra
- Journey del shopper
- Necesidades y motivaciones
- Fricciones y barreras
- Momentos y misiones de compra
- Drivers de decisión

## Inputs

- `structured data` producida por el Data Agent
- Datos de shopper cargados en el Data Hub (paneles, estudios, encuestas)
- Contexto del Challenge (categoría, mercado, shopper objetivo definidos en el Challenge Builder)

## Outputs

- `shopper insights` (candidatos a Insight, ver `04-data/intelligence-model.md`)
- `behavioral patterns`
- `shopper opportunities` (candidatos a Opportunity, categoría "shopper")

## Tools

- `data_analysis`
- `knowledge_retrieval` (para consultar frameworks de shopper behavior en Knowledge Base, si existen)
- `structured_extraction`

## Permissions

Lectura de `structured data` del Data Agent y de Project/Client Memory relevante a shopper behavior. No genera Recommendations directamente (eso corresponde a Opportunity/Trade Agent).

## Trigger

Se activa cuando el Challenge involucra explícitamente shopper (categoría, journey, comportamiento de compra) y existen datos de shopper disponibles o investigables.

## Dependencies

Puede depender parcialmente del Data Agent (si requiere datos estructurados previamente) o correr en paralelo si opera sobre datos ya estructurados/documentos cualitativos.

## Validation

- Todo `shopper insight` debe declarar su tipo epistémico (FACT/INTERPRETATION/HYPOTHESIS) según §50.
- Todo patrón de comportamiento debe referenciar la fuente de datos específica.

## Memory

Lee Client Memory para conocimiento histórico de shopper de ese cliente (journeys previamente documentados, segmentaciones ya validadas).

## Version

Versionado independiente.

## Execution Log

Registrado como `AgentRun` con input, output y validación estándar.
