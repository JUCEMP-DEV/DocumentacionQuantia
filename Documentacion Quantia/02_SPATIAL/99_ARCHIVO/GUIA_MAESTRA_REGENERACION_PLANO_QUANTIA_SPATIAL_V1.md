# GUÍA MAESTRA — REGENERACIÓN DEL PLANO
## Quantia SpatialV1

**Fecha de corte:** 2026-09-30  
**Objetivo actual:** reconstruir el plano arquitectónico mediante geometría trazable, producir un WallGraph coherente, cerrar espacios y entregar geometría editable a Fase 04.

---

## 1. Principio general

El proceso no intenta redibujar el plano de una sola vez.

Se divide en cuatro capas:

```text
EVIDENCIA
   ↓
RECONSTRUCCIÓN DE MUROS
   ↓
CIERRE Y TOPOLOGÍA DE ESPACIOS
   ↓
ELEMENTOS ARQUITECTÓNICOS + ENTREGA A 04
```

Regla principal:

> La imagen original es la verdad visual.  
> La geometría reconstruida es una hipótesis verificable.

---

# 2. Flujo maestro

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

---

# 3. Proceso por etapa

## F01 — Identificación de niveles

**Entrada:** PDF o imagen del plano.

**Proceso:**
- detectar páginas;
- localizar plantas/niveles;
- recortar cada planta;
- conservar transformación respecto a la página fuente.

**Salida:**
- `LevelView`;
- `source_page_number`;
- `source_bbox_px`;
- transformación página ↔ LevelView;
- raster original por nivel.

**Estado:** CUMPLE.

**Herramientas:**
- PyMuPDF;
- Pillow;
- Gemini;
- Pydantic;
- `LevelViewBuilder`;
- `GeminiLevelLocalizationService`.

---

## F01.5 — Evidencia multimodal

**Entrada:** `LevelView`.

**Proceso:**
- extraer líneas/vectoriales;
- detectar geometría raster;
- extraer texto;
- incorporar evidencia semántica de Gemini;
- parametrizar evidencia sin eliminar la fuente original.

**Fuentes:**
- PyMuPDF;
- OpenCV;
- OCR;
- Gemini.

**Salida:**
- `RawEvidence[]`;
- geometría;
- texto;
- bbox;
- tipo de evidencia;
- parámetros;
- procedencia.

**Estado:** CUMPLE.

**Herramientas:**
- PyMuPDF;
- OpenCV (`cv2`);
- NumPy;
- Tesseract / `pytesseract`;
- Pillow;
- Gemini;
- Pydantic;
- `EvidencePipeline`.

### Información semántica disponible

La primera extracción puede identificar:

- espacios;
- nombres de espacios;
- cotas;
- ejes;
- escaleras;
- puertas/ventanas u otras regiones;
- relaciones visuales.

**Limitación actual:**

Las cotas y los espacios pueden existir como evidencias separadas.

Ejemplo:

```text
SPACE: Recámara
DIMENSION: 4.00
DIMENSION: 5.00
```

todavía no implica automáticamente:

```text
Recámara = 4.00 × 5.00 = 20.00 m²
```

Falta resolver la correlación `cota ↔ espacio ↔ muros`.

---

## Normalización de escala

**Entrada:** evidencia de cotas/ejes + raster.

**Proceso:**
- resolver `px/m`;
- reconciliar escala;
- rerasterizar a densidad métrica estable;
- reproyectar evidencia ya validada.

**Objetivo:**
mantener geometría comparable entre plantas y procesos.

**Salida:**
- `px_per_m`;
- raster métrico canónico;
- transformación métrica.

**Estado:** CUMPLE, sujeto a calidad de evidencia de escala.

**Herramientas:**
- PyMuPDF;
- NumPy;
- OpenCV;
- `ScaleEvidenceResolver`;
- `LevelViewRerasterizer`;
- `PerimeterRasterReprojector`.

---

## F02 — Perímetro base

**Entrada:**
- `LevelView`;
- `RawEvidence`;
- escala.

**Proceso:**
- detectar geometría exterior;
- reconciliar evidencia;
- construir segmentos perimetrales;
- resolver conectividad;
- validar perímetro;
- asociar cotas cuando existe evidencia suficiente.

**Salida:**
- `EditablePerimeterModel`;
- geometría base del perímetro;
- evidencia y trazabilidad.

