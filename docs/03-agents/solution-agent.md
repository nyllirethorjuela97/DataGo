# Solution Agent

> Fuente: Master Spec §19. Agente "later" (post-MVP, §42).

## Purpose

Convertir estrategia en soluciones concretas.

## Responsibilities

Puede producir:
- Conceptos
- Iniciativas
- Experiencias
- Activaciones
- Soluciones comerciales concretas

## Inputs

- `Strategy` (del Strategy Agent) u `Opportunity`/`Recommendation` directamente cuando no exista un paso de Strategy explícito en el workflow

## Outputs

- `Solution` (concepto, descripción, iniciativas asociadas) — entidad a definir en detalle en fase futura

## Tools

- `content_generation`
- `knowledge_retrieval`

## Permissions

Lectura de Strategy/Opportunity/Recommendation del proyecto.

## Trigger

Se activa cuando existe una Strategy (o, en flujos simplificados, una Opportunity de alta prioridad) que requiere una solución concreta y accionable.

## Dependencies

Depende de Strategy Agent (o directamente de Opportunity Agent en flujos que omiten el paso de Strategy).

## Validation

Toda Solution debe vincularse explícitamente a la Strategy u Opportunity que la origina.

## Memory

Lee Client Memory para reutilizar conceptos/soluciones ya validadas con ese cliente.

## Version

Versionado independiente.

## Execution Log

Registrado como `AgentRun` estándar.

## Nota de alcance

Fuera del MVP (§42, §45). Documentado por completitud del grafo de agentes.
