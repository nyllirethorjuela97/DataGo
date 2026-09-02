# AI Abstraction Layer

> Fuente: Master Spec §36, §56 (decisión #7).

## 1. Propósito

Evitar que la lógica de negocio de DataGo quede acoplada a un proveedor de modelos específico.

```text
DataGo
  ↓
AI Service (interfaz interna, estable)
  ↓
Model Provider (Claude u otros, intercambiable)
```

## 2. Contrato de la interfaz interna (conceptual)

Todo agente invoca IA a través de un servicio interno con una interfaz estable, independientemente del proveedor real detrás. Este servicio debe exponer, como mínimo:

- **Completar/generar** con un input estructurado (system context del agente + task input + herramientas disponibles).
- **Uso de herramientas** (tool calling) de forma normalizada, para que el contrato de `Tool` (ver `tool-architecture.md`) no dependa del formato específico de un proveedor.
- **Métricas de uso** normalizadas (tokens, duración, costo estimado) para alimentar Observability (§51).
- **Manejo de errores** normalizado (timeout, rate limit, content filtering) que el Orchestrator pueda interpretar de forma uniforme para decidir reintentos.

## 3. Por qué es una decisión arquitectónica y no un detalle de implementación

- Permite evaluar/cambiar de modelo (o usar distintos modelos para distintos agentes según costo/calidad) sin tocar la lógica de cada agente.
- Centraliza rate limiting, manejo de secretos y control de costo en un solo punto (alineado con `security-architecture.md` §6).
- Facilita la evaluación de IA (§53, AI Evaluation) al tener un punto único de instrumentación.

## 4. Relación con el Agent Hub

Cada `Agent` en el Agent Registry declara qué modelo/proveedor prefiere (o "cualquiera compatible"), pero la resolución final del proveedor ocurre en la AI Abstraction Layer, no en el propio agente. Esto permite, por ejemplo, degradar a un modelo más barato automáticamente si el proyecto excede su presupuesto de análisis (ver monetización, Master Spec §46 "Analysis Consumption").

## 5. No incluido en el MVP

- Selección automática de "mejor modelo por tarea" basada en benchmarks internos (post-MVP).
- Soporte simultáneo de múltiples proveedores en producción (el MVP puede operar con un único proveedor aprobado detrás de la interfaz, pero la interfaz debe existir desde el inicio para no requerir refactor).