**Estado:** CUMPLE como baseline.

**Regla:**
F02 se conserva como evidencia histórica.  
Correcciones posteriores no deben sobrescribirlo.

**Herramientas:**
- Shapely;
- OpenCV;
- NumPy;
- Pydantic;
- `BoundaryGeometryDetector`;
- `PerimeterWallGraphBuilder`;
- `PerimeterWallResolver`;
- `PerimeterWallValidator`.

---

## Adaptive Reconstruction — Reconstrucción inicial de muros

**Entrada:**
- F01.5;
- F02;
- escala;
- contexto geométrico.

**Proceso:**
- crear candidatos de muro;
- analizar trazos físicos;
- detectar contexto;
- construir grafo de candidatos;
- resolver conflictos;
- buscar continuidad;
- resolver topología;
- producir hipótesis de muro.

**Salida:**
- `SingleLineWallGraph` preliminar;
- candidatos;
- relaciones;
- diagnósticos.

**Estado:** CUMPLE parcialmente.

**Pendiente:**
no todos los muros reales son recuperados y pueden sobrevivir líneas no-muro.

**Herramientas:**
- OpenCV;
- NumPy;
- Shapely;
- Pydantic;
- `WallCandidateGenerator`;
- `CandidateContextGate`;
- `GlobalTopologySolver`;
- `WallTrackConsolidator`;
- `AdaptiveReconstructionEngine`.

---

## PostFilter V4 — Separación muro / no-muro

**Entrada:** reconstrucción adaptativa.

**Proceso:**
clasificar las hipótesis como:

```text
WALL
ARCHITECTURAL_ELEMENT
EXCLUDED_GRAPHIC
UNRESOLVED
```

También:
- detectar patrones repetitivos;
- excluir gráficos;
- conservar evidencia descartada;
- generar WallGraph walls-only.

**Salida:** WallGraph limpio preliminar.

**Estado:** CUMPLE parcialmente.

**Pendiente:**
pueden sobrevivir caras paralelas o eliminarse líneas que después resulten necesarias.

**Herramientas:**
- reglas geométricas;
- `PostFilterGeometryPatternAnalyzer`;
- `PostFilterSelector`;
- `WallHypothesisCanonicalizer`;
- `PostReconstructionFilterEngine`;
- OpenCV/NumPy para auditoría visual.

---

## Canonical WallGraph — Una geometría por muro físico

**Entrada:** WallGraph filtrado.

**Proceso:**
- validar IDs;
- eliminar referencias inválidas;
- detectar geometría degenerada;
- consolidar hipótesis cercanas;
- fusionar caras paralelas compatibles;
- reconstruir relaciones;
- conservar linaje y evidencia.

**Objetivo:**

```text
1 muro físico = 1 centerline
```

**Salida:** WallGraph canónico.

**Estado:** CUMPLE PARCIALMENTE.

**Pendiente actual:**
todavía pueden permanecer algunas caras/paralelos residuales.

**Herramientas:**
- NumPy;
- geometría analítica;
- `CanonicalWallGraphFinalizer`;
- `ReconstructionEvidenceBundleBuilder`.

---

## CALL 2 — Auditoría visual diferencial

**Entrada visual:**
- plano original;
- WallGraph actual.

**Prioridad:**

```text
Plano original
    >
evidencia geométrica
    >
WallGraph actual
    >
metadatos previos
```

**Proceso solicitado al modelo:**
- eliminar falsos muros;
- agregar muros faltantes;
- extender/recortar muros;
- reposicionar centerlines;
- fusionar o dividir muros;
- detectar elementos no-muro relevantes;
- conservar openings visibles.

**Acciones:**

```text
ADD_WALL
REMOVE_WALL
EXTEND_WALL
TRIM_WALL
REPOSITION_WALL
MERGE_WALLS
SPLIT_WALL
```

**Candidatos semánticos:**
- puerta;
- ventana;
- escalera;
- glazing/cancel;
- barandal;
- cubierta;
- columna;
- otros.

**Salida:** DELTAS, no reconstrucción completa.

**Estado:** FUNCIONAL / PARCIALMENTE VALIDADO.

**Resultado actual importante:**
Call 2 sí puede mejorar el WallGraph, pero algunas eliminaciones deben verificarse topológicamente para no perder muros reales.

**Herramientas:**
- Gemini;
- JSON Schema;
- `WallGraphMultimodalReviewer`;
- `CALL2_SCHEMA`;
- persistencia/replay por hash.

