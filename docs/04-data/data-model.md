# Data Model

> Fuente: Master Spec §30, §56 (decisión #4). Modelo conceptual de entidades — no es un schema de Convex final (eso se define en Fase 1/técnica), pero establece los campos y relaciones que cualquier implementación debe respetar.

## 1. Entidades principales (Master Spec §30)

```text
Users
Organizations
Workspaces
Projects
Challenges
DataSources
Files
Datasets
Agents
AgentRuns
Tasks
Evidence
Insights
Hypotheses
Opportunities
Recommendations
Strategies
Proposals
Deliverables
Knowledge
AuditLogs
```

A esta lista base se agregan, por necesidad funcional detectada en `02-architecture/`, las siguientes entidades de soporte:

```text
WorkflowRuns      (ver orchestrator-architecture.md §3)
ProjectMemory / ClientMemory / OrganizationMemory   (ver project-memory.md)
Tools             (ver tool-architecture.md)
```

## 2. Diagrama de relaciones (conceptual)

```text
Organization 1──* Workspace 1──* Project
                                    │
                                    ├──1 Challenge
                                    ├──* DataSource ──* File / Dataset
                                    ├──* WorkflowRun ──* Task ──1 AgentRun
                                    ├──* Evidence
                                    ├──* Insight ──* Evidence (referencia)
                                    ├──* Hypothesis
                                    ├──* Opportunity ──* Insight (referencia)
                                    ├──* Recommendation ──1 Opportunity
                                    ├──* Strategy (post-MVP) ──* Opportunity
                                    ├──* Deliverable
                                    └──1 ProjectMemory

Agent 1──* AgentRun
Agent *──* Tool          (vía permissions)
User *──* Workspace       (vía membership + rol)
```

## 3. Definición de campos por entidad clave

### Project
| Campo | Tipo | Notas |
|---|---|---|
| `organizationId` | ref | Obligatorio — aislamiento multi-tenant |
| `workspaceId` | ref | Obligatorio |
| `name` | string | |
| `clientRef` | string/ref opcional | Ver `multi-tenancy-architecture.md` §3 |
| `status` | enum | `active`, `archived` |
| `createdBy` / `createdAt` | ref / timestamp | |

### Challenge
| Campo | Tipo | Notas |
|---|---|---|
| `projectId` | ref | |
| `mainQuestion` | string | Pregunta principal (§9.3) |
| `objective` | string | |
| `context` | text | |
| `category` | string | |
| `market` | string | |
| `shopper` | string | |
| `constraints` | text | |
| `timeHorizon` | string | |
| `availableInformation` | text | |
| `expectedOutcome` | text | |
| `status` | enum | `draft`, `defined`, `analyzed` (ver `functional-architecture.md` §2) |

### DataSource / File / Dataset
| Campo | Tipo | Notas |
|---|---|---|
| `projectId` | ref | |
| `type` | enum | `file`, `dataset`, `external_api` (futuro) |
| `storageRef` | ref | Referencia a Convex Storage |
| `processedStatus` | enum | `pending`, `processing`, `ready`, `failed` (ver `file-processing.md`) |

### Agent (registro, no ejecución)
Ver `03-agents/orchestrator-agent-protocol.md` §4 para el contrato completo (`AgentDefinition`).

### AgentRun / Task / WorkflowRun
Ver `02-architecture/orchestrator-architecture.md` §3 — definidos allí en detalle por ser el núcleo del modelo de orquestación.

### Evidence, Insight, Hypothesis, Opportunity, Recommendation
Ver `intelligence-model.md` — estas entidades tienen un documento dedicado por su rol central en el producto y su relación directa con §50 (separación epistémica).

### Knowledge
| Campo | Tipo | Notas |
|---|---|---|
| `scope` | enum | `project`, `client`, `organization` (ver `project-memory.md`) |
| `content` | text/structured | |
| `sourceProjectId` | ref opcional | De qué proyecto se originó, si aplica |
| `sharingApproved` | boolean | Compartición ascendente es opt-in, nunca implícita |

### AuditLogs
| Campo | Tipo | Notas |
|---|---|---|
| `actorId` | ref | |
| `action` | string | |
| `resourceType` / `resourceId` | string / ref | |
| `organizationId` / `workspaceId` | ref | Para poder auditar por tenant |
| `timestamp` | timestamp | |
| `result` | enum | `success`, `denied`, `error` |

## 4. Reglas transversales de modelado

1. Toda tabla con datos de negocio lleva `organizationId` + `workspaceId` indexados (Rule de `multi-tenancy-architecture.md` §2).
2. Toda entidad de inteligencia (Evidence, Insight, Hypothesis, Opportunity, Recommendation) lleva `epistemicType` explícito (§50).
3. Ninguna entidad de resultado (Insight, Opportunity, Recommendation, Deliverable) se sobrescribe — los cambios generan nuevas versiones o nuevos registros vinculados, nunca mutación silenciosa del original (Rule 2 y §52).
4. Toda entidad generada por un agente lleva referencia a `agentId` + `agentVersion` + `agentRunId` que la produjo.

## 5. Qué queda para la fase técnica (no se resuelve aquí)

- Tipos de índice específicos de Convex.
- Estrategia de paginación para listados grandes (Evidence/Insights por proyecto).
- Límites de tamaño de documento y estrategia de particionado si un proyecto crece mucho (fuera de alcance del MVP).
