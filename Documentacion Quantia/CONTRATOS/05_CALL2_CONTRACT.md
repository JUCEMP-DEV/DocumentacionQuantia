# Contrato 05 — Call 2

[[00_INDICE_CONTRATOS|← Índice de contratos]]

**Versión documental:** 1.0  
**Estado:** FUNCIONAL / PARCIALMENTE VALIDADO  
**Productor:** revisor multimodal diferencial  
**Consumidores:** DeltaValidator, CorrectionApplier y Finalizer

## Entrada
- plano/LevelView original;
- WallGraph actual;
- contexto geométrico y escala;
- identificadores necesarios para referenciar entidades existentes.

## Prioridad de evidencia
`plano original > evidencia geométrica > WallGraph actual > metadatos previos`.

## Salida
Deltas/correcciones sobre el WallGraph; no una reconstrucción completa.

## Operaciones
Las operaciones válidas son las definidas por el schema runtime vigente. La documentación de proceso contempla altas, bajas, ajuste/reemplazo de geometría, merge y split. El schema ejecutable es la fuente de verdad para los nombres exactos.

## Candidatos arquitectónicos
Puede reportar puerta, ventana, garage door, escalera y otras regiones arquitectónicas cuando exista evidencia visual suficiente.

## Validaciones
- contrato/schema válido;
- IDs existentes;
- coordenadas en el sistema declarado;
- confianza y evidencia;
- continuidad y longitud;
- merge/split coherentes;
- openings no deben convertirse automáticamente en muro sólido.

## Persistencia
Conservar prompt, schema, imagen/artefacto visual, respuesta, proveedor/modelo y hashes/replay cuando aplique.

## Compatibilidad
`contract_version` y schema runtime deben mantenerse alineados. Un cambio incompatible requiere nueva versión.
