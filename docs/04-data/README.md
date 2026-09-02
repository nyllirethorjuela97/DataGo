# 04 — Data

Modelo de datos, modelo de inteligencia, memoria de proyecto y procesamiento de archivos.

## Contenido

| Documento | Cubre |
|---|---|
| [`data-model.md`](./data-model.md) | Las 19+ entidades principales, relaciones, campos clave por entidad, reglas transversales de modelado |
| [`intelligence-model.md`](./intelligence-model.md) | La cadena Source → Evidence → Insight → Hypothesis → Opportunity → Recommendation, y la separación epistémica FACT/INTERPRETATION/HYPOTHESIS/RECOMMENDATION |
| [`project-memory.md`](./project-memory.md) | Los cinco niveles de memoria (Project/Client/Organization/Learning/Decision) y el principio de compartición opt-in |
| [`file-processing.md`](./file-processing.md) | Pipeline de ingesta de archivos/datasets hacia el Data Hub |

## Regla de lectura

`intelligence-model.md` es el documento más citado desde el resto de `/docs` (Agents, Orchestrator, Security) porque codifica la Regla 6 y 9 del Master Spec (§49): todo insight debe tener evidencia, y la IA no debe inventar datos. Cualquier cambio a este documento debe revisarse contra esas dos reglas explícitamente.
