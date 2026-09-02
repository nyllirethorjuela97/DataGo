# Orchestrator Architecture

> Fuente: Master Spec §21, §22, §23, §56 (decisión #1). El Orchestrator es "el cerebro operativo de DataGo" — este documento detalla su ciclo de vida, contratos y modelo de estado.

## 1. Ciclo de vida (Master Spec §21)

```text
CHALLENGE
   ↓
CHALLENGE ANALYSIS
   ↓
WORKFLOW PLANNING
   ↓
AGENT SELECTION
   ↓
TASK CREATION
   ↓
AGENT EXECUTION
   ↓
VALIDATION
   ↓
RESULT STORAGE
   ↓
NEXT STEP
```

## 2. Componentes internos del Orchestrator

| Componente | Función |
|---|---|
| **Challenge Analyzer** | Convierte el Challenge estructurado en un conjunto de requerimientos de información (qué datos, qué tipo de análisis, qué agentes son candidatos) |
| **Workflow Planner** | Produce un `workflow_plan`: lista ordenada de tareas por agente con dependencias explícitas (grafo dirigido acíclico) |
| **Agent Selector** | Filtra el catálogo del Agent Registry según capacidades requeridas, disponibilidad y permisos del proyecto |
| **Task Dispatcher** | Crea `Task` individuales con su contexto de entrada y las despacha a cada Agent Run |
| **Execution Monitor** | Rastrea el estado de cada Agent Run (`pending`, `running`, `completed`, `failed`, `needs_review`) |
| **Validator** | Aplica reglas de validación de salida por tipo de agente antes de aceptar un resultado (ver §5) |
| **Result Store** | Persiste outputs como Evidence/Insight/Opportunity según el tipo de agente |
| **Next-Step Resolver** | Decide si el workflow continúa, se detiene por falta de datos, o requiere revisión humana |

## 3. Modelo de datos del Orchestrator (conceptual — detalle en `04-data/data-model.md`)

```text
WorkflowRun
 ├── projectId
 ├── challengeId
 ├── plan[]            (lista ordenada de pasos: agentId, dependsOn[])
 ├── status             (planned | running | completed | failed | needs_review)
 ├── currentStep
 └── createdAt / updatedAt

Task
 ├── workflowRunId
 ├── agentId
 ├── input (contexto + referencias a datos/evidencia previa)
 ├── status
 └── dependsOn[]

AgentRun
 ├── taskId
 ├── agentId + agentVersion
 ├── model (proveedor/modelo usado)
 ├── input / output
 ├── status
 ├── validation (pass | fail | needs_review + motivo)
 ├── tokens/usage
 ├── durationMs
 └── errorLog
```

Este modelo satisface directamente el requisito de trazabilidad de Rule 5 (§49) y el punto de Observability (§51).

## 4. Planificación dinámica: por qué no es un pipeline fijo

Master Spec §10 y §40 son explícitos: **no todos los proyectos necesitan todos los agentes**. El Workflow Planner debe:

1. Partir del grafo de dependencias conceptual definido en `03-agents/README.md` (Data/Shopper/Research → Insight → Opportunity → Trade → Strategy → Solution → Proposal).
2. Podar ramas no relevantes según el Challenge (p. ej., si no hay datos de shopper disponibles, el Shopper Agent se omite y esa decisión queda registrada con motivo).
3. Permitir reordenamientos cuando existan dependencias parciales (p. ej., Research Agent puede correr en paralelo con Data Agent).

**Regla dura:** ningún agente "downstream" (Insight, Opportunity, Trade, Strategy, Solution, Proposal) puede ejecutarse sin al menos un output válido de un agente "upstream" que le provea evidencia. Esto se valida en el paso de Workflow Planning, no en tiempo de ejecución del agente.

## 5. Validación de resultados

Antes de que un output de Agent Run se acepte como Evidence/Insight/Opportunity definitivo, el Validator aplica:

- **Validación de esquema**: el output cumple el contrato de salida declarado por el agente (Agent Hub, §9.6).
- **Validación de evidencia (Rule 6, §49)**: todo insight con `business relevance` alta debe referenciar al menos una Evidence con `source`.
- **Validación de tipo epistémico (§50)**: el output debe declarar si es FACT, INTERPRETATION, HYPOTHESIS o RECOMMENDATION; el Validator rechaza outputs que no distingan esto.
- **Validación de confianza**: si `confidence` está bajo el umbral configurado, el resultado se marca `needs_review` en vez de `completed`.

Un resultado que falla validación **no bloquea todo el workflow**: se marca y el Next-Step Resolver decide si continuar con degradación (menos evidencia disponible) o pausar para revisión humana.

## 6. Manejo de errores y reintentos

```text
AgentRun.status = failed
        ↓
¿Error transitorio (timeout, rate limit)?
   ├── Sí → retry automático (máx. N intentos, backoff) → running
   └── No (error de validación / input insuficiente) → needs_review
                                                              ↓
                                                     Human decide:
                                                     retry / skip / abort workflow
```

Todo reintento se registra como un nuevo `AgentRun` vinculado al mismo `Task`, nunca sobrescribe el intento anterior (trazabilidad completa).

## 7. Human-in-the-loop dentro del ciclo del Orchestrator

El Orchestrator es responsable de **pausar** el workflow (no de decidir el resultado de la revisión) cuando el Next-Step Resolver detecta cualquiera de los disparadores definidos en `functional-architecture.md` §5. El estado `needs_review` bloquea el avance del `WorkflowRun` hasta que exista una decisión humana registrada (`approved`, `modified`, `rejected`), la cual se adjunta como parte del historial del `WorkflowRun`, no como una edición silenciosa del output original.

## 8. Ejecución síncrona vs. asíncrona

Ver `technical-architecture.md` §5. Resumen aplicado al Orchestrator: el `WorkflowRun` se planifica de forma síncrona (respuesta rápida al usuario: "tu plan es X pasos"), pero cada `AgentRun` se ejecuta de forma asíncrona con actualización reactiva de estado.

## 9. Qué el Orchestrator explícitamente NO hace

- No genera contenido de negocio directamente (eso es responsabilidad de cada agente).
- No decide permisos de usuario (eso es responsabilidad de la capa de autorización, ver `security-architecture.md`).
- No persiste conocimiento reutilizable de largo plazo (eso es responsabilidad de Knowledge Base / Project Memory, ver `04-data/project-memory.md`).
