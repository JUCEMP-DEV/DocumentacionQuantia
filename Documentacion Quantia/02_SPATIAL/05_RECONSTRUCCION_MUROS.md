# Reconstrucción y canonicalización de muros

[[04_ESCALA_Y_F02_PERIMETRO|← Anterior]] · [[00_INDICE|Índice]] · [[06_CALL2_Y_VALIDACION|Siguiente →]]

## Adaptive Reconstruction

### Entrada

- F01.5
- F02
- escala
- contexto geométrico

### Proceso

- Crear candidatos de muro.
- Analizar trazos físicos.
- Detectar contexto.
- Construir grafo de candidatos.
- Resolver conflictos.
- Buscar continuidad.
- Resolver topología.
- Producir hipótesis de muro.

### Salida

- `SingleLineWallGraph` preliminar
- candidatos
- relaciones
- diagnósticos

### Estado

**CUMPLE PARCIALMENTE**

Pendiente:
- no todos los muros reales son recuperados;
- pueden sobrevivir líneas no-muro.

### Herramientas

- OpenCV
- NumPy
- Shapely
- Pydantic
- `WallCandidateGenerator`
- `CandidateContextGate`
- `GlobalTopologySolver`
- `WallTrackConsolidator`
- `AdaptiveReconstructionEngine`

---

## PostFilter V4

### Proceso

Clasifica:

```text
WALL
ARCHITECTURAL_ELEMENT
EXCLUDED_GRAPHIC
UNRESOLVED
```

También:
- detecta patrones repetitivos;
- excluye gráficos;
- conserva evidencia descartada;
- genera WallGraph walls-only.

### Estado

**CUMPLE PARCIALMENTE**

Pendiente:
- pueden sobrevivir caras paralelas;
- pueden eliminarse líneas que después resulten necesarias.

---

## Canonical WallGraph

### Objetivo

```text
1 muro físico = 1 centerline
```

### Proceso

- Validar IDs.
- Eliminar referencias inválidas.
- Detectar geometría degenerada.
- Consolidar hipótesis cercanas.
- Fusionar caras paralelas compatibles.
- Reconstruir relaciones.
- Conservar linaje y evidencia.

### Estado

**CUMPLE PARCIALMENTE**

Pendiente:
todavía pueden permanecer caras/paralelos residuales.

### Herramientas

- NumPy
- geometría analítica
- `CanonicalWallGraphFinalizer`
- `ReconstructionEvidenceBundleBuilder`
