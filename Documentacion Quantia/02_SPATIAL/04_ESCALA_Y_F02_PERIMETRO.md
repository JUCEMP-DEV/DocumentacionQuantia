# Escala y F02 — Perímetro

[[03_F01_5_EVIDENCIA|← Anterior]] · [[00_INDICE|Índice]] · [[05_RECONSTRUCCION_MUROS|Siguiente →]]

## Normalización de escala

### Entrada

Evidencia de cotas/ejes + raster.

### Proceso

- Resolver `px/m`.
- Reconciliar escala.
- Rerasterizar a densidad métrica estable.
- Reproyectar evidencia validada.

### Salida

- `px_per_m`
- raster métrico canónico
- transformación métrica

### Estado

**CUMPLE**, sujeto a la calidad de la evidencia de escala.

### Herramientas

- PyMuPDF
- NumPy
- OpenCV
- `ScaleEvidenceResolver`
- `LevelViewRerasterizer`
- `PerimeterRasterReprojector`

---

## F02 — Perímetro base

### Entrada

- `LevelView`
- `RawEvidence`
- escala

### Proceso

- Detectar geometría exterior.
- Reconciliar evidencia.
- Construir segmentos perimetrales.
- Resolver conectividad.
- Validar perímetro.
- Asociar cotas cuando existe evidencia suficiente.

### Salida

- `EditablePerimeterModel`
- geometría base del perímetro
- evidencia y trazabilidad

### Estado

**CUMPLE como baseline**

### Regla

F02 se conserva como evidencia histórica. Las correcciones posteriores no deben sobrescribirlo.

### Herramientas

- Shapely
- OpenCV
- NumPy
- Pydantic
- `BoundaryGeometryDetector`
- `PerimeterWallGraphBuilder`
- `PerimeterWallResolver`
- `PerimeterWallValidator`
