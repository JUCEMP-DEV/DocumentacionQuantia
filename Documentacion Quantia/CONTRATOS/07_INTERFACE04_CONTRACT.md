# Contrato 07 — Spatial → Interfaz/Fase 04

[[00_INDICE_CONTRATOS|← Índice de contratos]] · [[../02_SPATIAL/11_CONTRATO_FASE04|Resumen técnico]]

**Versión documental:** 1.0  
**Estado:** PARCIAL  
**Productor:** Quantia Spatial  
**Consumidor:** Fase/Editor 04

## Muros
Campos objetivo:
- `id`
- `nivel`
- centerline raster
- centerline métrica
- espesor
- longitud
- rol
- confianza
- source/evidence
- lineage

## Espacios
Campos objetivo:
- `id`
- `nivel`
- `polygonRaster`
- `polygonMetric`
- `boundaryWallIds`
- `openingIds`
- nombre
- uso/categoría
- área

## Openings
Puertas, ventanas y garage door deben parametrizarse con host wall, offsets/posición sobre muro, ancho, orientación y confianza.

## Escaleras
Debe conservarse bbox/polygon, eje de tramo, dirección, landings y relación entre nivel origen/destino cuando esté resuelta.

## Estado de consumo
- muros: consumibles por 04;
- espacios: experimental;
- openings parametrizados: pendiente;
- alturas: pendiente;
- quantification readiness: pendiente.

## Validaciones
El consumidor no debe tratar campos pendientes o experimentales como definitivos. Toda geometría debe declarar nivel, coordenadas/unidades y linaje.

## Compatibilidad
El schema ejecutable de la interfaz 03→04/04 es la fuente de verdad cuando exista; este documento fija la intención contractual y los campos mínimos.
