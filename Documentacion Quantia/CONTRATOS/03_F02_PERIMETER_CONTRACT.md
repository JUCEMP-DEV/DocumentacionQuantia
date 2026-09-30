# Contrato 03 — F02 Perímetro

[[00_INDICE_CONTRATOS|← Índice de contratos]]

**Versión documental:** 1.0  
**Estado:** VIGENTE / BASELINE INMUTABLE  
**Productor:** F02  
**Consumidores:** reconstrucción de muros, topología y validaciones posteriores

## Entrada
- `LevelView`
- `RawEvidence[]`
- escala resuelta

## Salida
`EditablePerimeterModel` con geometría base, segmentos perimetrales, conectividad, evidencia y trazabilidad.

## Coordenadas y unidades
Debe conservarse correspondencia entre raster canónico y geometría métrica. `px_per_m` y la transformación métrica pertenecen al contexto de escala.

## Validaciones
- conectividad del perímetro;
- geometría no degenerada;
- asociación de cotas sólo con evidencia suficiente;
- trazabilidad a evidencia fuente.

## Regla de inmutabilidad
F02 se conserva como baseline histórico. Las correcciones de fases posteriores no deben sobrescribir ni reinterpretar silenciosamente su evidencia original.

## Linaje
`LevelView + RawEvidence + Scale → EditablePerimeterModel`.

## Compatibilidad
Cualquier cambio que altere la semántica del baseline o sus coordenadas requiere nueva versión.
