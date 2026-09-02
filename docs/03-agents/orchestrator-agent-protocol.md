# Orchestrator ↔ Agent Protocol

> Complementa `agent-principles.md` y `02-architecture/orchestrator-architecture.md`. Define el contrato de comunicación entre el Orchestrator y cualquier agente, para que agregar un agente nuevo al Agent Registry no requiera cambios ad-hoc en el Orchestrator.

## 1. Ciclo de un Task individual

```text
Orchestrator crea Task
   input = {
     challengeContext,
     upstreamOutputs: [...],   // Evidence/Insight relevantes ya producidos
     projectMemoryRefs: [...], // referencias, no copia completa
     toolsAvailable: [...]
   }
        ↓
Agent recibe Task → ejecuta con sus Tools permitidas
        ↓
Agent devuelve output estandarizado:
   {
     result: {...},            // específico del tipo de agente
     epistemicType: FACT | INTERPRETATION | HYPOTHESIS | RECOMMENDATION,
     confidence: number,
     evidenceRefs: [...],
     status: completed | needs_review | failed
   }
        ↓
Orchestrator valida (ver orchestrator-architecture.md §5)
        ↓
Result Store persiste como Evidence/Insight/Opportunity/Recommendation según el tipo de agente
```

## 2. Por qué el output es estandarizado aunque cada agente produzca contenido distinto

El `result` interno varía por agente (un Data Agent produce `structured data`, un Opportunity Agent produce `Opportunity`), pero el **sobre** (`epistemicType`, `confidence`, `evidenceRefs`, `status`) es idéntico para todos. Esto permite que:

- El Validator del Orchestrator aplique las mismas reglas de validación epistémica (§50) a cualquier agente sin lógica especial por tipo.
- El Observability layer (`02-architecture/observability-and-versioning.md`) registre métricas uniformes independientemente del agente.
- Agregar un agente nuevo (p. ej., un futuro "Competitive Intelligence Agent") no requiera tocar el Orchestrator, solo registrar el nuevo agente en el Agent Registry con su contrato.

## 3. Reglas de comunicación

- Un agente **nunca** invoca a otro agente directamente. Toda dependencia entre agentes pasa por el Orchestrator (evita acoplamiento y preserva trazabilidad centralizada).
- Un agente **nunca** escribe directamente en las tablas finales de negocio (Insights, Opportunities, etc.) — entrega su output al Orchestrator, que lo valida y lo persiste vía el Result Store.
- Un agente que necesita más contexto del que recibió (p. ej., Insight Agent necesita más Evidence) lo declara en su output (`status: needs_review`, `reason: insufficient_evidence`) en vez de intentar obtenerlo por su cuenta.

## 4. Extensibilidad del Agent Registry

Cada entrada del Agent Registry (Agent Hub, §9.6) debe declarar:

```text
AgentDefinition
 ├── id / name / description
 ├── purpose
 ├── inputSchema
 ├── outputSchema (debe incluir el "sobre" estándar de §1)
 ├── toolsAllowed[]
 ├── dependsOn[] (qué tipos de agentes upstream requiere como mínimo)
 ├── permissions
 ├── version
 ├── status (active | deprecated | experimental)
 └── estimatedCost (para presupuesto de análisis, §46)
```

Esto es lo que permite a `02-architecture/orchestrator-architecture.md` §4 podar dinámicamente el grafo de agentes según el Challenge, sin lógica hardcodeada por agente dentro del Workflow Planner.
