# 02 — Architecture

Arquitectura funcional, técnica, de orquestación y de seguridad de DataGo. Esta carpeta es el corazón del checkpoint de arquitectura exigido por el Master Spec (§57) antes de iniciar desarrollo.

## Contenido

| Documento | Cubre |
|---|---|
| [`functional-architecture.md`](./functional-architecture.md) | Pipeline funcional Challenge → Deliverable con estados, human-in-the-loop, y la separación FACT/INTERPRETATION/HYPOTHESIS/RECOMMENDATION |
| [`technical-architecture.md`](./technical-architecture.md) | Capas técnicas (Frontend/Application/Orchestrator/AI/Convex), stack tecnológico, racional de Convex |
| [`orchestrator-architecture.md`](./orchestrator-architecture.md) | Ciclo de vida del Orchestrator, planificación dinámica de workflow, validación, errores/reintentos |
| [`multi-tenancy-architecture.md`](./multi-tenancy-architecture.md) | Jerarquía User → Team → Organization → Clients → Projects, aislamiento de datos, roles |
| [`security-architecture.md`](./security-architecture.md) | Secretos, autenticación, autorización, riesgos específicos de agentes de IA, auditabilidad |
| [`ai-abstraction-layer.md`](./ai-abstraction-layer.md) | Capa de abstracción sobre proveedores de modelos de IA |
| [`tool-architecture.md`](./tool-architecture.md) | Contrato de Tools, catálogo inicial, permisos y validación |
| [`observability-and-versioning.md`](./observability-and-versioning.md) | Qué se registra por ejecución, niveles de observabilidad, qué elementos deben versionarse |
| [`architectural-decisions.md`](./architectural-decisions.md) | **Registro de las 13 decisiones arquitectónicas pendientes (Master Spec §56)** con recomendación preliminar y checklist de aprobación (§57) |

## Cómo leer esta carpeta

1. Empezar por `functional-architecture.md` (el "qué debe pasar") antes que `technical-architecture.md` (el "con qué se construye").
2. `orchestrator-architecture.md` y `tool-architecture.md` dependen de `03-agents/` para el detalle de cada agente — leer en conjunto.
3. `architectural-decisions.md` es el documento de cierre: ninguna fase de desarrollo (`06-development/roadmap.md`, Fase 1+) debe iniciar sin que sus decisiones relevantes estén aprobadas.
