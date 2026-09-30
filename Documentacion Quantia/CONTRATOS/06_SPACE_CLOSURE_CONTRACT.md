# Contrato 06 — Cierre de espacios

[[00_INDICE_CONTRATOS|← Índice de contratos]]

**Versión documental:** 1.0  
**Estado:** EXPERIMENTAL  
**Productor:** cierre topológico / polygonize  
**Consumidores:** correlación semántica y Fase 04

## Entrada
- Canonical WallGraph;
- perímetro baseline;
- cierres lógicos;
- escala válida cuando exista.

## Regla principal
Los openings no se convierten en muro físico. Un cierre lógico puede existir sólo para topología y debe distinguirse de la geometría física.

## Salida
Polígonos candidatos de espacio con:
- identificador;
- nivel;
- polygon raster;
- polygon métrico cuando exista escala;
- `boundaryWallIds`;
- referencias a openings/cierres lógicos;
- métricas y estado de validación.

## Validaciones
- cobertura respecto al footprint arquitectónico;
- balance geométrico;
- slivers;
- dangles;
- protección perimetral;
- consistencia de orientación y junctions;
- correlación posterior `SPACE ↔ polygon`.

## Estado actual
El cierre del footprint puede ser alto sin garantizar la separación correcta de recintos. Un polígono cerrado no equivale por sí solo a un espacio semánticamente correcto.

## Compatibilidad
Consumidores deben aceptar estados REVIEW/EXPERIMENTAL hasta que se valide la separación interior.
