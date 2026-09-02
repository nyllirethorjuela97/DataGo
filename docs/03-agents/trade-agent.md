# Trade Agent

> Fuente: Master Spec §17. Agente "later" en MVP Agents (§42) — no requerido para el primer corte del MVP, pero la arquitectura debe permitir incorporarlo sin reconstrucción.

## Purpose

Interpretar implicaciones comerciales y de trade derivadas de las oportunidades identificadas.

## Responsibilities

Puede analizar:
- Canales
- Categorías
- Puntos de venta
- Ejecución en punto de venta
- Promociones
- Portafolio
- Shopper journey (en su dimensión comercial/de conversión)
- Conversión

## Inputs

- `Opportunity` (priorizadas por Opportunity Agent)
- Evidence relacionada a canal/trade

## Outputs

- `Recommendation` con foco en ejecución comercial/trade
- Implicaciones de portafolio/canal asociadas a una Opportunity existente

## Tools

- `data_analysis`
- `calculations`
- `knowledge_retrieval`

## Permissions

Lectura de Opportunities del proyecto. Escritura de Recommendations vinculadas a Opportunities existentes (no crea Opportunities nuevas — esa es responsabilidad del Opportunity Agent).

## Trigger

Se activa cuando una Opportunity está categorizada como "canal", "portafolio" o "trade" y requiere una recomendación de ejecución concreta.

## Dependencies

Depende de Opportunity Agent.

## Validation

- Toda Recommendation debe estar vinculada a una Opportunity existente y ser accionable (Master Spec §29).

## Memory

Lee Client Memory para contexto de ejecución comercial histórica del cliente (qué tácticas de trade ya se probaron).

## Version

Versionado independiente.

## Execution Log

Registrado como `AgentRun` estándar.

## Nota de alcance

Este agente no bloquea el MVP. Su documentación se incluye ahora para que el modelo de datos y el Agent Registry lo soporten desde el inicio sin requerir cambios de esquema cuando se active (Master Spec §42: "La arquitectura debe permitir incorporar los agentes posteriores sin reconstruir el sistema").
