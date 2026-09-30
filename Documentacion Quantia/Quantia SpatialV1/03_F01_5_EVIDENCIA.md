# F01.5 — Evidencia multimodal

[[02_F01_NIVELES|← Anterior]] · [[00_INDICE|Índice]] · [[04_ESCALA_Y_F02_PERIMETRO|Siguiente →]]

## Entrada

`LevelView`

## Proceso

- Extraer líneas/vectoriales.
- Detectar geometría raster.
- Extraer texto.
- Incorporar evidencia semántica de Gemini.
- Parametrizar evidencia sin eliminar la fuente original.

## Fuentes

- PyMuPDF
- OpenCV
- OCR
- Gemini

## Salida

- `RawEvidence[]`
- geometría
- texto
- bbox
- tipo de evidencia
- parámetros
- procedencia

## Estado

**CUMPLE**

## Información semántica disponible

Puede identificar:

- espacios;
- nombres de espacios;
- cotas;
- ejes;
- escaleras;
- puertas/ventanas u otras regiones;
- relaciones visuales.

## Limitación actual

Las cotas y los espacios pueden existir como evidencias separadas.

```text
SPACE: Recámara
DIMENSION: 4.00
DIMENSION: 5.00
```

todavía no implica automáticamente:

```text
Recámara = 4.00 × 5.00 = 20.00 m²
```

Falta resolver:

```text
cota ↔ espacio ↔ muros
```

## Herramientas

- PyMuPDF
- OpenCV (`cv2`)
- NumPy
- Tesseract / `pytesseract`
- Pillow
- Gemini
- Pydantic
- `EvidencePipeline`
