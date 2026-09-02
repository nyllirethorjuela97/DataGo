# Navigation Map

> Fuente: Master Spec §38, §39.

## 1. Mapa de navegación global

```text
HOME
│
├── WORKSPACES
│   │
│   └── PROJECTS
│       │
│       ├── Overview
│       ├── Challenge
│       ├── Data
│       ├── Agents
│       ├── Intelligence
│       ├── Opportunities
│       ├── Strategy
│       └── Deliverables
│
├── AGENT HUB
│
├── KNOWLEDGE
│
└── ADMIN
```

## 2. Navegación dentro de un proyecto (§39)

```text
PROJECT
│
├── Overview
├── Challenge
├── Data
├── Agent Runs
├── Intelligence
├── Opportunities
├── Recommendations
├── Strategy
└── Deliverables
```

Nota: el spec lista "Agents" en la navegación global y "Agent Runs" en la navegación de proyecto — son vistas distintas: **Agent Hub** (global) es el catálogo de agentes disponibles en la plataforma; **Agent Runs** (dentro de un proyecto) es el historial de ejecuciones de ese proyecto específico.

## 3. Prioridad de secciones en el MVP

| Sección | Nivel | MVP |
|---|---|---|
| Home / Workspaces / Projects | Global | Sí |
| Overview, Challenge, Data | Proyecto | Sí |
| Agent Runs (progreso de orquestación) | Proyecto | Sí |
| Intelligence, Opportunities, Recommendations | Proyecto | Sí |
| Deliverables (executive summary) | Proyecto | Sí (versión mínima) |
| Strategy | Proyecto | No (post-MVP, §9.10) |
| Agent Hub (catálogo completo, gestión) | Global | Versión mínima — lectura del catálogo, sin marketplace |
| Knowledge | Global | Versión mínima — ligado a Project Memory, sin gestión avanzada |
| Admin | Global | No (post-MVP, §9.14) |

## 4. Principio de navegación

La navegación de proyecto sigue el mismo orden que el pipeline funcional (`02-architecture/functional-architecture.md` §2): Challenge → Data → Agent Runs → Intelligence → Opportunities → Recommendations → (Strategy) → Deliverables. Esto es intencional: la navegación **es** el flujo Challenge Driven Intelligence, no una organización arbitraria de menús. El usuario nunca debería tener que "adivinar" el siguiente paso — la posición en el menú lo indica.
