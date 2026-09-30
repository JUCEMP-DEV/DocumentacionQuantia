# Estado actual — Cumple / Pendiente

[[08_CORRELACIONES|← Anterior]] · [[00_INDICE|Índice]] · [[10_HERRAMIENTAS|Siguiente →]]

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
| Call 2 | FUNCIONAL | Validar más LevelViews |
| DeltaValidator | CUMPLE | Añadir validaciones topológicas globales |
| Linaje de correcciones | CUMPLE | — |
| Cierre lógico de gaps | EXPERIMENTAL | Clasificación del gap |
| Cierre del footprint | EXPERIMENTAL / ALTO | Validar en todos los casos |
| Separación de recintos | PENDIENTE | Recuperar particiones interiores |
| SPACE ↔ polígono | PARCIAL | Mejorar grounding |
| Dimensión por recinto | PENDIENTE | Asociar cotas |
| Área neta interior | PENDIENTE | Considerar espesor |
| Puertas parametrizadas | PENDIENTE | Host + offset + ancho |
| Ventanas parametrizadas | PENDIENTE | Host + offset + ancho |
| Garage door | PENDIENTE | Grounding específico |
| Escalera final | PARCIAL | Geometría/dirección |
| Contrato 04 — muros | CUMPLE | — |
| Contrato 04 — espacios | EXPERIMENTAL | Validación final |
| Contrato 04 — openings | PENDIENTE | Parametrización |
| Readiness cuantificación | PENDIENTE | Alturas + openings + espacios |
