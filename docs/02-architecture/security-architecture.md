# Security Architecture

> Fuente: Master Spec §32, §49 (Rules 3, 4), §51, §56 (decisión #5). Consolida principios de seguridad transversales a toda la plataforma.

## 1. Principios no negociables (derivados directamente del Master Spec)

| # | Principio | Fuente |
|---|---|---|
| 1 | Nunca almacenar API keys, passwords, tokens o credenciales privadas en el repositorio | §32, Rule 3 |
| 2 | Nunca exponer API keys en el frontend | Rule 4 |
| 3 | La autorización se valida siempre en backend, nunca solo en frontend | §32 |
| 4 | Un usuario no accede a información de otra organización o workspace | §32, ver `multi-tenancy-architecture.md` |
| 5 | Las acciones importantes deben ser auditables | §32 |
| 6 | Todo Agent Run debe ser trazable | Rule 5 |

## 2. Gestión de secretos

- Variables sensibles viven exclusivamente en variables de entorno del entorno de deployment (Vercel) y de Convex (environment variables de Convex), nunca en código ni en `.env` versionado.
- `.env.example` en el repositorio documenta **nombres** de variables requeridas, nunca valores reales.
- Claves de proveedores de IA se consumen únicamente desde el AI Abstraction Layer (server-side / Convex actions), nunca desde componentes de cliente.

## 3. Autenticación (decisión pendiente — Master Spec §56 #5)

**Estado:** por definir. Candidatos a evaluar en el checkpoint de arquitectura:

| Opción | A favor | En contra |
|---|---|---|
| Clerk | UI lista, gestión de organizaciones incorporada (encaja con multi-tenancy) | Dependencia externa adicional, costo |
| Convex Auth | Integración nativa con el resto del stack | Más joven, menos features "out of the box" de gestión de org |
| Auth.js (NextAuth) | Flexible, muy usado en Next.js | Requiere más trabajo manual para modelar Organization/Workspace |

**Recomendación preliminar:** Clerk u otra solución con soporte nativo de "Organizations", dado que la jerarquía User → Team → Organization es un requisito desde el día uno (§31) y reimplementar esa lógica manualmente añade riesgo de seguridad innecesario. Confirmar en el checkpoint antes de Fase 1.

## 4. Autorización

Modelo de referencia (RBAC simple, alineado con `multi-tenancy-architecture.md` §4):

```text
Request → resolver identidad (server-side, desde sesión validada)
        → resolver rol efectivo (Owner/Admin/Editor/Viewer) para el recurso solicitado
        → resolver organizationId/workspaceId del recurso
        → ¿coincide con el contexto del usuario? → permitir
        → ¿rol permite la operación solicitada? → permitir/denegar
```

Toda mutation que modifique datos de negocio debe pasar por esta cadena antes de tocar la base de datos. No debe existir ninguna mutation "de conveniencia" que salte la validación de rol.

## 5. Aislamiento de datos

Ver `multi-tenancy-architecture.md`. Resumen de seguridad: el aislamiento no es solo una regla de UI (ocultar proyectos de otra organización) sino una restricción a nivel de query server-side. Ningún endpoint debe depender de que el frontend "no pida" datos ajenos.

## 6. Seguridad específica de agentes de IA

Riesgos particulares de una plataforma agentic que deben mitigarse desde el diseño:

| Riesgo | Mitigación de diseño |
|---|---|
| Prompt injection desde documentos/datasets cargados por el usuario | Los agentes que procesan contenido externo (Data Agent, Research Agent) deben tratar el contenido de archivos como **datos**, nunca como instrucciones del sistema; el system prompt de cada agente vive fuera del contexto de usuario |
| Un agente ejecuta una tool fuera de su permiso declarado | Cada Tool valida `permissions` contra el `Agent` que la invoca antes de ejecutar (ver `03-agents/agent-principles.md` y `tool-architecture.md`) |
| Fuga de datos de un proyecto/cliente hacia otro vía memoria compartida | Project Memory / Client Memory / Organization Memory son niveles explícitos y opt-in, nunca compartición implícita (ver `04-data/project-memory.md`) |
| Alucinación presentada como hecho verificado | Separación obligatoria FACT/INTERPRETATION/HYPOTHESIS/RECOMMENDATION (§50) validada por el Orchestrator antes de aceptar un output |
| Costo/abuso por ejecución descontrolada de agentes | Registro de tokens/usage por Agent Run (§51) + límites configurables por plan (ver `06-development/mvp-scope.md` y Master Spec §46 Monetization) |

## 7. Auditabilidad

Toda acción sensible (cambios de permisos, ejecución de workflow, aprobación/rechazo human-in-the-loop, generación de deliverables) se registra en `AuditLogs` con: actor, acción, recurso afectado, timestamp, resultado. Esta tabla es de solo-append; no se edita ni se borra desde la aplicación.

## 8. Checklist de seguridad para Fase 10 (QA/Security/Deployment del roadmap)

- [ ] Ninguna API key visible en bundle de frontend (verificar en build de producción).
- [ ] Todas las queries/mutations de datos de negocio filtran por tenant derivado del server, no del cliente.
- [ ] Pruebas de aislamiento: usuario A no puede leer/escribir recursos de organización B (test de integración obligatorio, ver `06-development/testing-and-evaluation.md`).
- [ ] Rutas de Agent Run registran evidencia de trazabilidad completa (agente, versión, input, output, validación).
- [ ] Revisión de permisos server-side para cada mutation nueva antes de merge (parte de Development Rules, `06-development/development-rules.md`).
