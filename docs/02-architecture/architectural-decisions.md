# Architectural Decisions Log (ADR Register)

> Fuente: Master Spec §56 "Architectural Decisions Required" y §57 "Architectural Checkpoint". Este documento es el punto único de seguimiento de las 13 decisiones que el Master Spec exige resolver **antes** de iniciar desarrollo (§0, §60: "DO NOT DEVELOP YET").

Cada fila indica: la decisión, dónde se documenta el detalle, una recomendación preliminar (para acelerar la revisión) y su estado. **Ninguna decisión aquí debe considerarse aprobada hasta que quede marcada explícitamente como tal por quien apruebe la arquitectura** — este documento propone, no decide por sí mismo.

| # | Decisión | Documento de detalle | Recomendación preliminar | Estado |
|---|---|---|---|---|
| 1 | Arquitectura exacta del Orchestrator | `orchestrator-architecture.md` | Máquina de estados persistida (`WorkflowRun` + `Task` + `AgentRun`), planificación dinámica basada en el grafo de dependencias de agentes | Propuesto |
| 2 | Estrategia de ejecución síncrona/asíncrona | `technical-architecture.md` §5 | CRUD síncrono; Agent Runs y Workflow Runs asíncronos con estado reactivo | Propuesto |
| 3 | Estrategia de memoria | `04-data/project-memory.md` | Niveles explícitos (Project / Client / Organization / Learning / Decision Memory), compartición ascendente opt-in, nunca implícita | Propuesto |
| 4 | Modelo final de datos | `04-data/data-model.md` | Ver esquema conceptual completo con 19 entidades | Propuesto |
| 5 | Sistema de permisos | `security-architecture.md` §3–4 | RBAC simple (Owner/Admin/Editor/Viewer) validado server-side | Propuesto |
| 6 | Sistema multi-tenant | `multi-tenancy-architecture.md` | Aislamiento por `organizationId`/`workspaceId` desde el esquema inicial en todas las tablas de negocio | Propuesto |
| 7 | AI provider abstraction | `ai-abstraction-layer.md` | Interfaz interna estable, proveedor intercambiable, un solo proveedor activo en el MVP | Propuesto |
| 8 | Tool architecture | `tool-architecture.md` | Tools versionadas, con permisos por agente, validadas en tiempo de ejecución | Propuesto |
| 9 | File processing | `04-data/file-processing.md` | Ingesta asíncrona desacoplada de la request principal; parsing con validación de tipo antes de habilitar como fuente para agentes | Propuesto |
| 10 | Evidence model | `04-data/intelligence-model.md` | Evidence con `source`, `location`, `content/reference`, `date`, `confidence`, `relevance`; enlazado obligatorio a Insight con `business relevance` alta | Propuesto |
| 11 | Agent evaluation | `06-development/testing-and-evaluation.md` §AI Evaluation | Evaluación de factuality, evidence grounding, consistency, usefulness, hallucination rate por agente y por versión | Propuesto |
| 12 | Observability | `observability-and-versioning.md` §1–3 | Registro por Agent Run + agregación por Workflow/Project/Organization | Propuesto |
| 13 | Deployment architecture | `technical-architecture.md` §1, §6 | Vercel (frontend) + Convex (backend/datos), sin infraestructura adicional en el MVP | Propuesto |

## Checklist de aprobación (Master Spec §57)

Antes de iniciar Fase 1 (Foundation) del roadmap, deben quedar explícitamente aprobados:

- [ ] Product Architecture (`01-product/product-architecture.md`)
- [ ] Functional Architecture (`functional-architecture.md`)
- [ ] Technical Architecture (`technical-architecture.md`)
- [ ] Agent Architecture (`03-agents/`)
- [ ] Orchestrator Architecture (`orchestrator-architecture.md`)
- [ ] Data Architecture (`04-data/`)
- [ ] UX Architecture (`05-ux/`)
- [ ] Security Architecture (`security-architecture.md`)
- [ ] MVP Scope (`06-development/mvp-scope.md`)
- [ ] Development Roadmap (`06-development/roadmap.md`)

## Proceso de cambio

Cualquier cambio a una decisión ya marcada como aprobada debe:
1. Documentarse en este archivo (nueva fila de historial o nota bajo la decisión afectada).
2. Justificarse explícitamente (Rule 2, §49: "No cambiar arquitectura silenciosamente").
3. Evaluarse su impacto en los documentos derivados listados en la columna "Documento de detalle".
