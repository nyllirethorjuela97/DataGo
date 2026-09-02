# Screen Inventory

> Traduce los módulos de `01-product/product-architecture.md` y el mapa de navegación (`navigation-map.md`) en un inventario de pantallas con su propósito y componentes clave. No es un diseño visual (eso pertenece a una fase de diseño de UI posterior) — es el contrato funcional de cada pantalla.

## 1. Global

| Pantalla | Propósito | Componentes clave |
|---|---|---|
| **Home** | Punto de entrada tras login; acceso a Workspaces recientes | Lista de workspaces, acceso rápido a proyectos recientes |
| **Workspaces** | Listado y gestión de workspaces del usuario | Lista de workspaces, crear nuevo workspace |
| **Agent Hub** | Catálogo de agentes disponibles en la plataforma | Lista de `AgentDefinition` (nombre, purpose, versión, estado, costo estimado) — ver `03-agents/orchestrator-agent-protocol.md` §4 |
| **Knowledge** | Vista de Client/Organization Memory disponible | Lista de `Knowledge` por scope, filtro por cliente/organización |
| **Admin** *(post-MVP)* | Usuarios, roles, permisos, billing, auditoría | Fuera de alcance MVP (§9.14) |

## 2. Dentro de un Project

| Pantalla | Propósito | Componentes clave | Estado de datos que muestra |
|---|---|---|---|
| **Overview** | Resumen del estado del proyecto | Estado del Challenge, último Workflow Run, conteo de Insights/Opportunities | Agregado de todas las entidades del proyecto |
| **Challenge** | Challenge Builder: crear/editar el reto | Formulario con los 10 campos de §9.3 (pregunta, objetivo, contexto, categoría, mercado, shopper, restricciones, horizonte, info disponible, resultado esperado) | `Challenge` |
| **Data** | Data Hub: gestión de fuentes | Lista de `DataSource`/`File`/`Dataset` con `processedStatus`, subir nuevo archivo | `DataSource`, `File`, `Dataset` |
| **Agent Runs** | Progreso y trazabilidad de la orquestación | Vista del `WorkflowRun` activo/histórico, estado de cada `Task`/`AgentRun`, motivo de agentes omitidos | `WorkflowRun`, `Task`, `AgentRun` |
| **Intelligence Board** | Visualización de evidencia, insights, hipótesis | Lista/tablero de `Evidence`/`Insight`/`Hypothesis` con indicador visual de `epistemicType` y `confidence`; trazabilidad hacia la fuente (§8, §24) | `Evidence`, `Insight`, `Hypothesis` |
| **Opportunities** | Oportunidades identificadas y priorizadas | Lista de `Opportunity` ordenada por `priority`, con `potentialImpact`/`feasibility` visibles, filtro por categoría (§9.9) | `Opportunity` |
| **Recommendations** | Recomendaciones accionables | Lista de `Recommendation` vinculada a su `Opportunity`, con flujo de revisión humana (`reviewStatus`) | `Recommendation` |
| **Strategy** *(post-MVP)* | Dirección estratégica | Fuera de alcance MVP (§9.10) | `Strategy` |
| **Deliverables** | Generación y consulta de entregables | Botón de generación de executive summary (MVP); post-MVP: report/presentation/proposal/action plan | `Deliverable` |

## 3. Requisitos transversales de UI (no negociables)

1. **Indicador epistémico visible**: cualquier tarjeta/fila que muestre un Insight, Hypothesis, Opportunity o Recommendation debe mostrar su `epistemicType` de forma visualmente distinguible (no solo texto pequeño) — deriva directamente de §50.
2. **Trazabilidad a un clic**: desde cualquier Insight/Opportunity/Recommendation debe poder navegarse hacia su Evidence de origen sin salir del contexto (panel lateral o expansión inline), consistente con `04-data/intelligence-model.md` §8.
3. **Estado de ejecución en tiempo real**: la pantalla de Agent Runs debe reflejar el estado de cada Task de forma reactiva (sin refresh manual) — requisito técnico ya cubierto por la elección de Convex (`02-architecture/technical-architecture.md` §4).
4. **Human-in-the-loop nunca oculto**: cualquier elemento en `needs_review`/`pending_review` debe ser visualmente prioritario, no un estado más entre otros.

## 4. Fuera de alcance de este documento

- Sistema de diseño (tipografía, color, espaciado) — pertenece a una fase de diseño visual posterior a la aprobación de esta arquitectura.
- Diseño responsive/mobile específico.
