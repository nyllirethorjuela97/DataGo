# 01 — Product Architecture

## 1. Producto

**Nombre:** DataGo
**Descriptor:** Shopper Intelligence & Agentic Platform
**Tipo:** Plataforma de inteligencia comercial impulsada por agentes de IA

## 2. Visión

DataGo transforma un **reto de negocio** en una secuencia completa de:

```text
Inteligencia → Oportunidad → Estrategia → Acción
```

combinando datos, investigación, conocimiento, IA, agentes especializados, workflows, memoria, análisis y generación de entregables, para llevar a los equipos de una pregunta de negocio a una respuesta accionable.

## 3. Problema que resuelve

Las organizaciones tienen información dispersa (archivos, investigaciones, bases de datos, presentaciones, conocimiento interno, datos de shopper/mercado) pero **disponer de información no es disponer de inteligencia**. Hoy ese salto requiere múltiples herramientas, múltiples analistas, procesos manuales y construcción manual de recomendaciones y presentaciones. DataGo integra ese proceso en una experiencia única.

## 4. Usuarios objetivo

Perfiles iniciales: shopper intelligence, consumer insights, trade marketing, estrategia comercial, marketing, investigación de mercados, innovación, estrategia, desarrollo de propuestas, consultoría.

La arquitectura de producto debe soportar la evolución:

```text
Individual → Team → Organization → Enterprise
```

Esto implica que, desde el día uno, entidades como *Users*, *Workspaces*, *Organizations* y *Projects* deben modelarse de forma jerárquica y multi-tenant (ver `04-data/data-model.md` y `02-architecture/multi-tenancy-architecture.md`), aunque el MVP solo exponga el nivel Individual/Team.

## 5. Propuesta de valor

DataGo convierte **un reto de negocio** en **inteligencia accionable y oportunidades de negocio**.

> "No solo te muestra información. Entiende tu reto, investiga, analiza, encuentra patrones, construye inteligencia y te ayuda a convertirla en oportunidades y acción."

## 6. Diferenciación

DataGo **no** es un dashboard, una herramienta BI, un chatbot, un buscador, un repositorio documental, un generador de contenido, un sistema de prompts, ni una colección de agentes independientes.

DataGo **es** una **Agentic Intelligence Platform**: la diferencia está en el flujo completo y orquestado, no en un módulo aislado.

```text
Challenge → Data → Research → Agent Orchestration → Analysis → Evidence
   → Insight → Hypothesis → Opportunity → Recommendation → Strategy
   → Solution → Deliverable
```

## 7. Principio central: Challenge Driven Intelligence

El usuario no empieza pidiendo un reporte; empieza con un reto de negocio (ej. *"Quiero entender cómo crecer en esta categoría"*). DataGo interpreta el reto y determina qué necesita para responderlo — datos, investigación y agentes — antes de producir cualquier salida.

## 8. Modelo funcional conceptual

```text
USER → CHALLENGE → UNDERSTANDING → DATA SOURCES → AGENT SELECTION
   → ORCHESTRATION → ANALYSIS → EVIDENCE → INTELLIGENCE
   → OPPORTUNITIES → RECOMMENDATIONS → ACTION → DELIVERABLES
```

Ver el detalle operativo (con estados y validaciones) en `02-architecture/functional-architecture.md`.

## 9. Experiencia de usuario completa

```text
LOGIN → WORKSPACE → PROJECT → NEW CHALLENGE → DATA SOURCES
   → ORCHESTRATOR → AGENTS → ANALYSIS → INTELLIGENCE BOARD
   → OPPORTUNITIES → RECOMMENDATIONS → ACTION → DELIVERABLES
```

Ver navegación detallada en `05-ux/navigation-map.md`.

## 10. Módulos de la plataforma

| # | Módulo | Rol | Prioridad |
|---|---|---|---|
| 9.1 | **Workspace** | Contenedor principal: proyectos, clientes, información, agentes, conocimiento, resultados | MVP |
| 9.2 | **Projects** | Unidad de iniciativa de negocio: challenge, fuentes, datasets, agent runs, insights, oportunidades, recomendaciones, estrategias, entregables, memoria | MVP |
| 9.3 | **Challenge Builder** | Convierte una necesidad de negocio en un reto estructurado (pregunta, objetivo, contexto, categoría, mercado, shopper, restricciones, horizonte, info disponible, resultado esperado) | MVP |
| 9.4 | **Data Hub** | Centraliza fuentes del proyecto: archivos, datasets, documentos, investigaciones, bases de datos, fuentes externas (futuras vía API) | MVP |
| 9.5 | **Knowledge Base** | Conocimiento reutilizable: cliente, organizacional, aprendizajes históricos, frameworks, metodologías, investigaciones previas | Post-MVP (fundacional, extensible) |
| 9.6 | **Agent Hub** | Catálogo de agentes: nombre, descripción, capacidades, herramientas, permisos, versión, estado, costo estimado, inputs/outputs | MVP |
| 9.7 | **Agent Orchestrator** | Cerebro operativo: interpreta el challenge, planifica, selecciona agentes, ejecuta, valida, registra y decide siguientes pasos | MVP (crítico) |
| 9.8 | **Intelligence Board** | Visualiza evidencia, insights, hipótesis, patrones, señales y conclusiones con trazabilidad al origen | MVP |
| 9.9 | **Opportunity Engine** | Convierte inteligencia en oportunidades comerciales, de shopper, canal, portafolio, comunicación y experiencia | MVP |
| 9.10 | **Strategy Workspace** | Convierte oportunidades en estrategia | Futuro (no MVP) |
| 9.11 | **Proposal Builder** | Convierte soluciones en propuestas | Futuro (no MVP) |
| 9.12 | **Deliverables** | Entregables: executive summary, report, strategy document, presentation, proposal, action plan | MVP (versión mínima: executive summary) |
| 9.13 | **Project Memory** | Contexto, decisiones, aprendizajes, resultados, insights, oportunidades y recomendaciones del proyecto | MVP (versión inicial) |
| 9.14 | **Administration** | Usuarios, organizaciones, roles, permisos, billing, configuración, uso, auditoría | Futuro (no MVP) |

## 11. Monetización (referencia)

DataGo debe prepararse arquitectónicamente (no implementarse en el MVP) para los modelos: Free/Trial, Professional, Team, Enterprise, Premium Agents, Analysis Consumption, Private Workspaces y White Label. Ver `06-development/development-rules.md` §Monetization.

## 12. Visión de largo plazo

```text
AI-powered research platform → AI-powered intelligence platform
   → AI operating system for commercial decision-making
```

Toda decisión de arquitectura de producto debe evaluarse contra esta trayectoria sin comprometer el enfoque del MVP (ver `06-development/roadmap.md`).
