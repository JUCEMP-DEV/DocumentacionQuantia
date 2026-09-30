# Correlaciones entre elementos

[[07_CIERRE_ESPACIOS|← Anterior]] · [[00_INDICE|Índice]] · [[09_ESTADO_ACTUAL|Siguiente →]]

| Elemento A | Elemento B | Correlación necesaria | Uso |
|---|---|---|---|
| Página | LevelView | `source_bbox_px` + transform | Trazabilidad |
| RawEvidence | LevelView | coordenadas locales | Ubicación |
| Cota | Eje/Muro | proyección + orientación + proximidad | Escala/dimensión |
| Cota | Espacio | bbox + dirección + límites del recinto | Dimensiones |
| Cara de muro | Cara paralela | orientación + separación + solape + espesor | Centerline |
| Muro | Junction | intersección / endpoint | Topología |
| Muro | Muro | continuidad + ángulo + separación | Reconstrucción |
| Gap | Muro | host + posición longitudinal | Opening |
| Puerta/Ventana | Host wall | proyección + span + orientación | Grounding |
| Escalera | Espacio | bbox + patrón + conectividad | Semántica |
| SPACE semántico | Polígono | solape/contención + límites | Nombre del recinto |
| Polígono | Footprint | área + contención | Validación |
| Muro eliminado | Topología | efecto antes/después | Restaurar/Rechazar |
| Call 2 delta | Muro origen | ID + lineage | Trazabilidad |
| WallGraph | Fase 04 | raster + métrico + IDs | Edición |

## Correlación crítica pendiente

```text
SPACE
↔ polygon
↔ boundary walls
↔ dimensions
```

Hasta resolver esta correlación no debe asumirse:

```text
Recámara = 4 × 5 = 20 m²
```

aunque las tres evidencias existan por separado.
