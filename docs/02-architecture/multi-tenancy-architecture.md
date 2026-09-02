# Multi-Tenancy Architecture

> Fuente: Master Spec §3, §31, §56 (decisión #6). DataGo debe soportar desde el día uno la evolución Individual → Team → Organization → Enterprise, aunque el MVP solo exponga los primeros niveles.

## 1. Jerarquía de tenancy

```text
USER
 ↓
TEAM
 ↓
ORGANIZATION
 ↓
CLIENTS
 ↓
PROJECTS
```

Cada nivel es un límite potencial de aislamiento de datos y de permisos, no solo una agrupación visual.

## 2. Regla de aislamiento fundamental

> Todas las tablas que contienen datos de negocio (Challenges, DataSources, Files, Datasets, AgentRuns, Evidence, Insights, Opportunities, Recommendations, Deliverables, Knowledge, ProjectMemory) deben portar `organizationId` y `workspaceId` como campos indexados desde el esquema inicial — incluso si el MVP solo opera con una organización implícita por usuario.

Justificación: introducir aislamiento multi-tenant después del hecho requiere migraciones de datos y reescritura de queries; hacerlo desde el inicio tiene costo marginal bajo y elimina riesgo de fuga de datos entre clientes (Master Spec §32, "Data Isolation").

## 3. Modelo de niveles y su mapeo MVP

| Nivel conceptual | Entidad técnica | ¿Existe en MVP? |
|---|---|---|
| User | `users` | Sí |
| Team | `workspaces` (equivalente funcional a Team en MVP) | Sí, versión simplificada |
| Organization | `organizations` | Sí — modelada aunque el MVP no exponga administración avanzada |
| Clients | proyectos etiquetados por cliente dentro de un workspace (`projects.clientRef`) | Parcial — campo presente, sin módulo de gestión de clientes dedicado |
| Projects | `projects` | Sí |

## 4. Modelo de permisos por nivel

Cada nivel debe resolver, como mínimo, estas tres preguntas (Master Spec §31):

1. **Ownership**: ¿quién es el dueño de este recurso?
2. **Permissions**: ¿qué roles pueden leer/escribir/ejecutar en este recurso?
3. **Access control**: ¿cómo se valida esto en cada request?

Propuesta de roles iniciales (a confirmar en `security-architecture.md`):

| Rol | Alcance | Puede |
|---|---|---|
| Owner | Organization | Todo, incluida gestión de miembros y billing (futuro) |
| Admin | Workspace | Crear/eliminar proyectos, gestionar agentes habilitados |
| Editor | Project | Definir challenge, adjuntar datos, ejecutar orquestación, revisar human-in-the-loop |
| Viewer | Project | Solo lectura de Intelligence Board, Opportunities, Deliverables |

## 5. Cómo se aplica el aislamiento técnicamente (principios, no implementación)

- Toda query/mutation del Application Layer recibe el contexto de identidad (`userId`) resuelto server-side (nunca confiado desde el cliente — Rule 4, §49).
- Toda query a una tabla con datos de negocio debe filtrar explícitamente por `organizationId`/`workspaceId` derivado de ese contexto, no por un parámetro enviado libremente por el frontend.
- Los índices de Convex deben diseñarse por `(organizationId, ...)` como prefijo, de forma que el aislamiento sea también una optimización de acceso, no solo una regla de negocio.

## 6. Conocimiento y memoria a través de niveles

`Project Memory` (§9.13, §33) tiene explícitamente varios niveles de alcance:

```text
Project Memory        → visible solo dentro del proyecto
Client Memory          → reutilizable entre proyectos del mismo cliente
Organization Memory    → reutilizable entre todos los proyectos de la organización
```

Esto significa que el modelo de aislamiento **no es "todo privado por proyecto"**: existen niveles intencionales de compartición ascendente, que deben ser explícitos y opt-in — nunca automáticos ni implícitos (evita fuga accidental de conocimiento de un cliente a otro). Ver `04-data/project-memory.md`.

## 7. Consideraciones para Enterprise (fuera del MVP, pero a no bloquear)

- SSO / SAML por organización.
- Políticas de retención de datos configurables por organización.
- Auditoría exportable por organización (base ya cubierta por `AuditLogs`, ver `04-data/data-model.md`).
- Posible aislamiento físico de datos (dedicated workspace) para clientes Enterprise — evaluar cuando exista demanda real, no diseñar prematuramente.

## 8. Qué NO se resuelve en el MVP

- Gestión visual de miembros de organización/roles (Administration, §9.14 — post-MVP).
- Billing por nivel de tenancy.
- White label por organización.

Estos quedan modelados conceptualmente pero no implementados, para no violar Rule 10 (§49): "El MVP debe mantenerse pequeño".
