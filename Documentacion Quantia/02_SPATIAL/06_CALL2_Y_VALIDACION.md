# Call 2, validación y aplicación de deltas

[[05_RECONSTRUCCION_MUROS|← Anterior]] · [[00_INDICE|Índice]] · [[07_CIERRE_ESPACIOS|Siguiente →]]

## Call 2 — Auditoría visual diferencial

### Entrada

- plano original;
- WallGraph actual.

### Prioridad

```text
Plano original
    >
evidencia geométrica
    >
WallGraph actual
    >
metadatos previos
```

### Acciones solicitadas

```text
ADD_WALL
REMOVE_WALL
EXTEND_WALL
TRIM_WALL
REPOSITION_WALL
MERGE_WALLS
SPLIT_WALL
```

### También identifica candidatos

- puerta;
- ventana;
- escalera;
- glazing/cancel;
- barandal;
- cubierta;
- columna;
- otros.

### Salida

DELTAS, no reconstrucción completa.

### Estado

**FUNCIONAL / PARCIALMENTE VALIDADO**

Call 2 puede mejorar el WallGraph, pero algunas eliminaciones deben verificarse topológicamente.

### Herramientas

- Gemini
- JSON Schema
- `WallGraphMultimodalReviewer`
- `CALL2_SCHEMA`
- persistencia/replay por hash

---

## DeltaValidator

### Proceso

- Validar IDs.
- Validar confianza.
- Validar coordenadas.
- Validar longitud.
- Validar continuidad.
- Validar merge/split.
- Validar regiones arquitectónicas.

### Salida

- correcciones aceptadas;
- correcciones rechazadas;
- razón de cada decisión.

### Estado

**CUMPLE**

### Herramientas

- geometría matemática
- Pydantic
- `Call2DeltaValidator`

---

## CorrectionApplier + Finalizer

### Proceso

- Aplicar deltas.
- Conservar linaje.
- Reconstruir relaciones.
- Volver a finalizar el WallGraph.

### Salida

`final_wall_graph`

### Estado

**CUMPLE**

### Herramientas

- `WallGraphCorrectionApplier`
- `apply_with_lineage`
- `CanonicalWallGraphFinalizer`
