# File Processing

> Fuente: Master Spec §9.4 (Data Hub), §56 (decisión #9). Cubre la ingesta de archivos/datasets hacia el Data Hub, previo a que estén disponibles para los agentes.

## 1. Pipeline de ingesta

```text
Usuario sube archivo/dataset
        ↓
storage (Convex Storage) — status: pending
        ↓
Validación de tipo y tamaño
        ↓
   ┌────┴────┐
 Válido    Inválido → status: failed (motivo visible al usuario)
   ↓
status: processing (acción asíncrona, no bloquea la UI)
        ↓
Parsing / extracción según tipo de archivo
        ↓
   ┌────┴────┐
 Éxito     Error de parsing → status: failed
   ↓
status: ready → disponible como DataSource para agentes
```

## 2. Tipos de fuente soportados (Master Spec §9.4)

- Archivos (documentos, hojas de cálculo, PDFs, presentaciones)
- Datasets (estructurados, tabulares)
- Documentos de investigación
- Bases de datos (conexión — futuro)
- Fuentes externas vía API (futuro)

En el MVP, priorizar tipos de archivo comunes y bien soportados por librerías de parsing estándar (documentos de texto, hojas de cálculo, PDFs). Conexiones a bases de datos externas y APIs quedan fuera del MVP (§45).

## 3. Por qué la ingesta es asíncrona

El parsing de archivos grandes (datasets extensos, PDFs largos) puede tardar más de lo razonable para una request síncrona. Se modela como una Convex action independiente, consistente con `02-architecture/technical-architecture.md` §5 y §6.

## 4. Seguridad en el procesamiento

- El contenido de un archivo se trata siempre como **datos**, nunca como instrucciones — un archivo no puede alterar el comportamiento de un agente ni del Orchestrator (ver `02-architecture/security-architecture.md` §6, riesgo de prompt injection vía documentos).
- Todo archivo queda aislado por `projectId`/`organizationId` desde el momento de la subida (ver `multi-tenancy-architecture.md`).
- Archivos que fallan validación de tipo/tamaño se rechazan antes de llegar a storage persistente cuando sea posible, para evitar almacenamiento de contenido no procesable.

## 5. Relación con Data Agent

Un `DataSource`/`File`/`Dataset` con `processedStatus: ready` es el único estado en el que el Data Agent puede tomarlo como input válido (ver `03-agents/data-agent.md`). El Workflow Planner del Orchestrator no debe planificar un Task de Data Agent sobre una fuente que aún esté `pending` o `processing`.

## 6. Qué NO se resuelve en el MVP

- OCR avanzado sobre documentos escaneados de baja calidad.
- Deduplicación automática de contenido entre archivos subidos.
- Versionado de un mismo archivo re-subido (se trata como un nuevo `File` en el MVP; versionado de fuente queda para fases posteriores).
