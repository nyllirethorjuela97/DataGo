# User Journeys

> Fuente: Master Spec §8, §40, §43.

## 1. Journey completo de experiencia (visión de producto, §8)

```text
LOGIN → WORKSPACE → PROJECT → NEW CHALLENGE → DATA SOURCES
   → ORCHESTRATOR → AGENTS → ANALYSIS → INTELLIGENCE BOARD
   → OPPORTUNITIES → RECOMMENDATIONS → ACTION → DELIVERABLES
```

## 2. Journey del MVP (§43) — el que debe funcionar de extremo a extremo primero

```text
LOGIN
 ↓
CREATE WORKSPACE
 ↓
CREATE PROJECT
 ↓
DEFINE CHALLENGE
 ↓
ADD DATA
 ↓
RUN ANALYSIS
 ↓
ORCHESTRATOR
 ↓
AGENTS
 ↓
INTELLIGENCE BOARD
 ↓
OPPORTUNITIES
 ↓
OUTPUT
```

Este es el journey que debe demostrar el MVP (Master Spec §61: "El objetivo del MVP es demostrar que DataGo puede convertir un reto real en inteligencia útil y oportunidades accionables"). Cualquier feature que no sirva directamente a completar este journey de punta a punta no debe priorizarse antes de que este camino funcione completo.

## 3. Ejemplo de proyecto real (§40) — cómo se ve el journey "por dentro"

Challenge de ejemplo: *"Quiero entender cómo crecer en una categoría."*

```text
CHALLENGE
   ↓
SOURCE IDENTIFICATION       (Orchestrator determina qué fuentes se necesitan)
   ↓
DATA COLLECTION              (usuario adjunta / sistema identifica fuentes)
   ↓
AGENT PLANNING                (Workflow Planner arma el plan)
   ↓
DATA AGENT                    (estructura los datos disponibles)
   ↓
SHOPPER AGENT                 (analiza comportamiento relevante)
   ↓
RESEARCH AGENT                 (investiga contexto de mercado/categoría)
   ↓
INSIGHT AGENT                   (sintetiza evidencia en insights)
   ↓
EVIDENCE                         (queda registrada y trazable)
   ↓
HYPOTHESES                        (donde la evidencia no alcanza aún)
   ↓
OPPORTUNITIES                      (Opportunity Agent prioriza)
   ↓
RECOMMENDATIONS                     (accionables, vinculadas a oportunidades)
   ↓
OUTPUT                               (executive summary)
```

**Nota crítica de UX**: no todos los agentes se ejecutan siempre. El usuario debe poder ver, en la pantalla de Agent Runs, no solo qué agentes corrieron sino también **por qué** ciertos agentes no se ejecutaron (ver `02-architecture/functional-architecture.md` §4) — esto es parte de la trazabilidad, no un detalle técnico oculto.

## 4. Journey de revisión humana (human-in-the-loop)

```text
Usuario ve una Hypothesis/Opportunity marcada "needs_review"
        ↓
Abre el detalle → ve la recomendación de la IA + evidencia asociada
        ↓
   ┌────┴────┬─────────┐
Approve   Modify    Reject
   ↓          ↓         ↓
Workflow continúa   Workflow continúa   Queda registrado,
con el valor          con el valor        no se genera output
aprobado               modificado          para ese elemento
```

Este journey debe estar disponible desde Intelligence Board y desde Opportunities — dondequiera que exista un elemento en estado `needs_review` o `pending_review`.

## 5. Journey de generación de Deliverable (versión mínima MVP)

```text
Usuario en la sección Deliverables del proyecto
        ↓
Click "Generar executive summary"
        ↓
Proposal Agent compone resumen a partir de:
  Challenge + Evidence principal + Insights + Hypotheses
  + Opportunities + Recommendations existentes
        ↓
Usuario revisa el resultado (siempre editable/exportable,
nunca se envía automáticamente a nadie)
```
