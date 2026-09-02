# Project Memory

> Fuente: Master Spec §33, §9.13, §56 (decisión #3).

## 1. Niveles de memoria

```text
Project Memory      → información específica del proyecto
Client Memory        → conocimiento reutilizable del cliente (entre proyectos)
Organization Memory   → conocimiento organizacional (entre clientes)
Learning Memory       → aprendizajes derivados de proyectos
Decision Memory        → registro de decisiones tomadas
```

## 2. Qué conserva cada nivel

| Nivel | Contenido | Alcance de lectura |
|---|---|---|
| **Project Memory** | Contexto del challenge, decisiones tomadas dentro del proyecto, aprendizajes, resultados, insights, oportunidades, recomendaciones | Solo agentes/usuarios del proyecto |
| **Client Memory** | Conocimiento reutilizable entre proyectos del mismo cliente (journeys de shopper ya documentados, oportunidades históricas, tono preferido) | Proyectos del mismo `clientRef` dentro de la organización |
| **Organization Memory** | Conocimiento organizacional (frameworks, metodologías propias, aprendizajes agregados de múltiples clientes) | Todos los proyectos de la organización |
| **Learning Memory** | Patrones aprendidos sobre qué funciona (qué tipo de evidencia suele derivar en oportunidades de alto impacto, por ejemplo) | Uso interno del sistema para mejorar planificación del Orchestrator — no necesariamente expuesto directamente al usuario en el MVP |
| **Decision Memory** | Registro explícito de decisiones humanas (aprobaciones/rechazos de human-in-the-loop) | Ligado al proyecto, visible en auditoría |

## 3. Principio de compartición: opt-in, nunca implícito

> La compartición ascendente de memoria (Project → Client → Organization) **nunca ocurre automáticamente**. Requiere una acción explícita (del usuario o de una regla de negocio auditable) que promueva un elemento de un nivel a otro.

Esto evita el riesgo descrito en `02-architecture/security-architecture.md` §6: fuga de conocimiento de un cliente a otro por compartición implícita de memoria. Es también la razón por la cual `Knowledge.sharingApproved` existe como campo explícito en `04-data/data-model.md` §3.

## 4. Flujo de promoción de memoria

```text
Insight/Opportunity recurrente detectado en Project Memory
        ↓
Usuario (o regla aprobada) decide promoverlo
        ↓
   ┌────┴────┐
Client Memory   Organization Memory
   (mismo         (cualquier
    clientRef)      proyecto)
        ↓
Queda disponible para la Tool `knowledge_retrieval`
en proyectos futuros dentro de ese alcance
```

## 5. Relación con los agentes

Cada agente puede **leer** los niveles de memoria relevantes a su tarea (ver tabla "Memory" en cada documento de `03-agents/`), pero **no escribe memoria directamente**: el Orchestrator/Result Store es responsable de persistir memoria a partir de resultados validados, siguiendo el mismo principio de no-escritura-directa descrito en `03-agents/orchestrator-agent-protocol.md` §3.

## 6. Alcance en el MVP

En el MVP se implementa como mínimo **Project Memory** completa. Client Memory y Organization Memory se modelan en el esquema (para no requerir migración futura) pero su UI de gestión/promoción puede ser mínima o inexistente hasta fases posteriores — consistente con Rule 10 (§49, "el MVP debe mantenerse pequeño").