---

## DeltaValidator — Validación determinista

**Entrada:** deltas propuestos por Call 2.

**Proceso:**
- validar IDs;
- validar confianza;
- validar coordenadas;
- validar longitud;
- validar continuidad;
- validar merge/split;
- validar regiones arquitectónicas.

**Salida:**
- correcciones aceptadas;
- correcciones rechazadas;
- razón de cada decisión.

**Estado:** CUMPLE.

**Herramientas:**
- geometría matemática;
- Pydantic;
- `Call2DeltaValidator`.

---

## CorrectionApplier + Finalizer

**Entrada:** correcciones aceptadas.

**Proceso:**
- aplicar deltas;
- conservar linaje;
- reconstruir relaciones;
- volver a finalizar el WallGraph.

**Salida:** `final_wall_graph`.

**Estado:** CUMPLE.

**Herramientas:**
- `WallGraphCorrectionApplier`;
- `apply_with_lineage`;
- `CanonicalWallGraphFinalizer`.

---

# 4. Cierre de espacios sin nueva llamada IA

## Canonicalización residual

**Objetivo:** limpiar caras/paralelos todavía presentes.

**Correlación usada:**

```text
misma orientación
+ separación compatible con espesor
+ solape longitudinal
+ contexto compatible
= mismo muro físico
```

**Estado:** EXPERIMENTAL.

---

## Junction Snap

**Objetivo:** conectar extremos que representan la misma intersección.

Debe priorizar:

```text
intersección geométrica real
→ orientación dominante
→ proyección H/V
→ tolerancia según escala
```

No debe crear diagonales artificiales sólo porque dos extremos estén cerca.

**Estado:** PENDIENTE DE CIERRE.

---

## Logical Gap Closure

Los openings no deben convertirse en muro físico.

Se manejan dos capas:

```text
GEOMETRÍA FÍSICA
muro ─────      ───── muro

GEOMETRÍA TOPOLÓGICA
─────────────────────
```

La segunda existe sólo para cerrar el espacio.

**Estado:** EXPERIMENTAL FUNCIONAL.

---

## Polygonize

**Entrada:**
- centerlines físicas;
- cierres lógicos;
- perímetro.

**Proceso:**
- construir red topológica;
- polygonizar;
- descartar slivers;
- relacionar polígonos con muros.

**Salida:** polígonos candidatos de espacio.

**Estado:** EXPERIMENTAL.

**Herramientas:**
- Shapely;
- `LineString`;
- `unary_union`;
- `snap`;
- `polygonize` / `polygonize_full`.

---

# 5. Validaciones necesarias para cierre

La existencia de un polígono no significa que el espacio sea correcto.

Se deben verificar:

### Conservación del footprint

```text
área cerrada ≈ área arquitectónica base
```

No utilizar automáticamente el área total del terreno si el edificio no ocupa todo el predio.

### Cobertura

```text
suma de espacios válidos / footprint
```

Detecta áreas que todavía quedaron abiertas.

### Balance geométrico

```text
espacios
+ muros
+ patios/vacíos
≈ footprint
```

### Dangles

Medir longitud total de extremos abiertos.

Un proceso de cierre correcto debe reducirlos sin crear conexiones artificiales.

### Protección perimetral

Una eliminación que rompe el perímetro necesita evidencia fuerte o debe regresar a REVIEW.

### Separación semántica

No basta obtener un único polígono grande.

Debe existir correspondencia entre:

```text
espacios semánticos detectados
↔
polígonos geométricos
```

### Estado experimental observado

En Miguel V PB:

```text
Cobertura geométrica aproximada: 99 %
Separación semántica: ~33 %
```

Esto indica:

> El footprint puede cerrarse, pero todavía faltan particiones interiores correctas.

---

# 6. Correlaciones necesarias entre elementos

| Elemento A | Elemento B | Correlación necesaria | Uso |
|---|---|---|---|
| Página | LevelView | `source_bbox_px` + transform | Trazabilidad |
| RawEvidence | LevelView | coordenadas locales | Ubicación |
| Cota | Eje/Muro | proyección + orientación + proximidad | Escala/dimensión |
| Cota | Espacio | bbox + dirección + límites del recinto | Dimensiones del espacio |
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

---

# 7. Estado actual

