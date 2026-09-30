# Quantia SpatialV1 — Índice de documentación

Punto de entrada de la documentación del proceso de regeneración del plano.

## Flujo principal

```text
Documento / Plano
→ F01 — Niveles
→ F01.5 — Evidencia
→ Escala / Raster métrico
→ F02 — Perímetro
→ Reconstrucción de muros
→ PostFilter V4
→ Canonical WallGraph
→ Call 2
→ DeltaValidator
→ Finalizer
→ Cierre de espacios
→ Grounding de elementos
→ Fase 04
```

## Documentos

1. [[01_PRINCIPIOS_Y_FLUJO|Principios y flujo general]]
2. [[02_F01_NIVELES|F01 — Identificación de niveles]]
3. [[03_F01_5_EVIDENCIA|F01.5 — Evidencia multimodal]]
4. [[04_ESCALA_Y_F02_PERIMETRO|Escala y F02 — Perímetro]]
5. [[05_RECONSTRUCCION_MUROS|Reconstrucción y canonicalización de muros]]
6. [[06_CALL2_Y_VALIDACION|Call 2, validación y aplicación de deltas]]
7. [[07_CIERRE_ESPACIOS|Cierre lógico y generación de espacios]]
8. [[08_CORRELACIONES|Correlaciones entre elementos]]
9. [[09_ESTADO_ACTUAL|Estado actual: cumple / pendiente]]
10. [[10_HERRAMIENTAS|Herramientas y librerías]]
11. [[11_CONTRATO_FASE04|Contrato objetivo para Fase 04]]
12. [[12_SIGUIENTE_FASE|Punto actual y siguiente fase]]

## Estado resumido

- Niveles / LevelView: **CUMPLE**
- Evidencia multimodal: **CUMPLE**
- Escala: **CUMPLE**
- F02 perímetro baseline: **CUMPLE**
- Reconstrucción de muros: **PARCIAL**
- Canonical centerlines: **PARCIAL**
- Call 2: **FUNCIONAL / PARCIALMENTE VALIDADO**
- Cierre de footprint: **EXPERIMENTAL / ALTO**
- Separación correcta de recintos: **PENDIENTE**
- Elementos parametrizados para 04: **PENDIENTE**

[[12_SIGUIENTE_FASE|Ir al punto actual]]
