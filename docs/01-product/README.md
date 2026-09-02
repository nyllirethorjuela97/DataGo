# 01 — Product

Documentación de producto de DataGo: qué es, qué problema resuelve, para quién, y cómo se organiza conceptualmente en módulos.

Esta carpeta responde al **"qué"** y al **"para quién"**. El **"cómo"** técnico vive en `02-architecture/`, el detalle de agentes en `03-agents/`, el modelo de datos en `04-data/`, la navegación en `05-ux/` y el plan de ejecución en `06-development/`.

## Contenido

| Documento | Descripción |
|---|---|
| [`product-architecture.md`](./product-architecture.md) | Visión, problema, usuarios objetivo, propuesta de valor, diferenciación, principio "Challenge Driven Intelligence", modelo funcional conceptual, catálogo de 14 módulos de la plataforma y visión de largo plazo. |

## Fuente de verdad

Todo el contenido de esta carpeta deriva de `DATAGO_MASTER_SPEC.md` (Secciones 1–9, 46, 55) y no debe contradecirlo. Cualquier cambio de alcance de producto debe reflejarse primero en el Master Spec.

## Relación con el resto de `/docs`

```text
01-product        → QUÉ es DataGo y QUÉ módulos tiene
02-architecture   → CÓMO se construye (funcional + técnico + orquestación + seguridad)
03-agents         → CÓMO piensa y actúa cada agente
04-data           → QUÉ datos existen y cómo se relacionan
05-ux             → CÓMO navega y qué experimenta el usuario
06-development    → CUÁNDO y en qué orden se construye
```
