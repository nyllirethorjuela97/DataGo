# 03 — Agents

Arquitectura y especificación individual de cada agente de DataGo.

## Contenido

| Documento | Descripción |
|---|---|
| [`agent-principles.md`](./agent-principles.md) | Topología conceptual del grafo de agentes, propiedades obligatorias, contrato común, clasificación por rol |
| [`orchestrator-agent-protocol.md`](./orchestrator-agent-protocol.md) | Protocolo de comunicación Orchestrator ↔ Agent, formato estándar de output, extensibilidad del Agent Registry |

### Agentes individuales

| Agente | Rol | Prioridad |
|---|---|---|
| [`data-agent.md`](./data-agent.md) | Limpieza, estructuración y análisis descriptivo de datos | MVP |
| [`shopper-agent.md`](./shopper-agent.md) | Comportamiento, journey y necesidades del shopper | MVP |
| [`research-agent.md`](./research-agent.md) | Investigación externa de mercado/categoría | MVP |
| [`insight-agent.md`](./insight-agent.md) | Síntesis de evidencia en inteligencia (insights/hipótesis) | MVP |
| [`opportunity-agent.md`](./opportunity-agent.md) | Traducción de insights en oportunidades priorizadas | MVP |
| [`trade-agent.md`](./trade-agent.md) | Implicaciones comerciales y de trade | Post-MVP |
| [`strategy-agent.md`](./strategy-agent.md) | Dirección estratégica a partir de oportunidades | Post-MVP |
| [`solution-agent.md`](./solution-agent.md) | Soluciones concretas a partir de estrategia | Post-MVP |
| [`proposal-agent.md`](./proposal-agent.md) | Entregables y propuestas (versión mínima MVP: executive summary) | MVP (mínimo) / Post-MVP (completo) |

## Regla de lectura obligatoria

Ningún agente debe implementarse leyendo solo su propio documento. Siempre debe leerse en conjunto con `agent-principles.md` (reglas transversales) y `02-architecture/orchestrator-architecture.md` (cómo el Orchestrator lo invoca).
