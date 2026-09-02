# MVP Scope

> Fuente: Master Spec §41–45. Define con precisión qué entra y qué no entra en el primer corte funcional de DataGo.

## 1. Incluido en el MVP (§41)

```text
Workspace
   ↓
Project
   ↓
Challenge
   ↓
Data
   ↓
Agent Orchestration
   ↓
Intelligence
   ↓
Opportunities
   ↓
Output
```

Este es el mismo journey documentado en `05-ux/user-journeys.md` §2 — **debe funcionar de punta a punta antes de invertir en cualquier feature adicional**.

## 2. Agentes del MVP (§42)

### Required (deben existir para que el journey funcione)
- Data Agent
- Shopper Agent
- Research Agent
- Insight Agent
- Opportunity Agent

### Later (documentados, no bloqueantes)
- Trade Agent
- Strategy Agent
- Solution Agent
- Proposal Agent *(excepción: su capacidad mínima de executive summary sí es MVP, ver §4 de este documento)*

**Restricción de arquitectura:** el Agent Registry y el modelo de datos deben soportar agregar estos agentes "later" sin reconstrucción (ver `03-agents/orchestrator-agent-protocol.md` §4) — ya están documentados individualmente en `03-agents/` precisamente por esto.

## 3. Journey del MVP (§43)

```text
LOGIN → CREATE WORKSPACE → CREATE PROJECT → DEFINE CHALLENGE
   → ADD DATA → RUN ANALYSIS → ORCHESTRATOR → AGENTS
   → INTELLIGENCE BOARD → OPPORTUNITIES → OUTPUT
```

## 4. Output mínimo del MVP (§44)

El MVP debe entregar, como mínimo:

- Resumen del challenge
- Principales evidencias
- Insights
- Hipótesis
- Oportunidades
- Recomendaciones
- Resumen ejecutivo (executive summary)

## 5. Explícitamente fuera del MVP (§45)

- Enterprise billing
- White label
- Marketplace de agentes/tools
- Aplicación móvil
- CRM avanzado
- Contabilidad
- Automatización compleja
- Colaboración avanzada (multi-usuario en tiempo real dentro de un proyecto, comentarios, etc.)
- Integraciones externas complejas (APIs de terceros más allá de lo necesario para IA)
- Generación avanzada de propuestas (reportes/presentaciones completas — solo executive summary en MVP)
- Strategy Workspace completo
- Administration (gestión de usuarios/roles/billing vía UI)

Estas funcionalidades están contempladas en la arquitectura futura (`02-architecture/`, `03-agents/`) pero no se construyen en el MVP.

## 6. Criterio de "listo" del MVP

El MVP se considera funcionalmente completo cuando un usuario puede:

1. Crear un workspace y un proyecto.
2. Definir un challenge completo (10 campos de §9.3).
3. Adjuntar al menos una fuente de datos.
4. Disparar la orquestación y ver el progreso de los agentes en tiempo real.
5. Ver al menos un Insight con su Evidence trazable.
6. Ver al menos una Opportunity con Recommendation asociada.
7. Generar y exportar un executive summary basado exclusivamente en contenido trazable del proyecto (sin invención de datos, Rule 9).

Este criterio es la base de los tests End-to-End de `testing-and-evaluation.md`.
