# Technical Architecture

> Fuente: Master Spec §34, §35, §48. Este documento traduce la arquitectura conceptual del spec en capas técnicas concretas. No incluye implementación — es la referencia que Fase 1 (Foundation) del roadmap debe seguir.

## 1. Diagrama de capas

```text
                              USER
                                │
                                ▼
                    ┌───────────────────────┐
                    │  FRONTEND (Vercel)     │
                    │  Next.js / React / TS  │
                    └───────────┬───────────┘
                                │  (Convex client, typed queries/mutations)
                                ▼
                    ┌───────────────────────┐
                    │  APPLICATION LAYER     │
                    │  Convex functions:     │
                    │  queries / mutations / │
                    │  actions               │
                    └───────────┬───────────┘
                                ▼
                    ┌───────────────────────┐
                    │  AGENT ORCHESTRATOR    │
                    │  (Convex actions +     │
                    │   workflow state)      │
                    └───────────┬───────────┘
                                ▼
                    ┌───────────────────────┐
                    │      AI LAYER          │
                    │  AI Abstraction Service│
                    └─────┬───────┬─────────┘
                          ▼       ▼          ▼
                       DATA     TOOLS    WORKFLOWS
                          │       │          │
                          └───────┼──────────┘
                                  ▼
                    ┌───────────────────────┐
                    │        CONVEX          │
                    │  DB + Storage + Cron   │
                    └─────┬───────┬─────────┘
                          ▼       ▼          ▼
                      PROJECTS   DATA    KNOWLEDGE
                                  │
                                  ▼
                               OUTPUTS
```

Este diagrama es una elaboración directa del Master Spec §34; no introduce componentes nuevos, solo los nombra técnicamente.

## 2. Responsabilidad de cada capa

| Capa | Responsabilidad | No debe hacer |
|---|---|---|
| **Frontend** | Renderizar UI, capturar input del usuario, suscribirse a datos reactivos de Convex, mostrar estado de ejecución en tiempo real | Contener lógica de autorización, llamar directamente a proveedores de IA, almacenar secretos |
| **Application Layer** | Exponer queries/mutations tipadas, validar input, aplicar reglas de autorización server-side | Exponer operaciones sin validar permisos (Rule 3/4 del Master Spec) |
| **Agent Orchestrator** | Planificar workflow, seleccionar agentes, gestionar dependencias/estado/errores/reintentos | Ejecutar lógica de negocio específica de un agente (eso vive en cada agente) |
| **AI Layer / AI Abstraction Service** | Traducir llamadas de agentes a proveedores de modelos concretos, normalizar respuestas | Acoplar el resto del sistema a un proveedor específico (ver `ai-abstraction-layer.md`) |
| **Convex (DB + Storage)** | Persistencia, reactividad, funciones serverless, almacenamiento de archivos | Contener lógica de presentación |

## 3. Stack tecnológico propuesto (Master Spec §35)

| Área | Tecnología | Estado |
|---|---|---|
| Frontend | Next.js + React + TypeScript | Propuesto |
| Backend | Convex (functions: queries, mutations, actions) | Propuesto |
| Base de datos | Convex Database | Propuesto |
| Storage de archivos | Convex Storage (o capa compatible aprobada) | Propuesto — evaluar límites de tamaño de archivo en Fase 4 |
| Deployment | Vercel | Propuesto |
| Repositorio | GitHub | Definido |
| IA | Capa de abstracción sobre Claude u otros modelos aprobados | Propuesto — ver §5 |
| Auth | Por definir (candidatos: Clerk, Convex Auth, Auth.js) — **decisión pendiente**, ver `architectural-decisions.md` #5 | Pendiente |

## 4. Por qué Convex (racional, no solo elección)

Convex se alinea con tres necesidades explícitas del spec:
1. **Reactividad**: la Intelligence Board y el estado de Agent Runs deben reflejarse en vivo (§8, §22) sin polling manual.
2. **Functions server-side por defecto**: reduce el riesgo de exponer lógica de autorización en el frontend (Rule 4, §32).
3. **Actions de larga duración**: adecuadas para orquestar llamadas a modelos de IA que pueden tardar segundos/minutos, con posibilidad de invocar servicios externos.

Riesgo a vigilar: acoplamiento fuerte a Convex como backend único. Se mitiga manteniendo la lógica de negocio de agentes y el AI Abstraction Layer independientes del runtime de Convex donde sea razonable (funciones puras, testeables fuera de Convex).

## 5. Ejecución síncrona vs. asíncrona (Decisión arquitectónica #2 del Master Spec §56)

**Recomendación:**
- Challenge Builder, CRUD de Workspace/Project: **síncrono** (mutations estándar de Convex).
- Ejecución de agentes (Agent Run): **asíncrona**, mediante Convex actions de larga duración + tabla de estado (`agent_runs`) que el frontend suscribe reactivamente. El usuario ve progreso en tiempo real sin bloquear la UI.
- Orquestación multi-agente: modelada como una **máquina de estados persistida** (`workflow_runs` con `current_step`, `status`, `plan`), no como una cadena de promesas en memoria — esto permite reanudar tras fallos y auditar cada paso (Rule 5, §51 Observability).

## 6. Consideraciones de escalabilidad (para revisión, no bloqueante del MVP)

- Aislamiento de datos por `organizationId`/`workspaceId` debe estar presente en **todas** las tablas desde el día uno (más barato que migrar después) — ver `multi-tenancy-architecture.md`.
- El procesamiento pesado de archivos (parsing de datasets grandes) debe desacoplarse de la request principal mediante actions asíncronas, para no bloquear el hilo de orquestación.
- Rate limiting hacia el proveedor de IA debe vivir en el AI Abstraction Layer, no en cada agente individualmente.

## 7. Qué queda explícitamente fuera de esta capa técnica en el MVP

- Infraestructura multi-región.
- Cache distribuido dedicado (Convex ya provee reactividad/cache razonable para el volumen esperado del MVP).
- Colas de mensajería externas (SQS/Kafka) — la orquestación se apoya en el modelo de actions + estado persistido de Convex mientras el volumen lo permita.
