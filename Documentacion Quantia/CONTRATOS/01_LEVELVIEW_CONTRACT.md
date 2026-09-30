# Contrato 01 — LevelView

[[00_INDICE_CONTRATOS|← Índice de contratos]]

**Versión documental:** 1.0  
**Estado:** VIGENTE  
**Productor:** F01 — identificación de niveles  
**Consumidores:** F01.5, escala/F02 y procesos posteriores que operan por nivel

## Entrada
PDF o imagen fuente del plano.

## Salida mínima
- `LevelView`
- `source_page_number`
- `source_bbox_px`
- transformación página ↔ LevelView
- raster original del nivel

## Coordenadas y unidades
Las coordenadas de recorte y raster se expresan en píxeles. La transformación debe permitir volver a la página fuente. Las unidades métricas no se asumen en F01.

## Validaciones
- cada LevelView debe corresponder a una página y región fuente identificables;
- el recorte no debe perder la transformación con la página;
- el raster asociado debe conservarse como evidencia;
- no se debe inventar un nivel inexistente.

## Linaje
Toda salida debe conservar referencia a documento/página/región fuente.

## Compatibilidad
Cambios de campos, semántica o sistema de coordenadas requieren nueva versión contractual.
