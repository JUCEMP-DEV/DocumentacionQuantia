# Contrato 04 — Canonical WallGraph

[[00_INDICE_CONTRATOS|← Índice de contratos]]

**Versión documental:** 1.0  
**Estado:** VIGENTE / PARCIALMENTE VALIDADO  
**Productores:** reconstrucción adaptativa, PostFilter V4 y CanonicalWallGraphFinalizer  
**Consumidores:** Call 2, cierre de espacios y Fase 04

## Principio
`1 muro físico = 1 centerline`.

## Contenido mínimo por muro
- identificador estable;
- nivel;
- centerline en raster;
- centerline métrica cuando exista escala válida;
- longitud;
- rol/clasificación;
- confianza;
- referencias de evidencia;
- linaje de consolidación/corrección.

## Relaciones
El grafo debe conservar relaciones válidas entre muros y junctions. Las referencias inválidas o geometrías degeneradas deben eliminarse o quedar en diagnóstico.

## Validaciones
- IDs únicos;
- centerlines no degeneradas;
- referencias existentes;
- consistencia del sistema de coordenadas;
- fusión sólo de hipótesis compatibles;
- preservar evidencia descartada para auditoría.

## Estado actual
La canonicalización es funcional pero todavía puede conservar caras/paralelos residuales; por ello el contrato admite estado REVIEW cuando la consolidación no es concluyente.

## Compatibilidad
Cambios de representación de centerline, IDs o relaciones requieren nueva versión.
