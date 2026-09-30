# Contrato 02 — Evidencia F01.5

[[00_INDICE_CONTRATOS|← Índice de contratos]]

**Versión documental:** 1.0  
**Estado:** VIGENTE  
**Productor:** F01.5 — evidencia multimodal  
**Consumidores:** escala, F02, reconstrucción de muros y correlaciones semánticas

## Entrada
`LevelView` válido.

## Salida
Colección `RawEvidence[]` con geometría, texto, bbox, tipo, parámetros y procedencia.

## Fuentes admitidas
PyMuPDF, OpenCV, OCR y evidencia semántica multimodal.

## Coordenadas y unidades
La evidencia geométrica debe indicar el sistema de coordenadas del LevelView. Las magnitudes métricas sólo son válidas cuando existe resolución de escala trazable.

## Validaciones
- cada evidencia debe conservar su procedencia;
- no eliminar la evidencia original al parametrizarla;
- distinguir texto, geometría y evidencia semántica;
- una asociación semántica no se considera resuelta sólo por proximidad;
- cotas y espacios pueden permanecer como evidencias independientes hasta reconciliación.

## Linaje
`source → LevelView → RawEvidence`.

## Compatibilidad
El consumidor no debe asumir relaciones `DIMENSION ↔ SPACE/WALL` mientras no hayan sido reconciliadas explícitamente.
