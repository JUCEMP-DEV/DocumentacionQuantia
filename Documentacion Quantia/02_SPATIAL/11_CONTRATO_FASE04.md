# Contrato objetivo para Fase 04

[[10_HERRAMIENTAS|← Anterior]] · [[00_INDICE|Índice]] · [[12_SIGUIENTE_FASE|Siguiente →]]

Documento contractual detallado: [[../CONTRATOS/07_INTERFACE04_CONTRACT|Spatial → Interfaz/Fase 04]].

## Muros

```text
id
nivel
centerline raster
centerline métrica
espesor
longitud
rol
confianza
source/evidence
lineage
```

## Espacios

```text
id
nivel
polygonRaster
polygonMetric
boundaryWallIds
openingIds
nombre
uso/categoría
área
```

## Puertas / ventanas / garage door

```text
id
type
host_wall_id
offset_start
offset_end
opening_width
center_on_wall
orientación
confianza
```

## Escaleras

```text
id
bbox/polygon
flight_axis
direction
landing_regions
nivel origen/destino
```

## Estado

- Muros: **consumibles por 04**
- Espacios: **experimental**
- Openings parametrizados: **pendiente**
- Alturas: **pendiente**
- Quantification readiness: **pendiente**

El schema ejecutable de la interfaz es la fuente de verdad cuando exista; este documento resume el objetivo funcional.
