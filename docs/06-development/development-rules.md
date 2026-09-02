# Development Rules

> Fuente: Master Spec §49, §58, §46 (Monetization, referencia arquitectónica). Reglas obligatorias para cualquier persona o agente de IA que desarrolle sobre DataGo.

## Las 10 reglas (§49)

| # | Regla | Dónde se aplica en `/docs` |
|---|---|---|
| 1 | No construir funcionalidades fuera del alcance aprobado | `mvp-scope.md` |
| 2 | No cambiar arquitectura silenciosamente | `02-architecture/architectural-decisions.md` §"Proceso de cambio" |
| 3 | No almacenar secretos en GitHub | `02-architecture/security-architecture.md` §2 |
| 4 | No exponer API keys en frontend | `02-architecture/security-architecture.md` §2, §3 |
| 5 | Todo Agent Run debe ser trazable | `02-architecture/orchestrator-architecture.md` §3; `02-architecture/observability-and-versioning.md` §1 |
| 6 | Todo insight importante debe tener evidencia | `04-data/intelligence-model.md` §4, §8 |
| 7 | El Orchestrator controla la ejecución de agentes | `02-architecture/orchestrator-architecture.md`; `03-agents/orchestrator-agent-protocol.md` §3 |
| 8 | Los agentes deben tener responsabilidades específicas | `03-agents/agent-principles.md` §5 |
| 9 | La IA no debe inventar datos | `03-agents/agent-principles.md` §7; `04-data/intelligence-model.md` §8 |
| 10 | El MVP debe mantenerse pequeño | `mvp-scope.md` |

Estas reglas no son sugerencias de estilo: son restricciones de arquitectura. Un pull request que las viole debe rechazarse en revisión, independientemente de si "funciona".

## Instrucción para agentes de desarrollo de IA (§58)

Cualquier agente de IA (p. ej. Claude Code) que trabaje sobre el repositorio `datago` debe, en este orden:

1. Leer `DATAGO_MASTER_SPEC.md`.
2. Revisar la documentación complementaria en `/docs` relevante a la tarea.
3. Identificar decisiones pendientes en `02-architecture/architectural-decisions.md` que afecten la tarea.
4. No inventar arquitectura no documentada.
5. No comenzar desarrollo antes de que la arquitectura relevante esté aprobada.
6. Respetar el alcance del MVP (`mvp-scope.md`).
7. Documentar cualquier cambio (actualizar el documento de `/docs` correspondiente en el mismo PR que el código).
8. Mantener trazabilidad (todo AgentRun/WorkflowRun con su registro completo, ver Rule 5).
9. Evitar funcionalidades innecesarias (Rule 1, Rule 10).
10. Pedir aprobación explícita ante cambios arquitectónicos importantes (no asumir aprobación implícita por ausencia de objeción).

## Monetización — restricción de diseño, no de implementación (§46)

La arquitectura debe **prepararse** para los siguientes modelos, sin implementarlos en el MVP:

- Free / Trial
- Professional
- Team
- Enterprise
- Premium Agents
- Analysis Consumption (cobro por consumo de análisis/IA — depende directamente de que `02-architecture/observability-and-versioning.md` §1 registre tokens/usage por Agent Run desde el día uno)
- Private Workspaces
- White Label

**Implicación concreta de diseño:** el modelo de datos (`04-data/data-model.md`) y el registro de uso (`observability-and-versioning.md`) deben soportar agregación de consumo por organización desde el inicio, aunque no exista aún ninguna pantalla de billing.

## Regla de commits y cambios de esquema

- Ningún cambio de esquema de datos que afecte una entidad documentada en `04-data/data-model.md` se implementa sin actualizar primero el documento correspondiente.
- Ningún agente nuevo se agrega al Agent Registry sin un documento en `03-agents/` que complete el contrato de §11 del Master Spec.
