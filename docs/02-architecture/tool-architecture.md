# Tool Architecture

> Fuente: Master Spec §37, §56 (decisión #8).

## 1. Principio

Los agentes no acceden a capacidades del sistema (leer archivos, buscar, calcular, generar contenido) de forma libre. Todo acceso pasa por una **Tool** registrada, controlada y con permisos explícitos.

## 2. Contrato de una Tool

```text
Tool
 ├── name
 ├── description
 ├── inputs (schema)
 ├── outputs (schema)
 ├── permissions (qué agentes pueden invocarla)
 └── validation (reglas antes/después de ejecutar)
```

Este contrato es simétrico al de `Agent` (§11 del Master Spec) — ambos son entidades versionables, trazables y con permisos, porque una Tool es, en la práctica, tan sensible como el agente que la usa (una Tool de "escritura a base de datos" o "envío de contenido externo" tiene el mismo nivel de riesgo que un agente mal configurado).

## 3. Catálogo inicial de Tools (Master Spec §37)

| Tool | Usada por (agentes candidatos) | Riesgo | Notas |
|---|---|---|---|
| `data_analysis` | Data Agent | Bajo | Opera sobre datasets ya ingeridos y aislados por proyecto |
| `file_parsing` | Data Agent | Medio | Debe tratar contenido de archivos como datos, no instrucciones (ver `security-architecture.md` §6) |
| `search` | Research Agent | Medio | Fuentes externas — debe registrar `source` para cada resultado usado como Evidence |
| `knowledge_retrieval` | Insight Agent, Opportunity Agent, cualquier agente con acceso a memoria | Medio | Debe respetar los niveles de Project/Client/Organization Memory (ver `04-data/project-memory.md`) |
| `calculations` | Data Agent, Trade Agent | Bajo | Determinístico, fácil de testear unitariamente |
| `structured_extraction` | Data Agent, Research Agent | Medio | Extrae campos estructurados de fuentes no estructuradas; salida debe validarse contra schema esperado |
| `content_generation` | Proposal Agent (principalmente) | Medio-Alto | Debe operar solo sobre Opportunities/Recommendations ya validadas, nunca inventar evidencia |

## 4. Registro y versión

Al igual que los Agents (§52 Versioning), las Tools son versionables. Un `AgentRun` registra qué versión de cada Tool utilizó, para que un cambio en una Tool no invalide silenciosamente la trazabilidad de ejecuciones pasadas.

## 5. Validación de permisos en tiempo de ejecución

```text
Agent solicita invocar Tool X
        ↓
¿Tool X está en la lista de tools permitidas del Agent (Agent Hub)?
   ├── No → rechazar, registrar en AuditLogs, marcar AgentRun como failed (validation)
   └── Sí → ¿input cumple schema de la Tool?
              ├── No → rechazar con error de validación
              └── Sí → ejecutar → validar output contra schema de salida → devolver al Agent
```

## 6. Relación con el Orchestrator

El Orchestrator no invoca Tools directamente; son los Agentes quienes lo hacen dentro de su propia ejecución (Agent Run). El Orchestrator solo es responsable de que el Agent tenga, en su contexto de Task, las Tools que le corresponden habilitadas — la invocación específica es decisión del agente en tiempo de ejecución.

## 7. No incluido en el MVP

- Marketplace de Tools de terceros.
- Tools que ejecuten acciones externas irreversibles (p. ej., publicar contenido, enviar emails) — cualquier Tool de este tipo requeriría obligatoriamente un paso de human-in-the-loop antes de ejecutar, y no está priorizada para el MVP.
