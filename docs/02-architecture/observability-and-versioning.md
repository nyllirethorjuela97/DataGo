# Observability & Versioning

> Fuente: Master Spec §51, §52, §56 (decisión #12).

## 1. Observability — qué se registra por cada Agent Run

Master Spec §51 define el conjunto mínimo de campos. Este documento los organiza por propósito:

| Categoría | Campos | Para qué sirve |
|---|---|---|
| Identidad de ejecución | `agent`, `version`, `project`, `task` | Trazabilidad (Rule 5) |
| Contenido | `input`, `output` | Auditoría y debugging; base para evaluación de calidad (§53) |
| Estado | `status`, `errors`, `validation` | Monitoreo operativo, disparo de reintentos |
| Rendimiento | `duration`, `model`, `tokens/usage` | Control de costo, optimización, base para monetización por consumo (§46) |
| Tiempo | `timestamp` | Orden causal, auditoría, reportes |

## 2. Niveles de observabilidad

```text
Agent Run          → detalle fino (input/output/tokens/duración de una sola ejecución)
Workflow Run        → agregado por proyecto (cuántos agentes corrieron, cuánto costó, cuánto tardó)
Project              → salud general (última ejecución, insights generados, oportunidades abiertas)
Organization         → consumo total, útil para billing por análisis (Master Spec §46)
```

## 3. Qué NO es observability en DataGo

- No es un dashboard de negocio para el usuario final (eso es Intelligence Board / Opportunity Engine).
- No reemplaza el Evidence model — un log de ejecución no es una fuente de evidencia citable dentro de un Insight.

## 4. Versioning — qué elementos deben versionarse (Master Spec §52)

| Elemento | Por qué debe versionarse |
|---|---|
| Agents | Un cambio en el comportamiento de un agente puede alterar resultados; ejecuciones pasadas deben quedar ligadas a la versión que efectivamente corrió |
| Prompts | Los prompts son, en la práctica, código de comportamiento — deben tratarse con el mismo rigor |
| Workflows | El plan de orquestación puede evolucionar; se debe poder auditar qué plan se usó en cada `WorkflowRun` |
| Tools | Ver `tool-architecture.md` §4 |
| Scoring logic | Cualquier lógica que priorice oportunidades o puntúe confianza debe ser auditable en el tiempo |
| Schemas | Cambios de esquema de datos deben documentarse y, si son breaking, migrarse explícitamente |
| Product specifications | El propio Master Spec es versionado (ya lo indica su encabezado `Version: 1.0`) |

## 5. Principio de versionado

> Ningún cambio a un Agent, Prompt, Tool o Workflow debe sobrescribir silenciosamente el comportamiento usado en ejecuciones pasadas. Cada `AgentRun`/`WorkflowRun` referencia la versión exacta usada, de forma que un resultado histórico siga siendo explicable incluso después de actualizar el agente.

## 6. Implicación de diseño de datos

Esto implica que `agents`, `tools`, `workflow_definitions` y `prompts` (si se modelan como entidad propia) deben soportar múltiples versiones activas simultáneamente en el esquema, no solo un campo `version` que se sobrescribe. Ver `04-data/data-model.md`.