| Componente | Estado | Pendiente principal |
|---|---|---|
| Identificación de niveles | CUMPLE | Validación adicional en planos atípicos |
| LevelView y coordenadas | CUMPLE | — |
| Evidencia PyMuPDF | CUMPLE | — |
| Evidencia OpenCV | CUMPLE | — |
| OCR | CUMPLE | Calidad depende del plano |
| Evidencia semántica Gemini | CUMPLE | Grounding de algunas medidas |
| Escala / raster métrico | CUMPLE | Casos ambiguos |
| F02 perímetro baseline | CUMPLE | Correcciones sólo como delta |
| Reconstrucción inicial de muros | PARCIAL | Recall de muros interiores |
| PostFilter | PARCIAL | Evitar eliminar geometría útil |
| Centerline canónica | PARCIAL | Paralelos residuales |
| Call 2 | FUNCIONAL | Validar generalización a más LevelViews |
| DeltaValidator | CUMPLE | Añadir validaciones topológicas globales |
| Linaje de correcciones | CUMPLE | — |
| Cierre lógico de gaps | EXPERIMENTAL | Clasificación arquitectónica del gap |
| Cierre del footprint | EXPERIMENTAL / ALTO | Validar en todos los casos |
| Separación de recintos | PENDIENTE | Recuperar particiones interiores |
| Asociación SPACE ↔ polígono | PARCIAL | Mejorar grounding |
| Dimensión por recinto | PENDIENTE | Asociar cotas a espacio |
| Área neta interior | PENDIENTE | Considerar espesor de muro |
| Puertas parametrizadas | PENDIENTE | Host + offset + ancho |
| Ventanas parametrizadas | PENDIENTE | Host + offset + ancho |
| Garage door | PENDIENTE | Tipo/grounding específico |
| Escalera final | PARCIAL | Geometría y dirección final |
| Contrato 04 — muros | CUMPLE | — |
| Contrato 04 — espacios | EXPERIMENTAL | Validación final |
| Contrato 04 — openings | PENDIENTE | Parametrización |
| Readiness cuantificación | PENDIENTE | Alturas + openings + espacios definitivos |

---

# 8. Herramientas y librerías por etapa

| Etapa | Herramientas / librerías |
|---|---|
| Documento / PDF | PyMuPDF |
| LevelView | PyMuPDF, Pillow, Pydantic |
| Localización semántica | Gemini |
| Evidencia vectorial | PyMuPDF |
| Evidencia raster | OpenCV, NumPy |
| OCR | Tesseract, `pytesseract`, Pillow |
| Contratos | Pydantic |
| Escala | PyMuPDF, NumPy, lógica geométrica |
| Perímetro F02 | Shapely, OpenCV, NumPy |
| Candidatos de muro | OpenCV, NumPy, Shapely |
| Topología | Shapely |
| PostFilter | geometría + reglas de contexto |
| Canonical WallGraph | NumPy + geometría analítica |
| Call 2 | Gemini + JSON Schema |
| Transporte API | `requests` / provider Gemini |
| Validación de deltas | Python geométrico + Pydantic |
| Cierre de espacios | Shapely |
| Visualización/auditoría | OpenCV, NumPy |
| Pruebas | pytest |
| Persistencia de evidencia | JSON / JSONL + hashes SHA-256 |

---

# 9. Datos objetivo para Fase 04

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

---

# 10. Punto actual del desarrollo

El problema actual no es únicamente cerrar líneas.

El objetivo inmediato es:

```text
WallGraph final
      ↓
recuperar particiones interiores correctas
      ↓
cerrar cada recinto
      ↓
correlacionar SPACE semántico con polígono
      ↓
asociar cotas y dimensiones
      ↓
parametrizar openings
      ↓
entregar geometría completa a Fase 04
```

La siguiente fase debe priorizar:

1. validación topológica de muros eliminados;
2. recuperación de particiones interiores;
3. canonicalización residual;
4. junction snap correcto;
5. correlación `SPACE ↔ polygon`;
6. correlación `DIMENSION ↔ SPACE/WALL`;
7. grounding de puertas/ventanas.

---

# 11. Regla de cierre

Una etapa no se considera cerrada porque el test marque `PASSED`.

Se considera cerrada cuando:

```text
resultado geométrico
+
resultado visual
+
consistencia topológica
+
trazabilidad
+
contrato de salida
```

son coherentes con el plano original.
