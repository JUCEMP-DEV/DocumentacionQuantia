# F01 — Identificación de niveles

[[01_PRINCIPIOS_Y_FLUJO|← Anterior]] · [[00_INDICE|Índice]] · [[03_F01_5_EVIDENCIA|Siguiente →]]

## Entrada

PDF o imagen del plano.

## Proceso

- Detectar páginas.
- Localizar plantas/niveles.
- Recortar cada planta.
- Conservar transformación respecto a la página fuente.

## Salida

- `LevelView`
- `source_page_number`
- `source_bbox_px`
- transformación página ↔ LevelView
- raster original por nivel

## Estado

**CUMPLE**

## Herramientas

- PyMuPDF
- Pillow
- Gemini
- Pydantic
- `LevelViewBuilder`
- `GeminiLevelLocalizationService`
