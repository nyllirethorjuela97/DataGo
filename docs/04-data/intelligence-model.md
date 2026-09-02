# Intelligence Model

> Fuente: Master Spec §24–29, §50, §56 (decisión #10, Evidence model). Este es el documento más sensible de todo `/docs`: define cómo DataGo diferencia lo que sabe de lo que interpreta.

## 1. Cadena de estructuración de la inteligencia (§24)

```text
SOURCE
 ↓
EVIDENCE
 ↓
INSIGHT
 ↓
HYPOTHESIS
 ↓
OPPORTUNITY
 ↓
RECOMMENDATION
```

Nota: Hypothesis no siempre es un paso secuencial estricto después de Insight — puede surgir en paralelo cuando la evidencia disponible no alcanza para un Insight validado (ver `03-agents/insight-agent.md`). El diagrama representa el orden conceptual de madurez de la inteligencia, no un pipeline rígido de una sola vía.

## 2. Separación epistémica obligatoria (§50)

```text
FACT             → verificado, con fuente directa
INTERPRETATION   → lectura razonada sobre uno o más FACTs
HYPOTHESIS       → interpretación no verificada, requiere validación
RECOMMENDATION   → acción sugerida basada en evidencia + interpretación
```

**Regla dura:** estos cuatro tipos nunca se presentan como equivalentes. El campo `epistemicType` es obligatorio en Evidence, Insight, Hypothesis, Opportunity y Recommendation, y el frontend debe darles tratamiento visual/lingüístico distinto (ver `05-ux/screen-inventory.md`, Intelligence Board).

Cuando no existe evidencia suficiente, el sistema debe expresar incertidumbre explícitamente (`confidence` bajo + lenguaje condicional), nunca omitir el campo o rellenar con afirmación categórica.

## 3. Evidence

| Campo | Tipo | Descripción |
|---|---|---|
| `source` | string/ref | De dónde proviene (dataset, documento, búsqueda externa) |
| `location` | string | Ubicación específica dentro de la fuente (página, celda, sección) |
| `content/reference` | text/ref | El contenido citado o referencia al dato exacto |
| `date` | date | Fecha del dato/fuente (no de creación del registro) |
| `confidence` | number (0–1) | Confianza en la validez de esta evidencia específica |
| `relevance` | number (0–1) o enum | Relevancia respecto al Challenge activo |
| `epistemicType` | enum | Casi siempre `FACT`, salvo evidencia de fuentes que ya son interpretativas (p. ej., un estudio con conclusiones propias) |
| `agentRunId` | ref | Qué ejecución la generó |

## 4. Insight

| Campo | Tipo | Descripción |
|---|---|---|
| `statement` | string | El insight en una frase clara |
| `explanation` | text | Por qué importa / qué significa |
| `evidence[]` | ref[] | Evidencia que lo sustenta (obligatorio, mínimo 1) |
| `confidence` | number (0–1) | |
| `category` | string | Ej. shopper, mercado, portafolio, canal |
| `businessRelevance` | enum | `low`, `medium`, `high` |
| `epistemicType` | enum | `FACT` (raro), `INTERPRETATION` (lo más común) |

## 5. Hypothesis

Una Hypothesis representa una interpretación que necesita validación — se diferencia de un Insight únicamente en su nivel de confianza/verificación, no en su estructura de datos (comparte los mismos campos que Insight, más):

| Campo adicional | Tipo | Descripción |
|---|---|---|
| `validationStatus` | enum | `open`, `under_review`, `validated` (se promueve a Insight), `discarded` |
| `criticalFlag` | boolean | Marca si requiere revisión humana obligatoria (ver `functional-architecture.md` §5) |

## 6. Opportunity

| Campo | Tipo | Descripción |
|---|---|---|
| `title` | string | |
| `description` | text | |
| `sourceInsights[]` | ref[] | Insights de origen (obligatorio) |
| `potentialImpact` | enum | `low`, `medium`, `high` |
| `feasibility` | enum | `low`, `medium`, `high` |
| `priority` | number/enum | Resultado del scoring del Opportunity Agent (versionado, ver `02-architecture/observability-and-versioning.md`) |
| `recommendedNextAction` | text | |
| `category` | enum | comercial, shopper, canal, portafolio, comunicación, experiencia (§9.9) |
| `epistemicType` | enum | Casi siempre `INTERPRETATION` o `RECOMMENDATION` según su madurez |

## 7. Recommendation

| Campo | Tipo | Descripción |
|---|---|---|
| `opportunityId` | ref | Obligatorio — toda recomendación se relaciona con una oportunidad (§29) |
| `text` | text | Accionable, no ambigua |
| `justification` | text | Basada en evidencia — referencia a Insights/Evidence |
| `epistemicType` | enum | `RECOMMENDATION` |
| `reviewStatus` | enum | `pending_review`, `approved`, `modified`, `rejected` (human-in-the-loop) |

## 8. Trazabilidad completa (requisito no negociable — Rule 6)

Cualquier Recommendation debe poder trazarse hacia atrás hasta su(s) Evidence original(es):

```text
Recommendation → Opportunity → Insight(s) → Evidence(s) → Source
```

Esta cadena debe ser recuperable en una sola consulta desde la UI (Intelligence Board) para que el usuario pueda auditar de dónde salió cualquier afirmación del sistema.

## 9. Qué NO cubre este modelo

- No define la lógica de scoring/priorización en sí (eso es lógica de agente, versionada por separado, ver `03-agents/opportunity-agent.md`).
- No define el formato de presentación visual (eso es `05-ux/screen-inventory.md`).
