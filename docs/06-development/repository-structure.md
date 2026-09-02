# Repository Structure

> Fuente: Master Spec §48. Estructura propuesta — la estructura definitiva se confirma durante Phase 1 (Foundation) del roadmap, pero debe partir de esta base para no contradecir el resto de `/docs`.

## Estructura propuesta

```text
datago/
│
├── README.md
├── DATAGO_MASTER_SPEC.md
│
├── docs/
│   ├── 01-product/
│   ├── 02-architecture/
│   ├── 03-agents/
│   ├── 04-data/
│   ├── 05-ux/
│   └── 06-development/
│
├── app/                 # Next.js app router
├── components/          # UI compartida
├── convex/               # Backend: schema, queries, mutations, actions
│   ├── schema.ts          # Entidades de 04-data/data-model.md
│   ├── orchestrator/       # Ver 02-architecture/orchestrator-architecture.md
│   ├── agents/              # Un módulo por agente, ver 03-agents/
│   └── tools/                # Ver 02-architecture/tool-architecture.md
├── lib/                  # AI Abstraction Layer, utilidades compartidas
├── public/
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
│
├── .env.example
├── package.json
├── tsconfig.json
└── ...
```

## Mapeo de `/docs` a carpetas técnicas (referencia para Phase 1)

| Documento de `/docs` | Carpeta técnica correspondiente |
|---|---|
| `04-data/data-model.md` | `convex/schema.ts` |
| `02-architecture/orchestrator-architecture.md` | `convex/orchestrator/` |
| `03-agents/*.md` | `convex/agents/<agent-name>/` |
| `02-architecture/tool-architecture.md` | `convex/tools/` |
| `02-architecture/ai-abstraction-layer.md` | `lib/ai/` |
| `05-ux/screen-inventory.md` | `app/(project)/<sección>/` |
| `06-development/testing-and-evaluation.md` | `tests/` |

## Reglas de estructura

1. `docs/` no se reorganiza sin actualizar todos los enlaces relativos entre documentos (varios documentos de este paquete se referencian entre sí por ruta).
2. Ningún código de agente vive fuera de `convex/agents/` — evita que la lógica de un agente termine dispersa entre frontend y backend (viola Rule 8, responsabilidades específicas).
3. `lib/ai/` es el único punto de importación permitido para clientes de proveedores de IA — ningún componente de `app/` ni función fuera de `lib/ai/`/`convex/` debe importar un SDK de proveedor de IA directamente (refuerza `ai-abstraction-layer.md` y Rule 4 de seguridad).
