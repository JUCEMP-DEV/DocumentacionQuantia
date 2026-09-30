# Principios y flujo general

[[00_INDICE|← Índice]] · [[02_F01_NIVELES|Siguiente →]]

## Objetivo

Reconstruir el plano arquitectónico mediante geometría trazable, producir un WallGraph coherente, cerrar espacios y entregar geometría editable a Fase 04.

## Principio

> La imagen original es la verdad visual.  
> La geometría reconstruida es una hipótesis verificable.

## Capas

```text
EVIDENCIA
   ↓
RECONSTRUCCIÓN DE MUROS
   ↓
CIERRE Y TOPOLOGÍA DE ESPACIOS
   ↓
ELEMENTOS ARQUITECTÓNICOS + ENTREGA A 04
```

## Flujo maestro

```text
DOCUMENTO / PLANO
        ↓
F01 — Identificación de niveles
        ↓
LevelView por planta
        ↓
F01.5 — Extracción multimodal de evidencia
        ↓
Normalización de escala / raster métrico
        ↓
F02 — Perímetro base
        ↓
Adaptive Reconstruction
        ↓
PostFilter V4
        ↓
Canonical WallGraph
        ↓
CALL 2 — Auditoría visual diferencial
        ↓
DeltaValidator
        ↓
Correcciones + Finalizer
        ↓
WallGraph final
        ↓
Canonicalización residual
        ↓
Cierre lógico de gaps
        ↓
Polygonize / espacios
        ↓
Validación geométrica y semántica
        ↓
Grounding de elementos arquitectónicos
        ↓
Contrato de geometría
        ↓
FASE 04 — Editor
```

## Regla de cierre

Una etapa no se considera cerrada sólo porque el test marque `PASSED`.

Debe existir coherencia entre:

```text
resultado geométrico
+ resultado visual
+ consistencia topológica
+ trazabilidad
+ contrato de salida
```
