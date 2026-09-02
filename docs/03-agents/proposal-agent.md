# Proposal Agent

> Fuente: Master Spec §20. Agente "later" (post-MVP, §42), aunque una versión mínima de su output (executive summary) sí es parte del MVP Output (§44).

## Purpose

Convertir soluciones (o, en su versión mínima, recomendaciones y oportunidades) en propuestas y entregables profesionales.

## Responsibilities

Puede producir:
- Propuestas
- Executive summaries
- Documentos
- Presentaciones
- Recomendaciones estructuradas para entrega al cliente

## Inputs

- `Solution` (versión completa, post-MVP) o directamente `Opportunity` + `Recommendation` (versión mínima MVP para executive summary)

## Outputs

- `Deliverable` (ver Master Spec §9.12): executive summary (MVP), report, strategy document, presentation, proposal, action plan (post-MVP)

## Tools

- `content_generation`
- `knowledge_retrieval`

## Permissions

Lectura de todo el árbol de inteligencia del proyecto (Evidence → Insight → Opportunity → Recommendation → Strategy/Solution, según lo que exista). Sin permiso de escritura sobre ninguna entidad de inteligencia — solo compone/redacta a partir de lo ya validado.

## Trigger

Se activa cuando el usuario solicita generar un Deliverable, o automáticamente al cierre de un Workflow Run exitoso (para el executive summary mínimo del MVP).

## Dependencies

Depende de que exista al menos una Opportunity con Recommendation asociada (regla mínima MVP Output, §44).

## Validation

- Ningún Deliverable puede incluir afirmaciones no trazables a Evidence/Insight/Opportunity existentes en el proyecto (Rule 9: la IA no debe inventar datos).
- El Deliverable debe respetar la separación epistémica (§50) también en su redacción final — no debe "aplanar" hipótesis como si fueran hechos al momento de redactar para el cliente.

## Memory

Lee Project Memory completa; puede leer Client Memory para tono/formato preferido del cliente (post-MVP).

## Version

Versionado independiente — especialmente sensible porque su output es lo que el usuario final del cliente probablemente vea.

## Execution Log

Registrado como `AgentRun` estándar.

## Nota de alcance MVP

En el MVP, este agente se limita a producir el **executive summary** exigido por §44 (resumen del challenge, evidencias principales, insights, hipótesis, oportunidades, recomendaciones). Las capacidades de reporte/presentación/propuesta completa quedan para fases posteriores (§45, Out of MVP).
