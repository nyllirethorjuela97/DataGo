# Development Roadmap

> Fuente: Master Spec §47. Detalla las 11 fases (0–10) con sus entregables y sus dependencias de aprobación arquitectónica.

## Regla de entrada al roadmap

> **Ninguna fase posterior a la Fase 0 puede iniciar sin que el checkpoint de `02-architecture/architectural-decisions.md` esté aprobado para las decisiones relevantes a esa fase.**

## PHASE 0 — ARCHITECTURE *(esta es la fase que este paquete de `/docs` completa)*

**Objetivo:** definir y aprobar product architecture, technical architecture, agent architecture, orchestrator, data model, UX y MVP.

**No coding.**

**Entregables:** todo `/docs` (01-product a 06-development) + aprobación explícita de `02-architecture/architectural-decisions.md`.

**Gate de salida:** checklist de `architectural-decisions.md` §"Checklist de aprobación" completo.

---

## PHASE 1 — FOUNDATION

**Crear:**
- Proyecto Next.js + TypeScript
- Estructura de carpetas (ver `repository-structure.md`)
- Repositorio GitHub
- Proyecto Vercel
- Proyecto Convex
- Configuración de entorno (`.env.example`, sin secretos reales)
- Sistema base de UI (design tokens mínimos, sin sistema de diseño completo)

**Depende de:** decisiones #2 (síncrono/asíncrono), #13 (deployment) aprobadas.

---

## PHASE 2 — WORKSPACE / PROJECTS

**Crear:**
- Users
- Organizations
- Workspaces
- Projects
- Permissions (RBAC básico)

**Depende de:** decisiones #5 (permisos), #6 (multi-tenant), #13 (auth) aprobadas. Ver `02-architecture/security-architecture.md` §3 (autenticación aún pendiente de elegir proveedor).

---

## PHASE 3 — CHALLENGE

**Crear:**
- Challenge Builder (UI + formulario de 10 campos)
- Modelo de datos de Challenge
- Validación de Challenge
- Contexto de Challenge para el Orchestrator

**Depende de:** `04-data/data-model.md` §3 (Challenge), `05-ux/screen-inventory.md` (pantalla Challenge).

---

## PHASE 4 — DATA HUB

**Crear:**
- Sources / Files / Datasets
- Storage (Convex Storage)
- Pipeline de ingesta (ver `04-data/file-processing.md`)
- Procesamiento básico (parsing inicial)

**Depende de:** decisión #9 (file processing) aprobada.

---

## PHASE 5 — ORCHESTRATOR

**Crear:**
- Agent Registry
- Tool Registry
- Workflow (planificación, `WorkflowRun`)
- Task
- Agent Run
- Execution State
- Context (contrato Orchestrator ↔ Agent, ver `03-agents/orchestrator-agent-protocol.md`)
- Validation
- Error handling

**Depende de:** decisiones #1 (orchestrator), #7 (AI abstraction), #8 (tools) aprobadas. Esta es la fase de mayor riesgo técnico — no debe iniciar sin `02-architecture/orchestrator-architecture.md` totalmente revisado.

---

## PHASE 6 — AGENTS

**Implementar inicialmente (agentes MVP, ver `mvp-scope.md`):**
- Data Agent
- Shopper Agent
- Research Agent
- Insight Agent
- Opportunity Agent

**Depende de:** Fase 5 completa (el Orchestrator debe existir antes que los agentes que orquesta).

---

## PHASE 7 — INTELLIGENCE

**Crear:**
- Evidence
- Insights
- Hypotheses
- Intelligence Board (UI)
- Trazabilidad (Evidence → Insight → Opportunity → Recommendation)

**Depende de:** decisión #10 (evidence model) aprobada, `04-data/intelligence-model.md` implementado tal cual está documentado.

---

## PHASE 8 — OPPORTUNITIES

**Crear:**
- Opportunity Engine
- Priorización (scoring versionado)
- Recommendations

**Depende de:** Fase 7 completa.

---

## PHASE 9 — OUTPUT

**Crear:**
- Executive summary (Proposal Agent, versión mínima)
- Reportes básicos
- Salidas exportables

**Depende de:** Fase 8 completa; `03-agents/proposal-agent.md` §"Nota de alcance MVP".

---

## PHASE 10 — QA / SECURITY / DEPLOYMENT

**Validar:**
- Authentication
- Authorization
- Data isolation
- Error handling
- Performance
- AI reliability
- Security
- Deployment

**Depende de:** checklist completo de `02-architecture/security-architecture.md` §8 y de `testing-and-evaluation.md`.

---

## Diagrama de dependencias entre fases

```text
PHASE 0 (Architecture)
   ↓
PHASE 1 (Foundation)
   ↓
PHASE 2 (Workspace/Projects) ──────┐
   ↓                               │
PHASE 3 (Challenge)                │
   ↓                               │
PHASE 4 (Data Hub) ────┐           │
   ↓                   │           │
PHASE 5 (Orchestrator) ←┘          │
   ↓                               │
PHASE 6 (Agents)                   │
   ↓                               │
PHASE 7 (Intelligence)             │
   ↓                               │
PHASE 8 (Opportunities)            │
   ↓                               │
PHASE 9 (Output)                   │
   ↓                               │
PHASE 10 (QA/Security/Deployment) ←┘
```

Nota: Phase 2 (permisos/multi-tenancy) es una dependencia transversal que Phase 10 vuelve a validar explícitamente, no solo una fase que "ya pasó".
