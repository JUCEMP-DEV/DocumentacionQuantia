# Punto actual y siguiente fase

[[11_CONTRATO_FASE04|← Anterior]] · [[00_INDICE|Índice]]

## Punto actual

El problema ya no es únicamente cerrar líneas.

El footprint puede cerrarse casi completamente, pero los espacios interiores todavía no quedan correctamente separados.

## Objetivo inmediato

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

## Prioridad

1. Validación topológica de muros eliminados.
2. Recuperación de particiones interiores.
3. Canonicalización residual.
4. Junction snap correcto.
5. Correlación `SPACE ↔ polygon`.
6. Correlación `DIMENSION ↔ SPACE/WALL`.
7. Grounding de puertas/ventanas.

## Regla

No integrar un proceso experimental al motor hasta validar:

```text
geometría
+ topología
+ evidencia visual
+ trazabilidad
+ contrato de salida
```
