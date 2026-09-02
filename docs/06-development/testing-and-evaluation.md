# Testing & Evaluation

> Fuente: Master Spec §53. Cubre testing de software estándar y evaluación específica de IA — ambos obligatorios, con distinto propósito.

## 1. Unit Tests

**Para:** funciones críticas y determinísticas.

Candidatos prioritarios:
- Lógica de scoring/priorización del Opportunity Agent (`03-agents/opportunity-agent.md`).
- Validadores del Orchestrator (esquema, evidencia mínima, tipo epistémico — `02-architecture/orchestrator-architecture.md` §5).
- Funciones de la Tool `calculations`.
- Reglas de aislamiento multi-tenant a nivel de query (helpers de resolución de `organizationId`/`workspaceId`).

## 2. Integration Tests

**Para:**
- **Convex**: queries/mutations con datos reales de prueba, incluyendo casos de aislamiento (usuario de organización A no puede leer datos de organización B).
- **Workflows**: un `WorkflowRun` completo con agentes simulados (mocks) que verifique transiciones de estado correctas (`planned → running → completed/failed/needs_review`).
- **Agents**: cada agente probado con inputs controlados y outputs esperados dentro del "sobre" estándar (`epistemicType`, `confidence`, `evidenceRefs`, `status` — ver `03-agents/orchestrator-agent-protocol.md` §1).
- **Data processing**: pipeline de `file-processing.md` con archivos de distintos tipos/tamaños, incluyendo casos de fallo esperado.

## 3. End-to-End Tests

**Para el journey completo del MVP:**

```text
Create Project
→ Challenge
→ Data
→ Run
→ Intelligence
→ Opportunity
→ Output
```

Este E2E debe verificar, además de que "algo se genera", que se cumple el criterio de "listo" del MVP (`mvp-scope.md` §6) — en particular que el Output final es trazable a Evidence real, no contenido genérico.

## 4. AI Evaluation

**Objetivo:** evaluar la calidad del comportamiento de los agentes, no solo si el código corre sin errores.

| Dimensión | Qué mide | Cómo se aplica |
|---|---|---|
| **Factuality** | ¿El output afirma solo lo que la evidencia soporta? | Comparar outputs de Research/Data/Insight Agent contra un set de evidencia de referencia (golden set) |
| **Evidence grounding** | ¿Todo Insight/Opportunity referencia Evidence real y relevante? | Validación automática: ningún Insight sin `evidence[]` no vacío pasa evaluación |
| **Consistency** | ¿El mismo input produce outputs coherentes entre ejecuciones? | Ejecutar el mismo Challenge/dataset varias veces y comparar variabilidad de conclusiones (no de redacción) |
| **Usefulness** | ¿Las oportunidades/recomendaciones son accionables y relevantes al Challenge? | Revisión humana estructurada sobre un set de proyectos de referencia |
| **Hallucination rate** | ¿Con qué frecuencia el sistema afirma algo no verificable? | Métrica agregada a partir de auditorías de Evidence grounding; debe reportarse por agente y por versión (ligado a `02-architecture/observability-and-versioning.md` §4) |

## 5. Relación entre evaluación de IA y versionado

Toda evaluación de IA debe registrarse asociada a la **versión específica** del agente/prompt evaluado (nunca "el Insight Agent" en abstracto, sino "Insight Agent v1.3"). Esto permite comparar mejoras/regresiones entre versiones — sin esto, Rule 9 ("la IA no debe inventar datos") no es verificable en el tiempo, solo una aspiración.

## 6. Gate de calidad antes de Fase 10 (QA/Security/Deployment)

- [ ] Unit tests de validadores del Orchestrator pasando.
- [ ] Integration tests de aislamiento multi-tenant pasando (sin excepciones).
- [ ] E2E del journey MVP completo pasando contra un Challenge de referencia real.
- [ ] Evaluación de AI Evaluation ejecutada al menos una vez por cada agente MVP (Data, Shopper, Research, Insight, Opportunity) con hallucination rate documentado como baseline.
