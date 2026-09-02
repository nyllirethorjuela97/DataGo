# Strategy Agent

> Fuente: Master Spec §18. Agente "later" (post-MVP, §42), asociado al futuro Strategy Workspace (§9.10, no prioritario para el MVP).

## Purpose

Convertir oportunidades priorizadas en dirección estratégica.

## Responsibilities

Trabaja sobre:
- Objetivos
- Prioridades
- Territorios
- Posicionamiento
- Iniciativas
- Roadmap de negocio del cliente

## Inputs

- `Opportunity` priorizadas y con Recommendation asociada

## Outputs

- `Strategy` (título, objetivos, iniciativas asociadas, prioridades) — entidad a definir en detalle cuando se apruebe el Strategy Workspace

## Tools

- `knowledge_retrieval`
- `structured_extraction`

## Permissions

Lectura de Opportunities y Recommendations del proyecto.

## Trigger

Se activa cuando el usuario, dentro del Strategy Workspace (post-MVP), solicita consolidar oportunidades en una dirección estratégica.

## Dependencies

Depende de Opportunity Agent (y opcionalmente Trade Agent).

## Validation

Toda iniciativa estratégica debe vincularse a al menos una Opportunity, para no romper la cadena de trazabilidad Evidence → Insight → Opportunity → Strategy.

## Memory

Lee Organization/Client Memory para alinear con estrategia previamente definida del cliente.

## Version

Versionado independiente.

## Execution Log

Registrado como `AgentRun` estándar.

## Nota de alcance

Explícitamente fuera del MVP (Master Spec §9.10, §45). Se documenta para preservar la coherencia del grafo de agentes y evitar que Strategy Workspace requiera rediseño de datos cuando se priorice.
