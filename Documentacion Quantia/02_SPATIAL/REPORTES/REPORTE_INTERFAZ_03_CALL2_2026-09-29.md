# Reporte: etapa 03, reconstrucción canónica y segunda llamada al modelo

**Fecha del reporte:** 2026-09-29  
**Proyecto:** QuantiaV2L / quantia_spatialV1  
**Periodo de trabajo documentado:** 2026-09-26 a 2026-09-29  
**Estado:** baseline canónico y pruebas disponibles; Call 2 real validada en un LevelView; integración completa de la ruta visual 03 → SpatialV1 → 04 todavía pendiente.

## 1. Alcance y distinción importante

El trabajo abarcó la reconstrucción posterior a F01.5, la integridad del WallGraph, conservación de evidencia, prueba de Call 2, exportación experimental para 04 y recepción de ese contrato en frontend.

**No se sustituyó completamente el análisis productivo de la pantalla 03.2 por el ejecutor de Call 2 probado.** La existencia del runner y su resultado visual no demuestra que pulsar «Analizar» en 03.2 ejecute actualmente esa misma cadena.

Este reporte complementa [el reporte del motor 04 → 05](../../04_INTEGRACION_04_05/REPORTE_CAMBIOS_MOTOR_INTERFACES_04_05_2026-09-29.md) y debe leerse junto con [Pipeline.md](../../01_PROCESO_GENERAL/PIPELINE_QUANTIA.md). El pipeline contiene objetivos y estados de distintas generaciones; no todos están consolidados en la ruta activa.

## 2. Baseline de reconstrucción conservado

El punto de retorno solicitado fue **Adaptive Reconstruction + PostFilter V4 / walls-only**. Los ajustes de integridad se realizaron después de esa reconstrucción, sin evolucionar F03, Mask o Topology como alternativa para cambiar el baseline visual.

Durante la revisión se detectó que filtrar muros podía dejar referencias y relaciones correspondientes al grafo anterior. Se trabajó en el finalizador canónico para que la salida reflejara los muros efectivamente conservados.

Cambios principales:

- Validación de IDs, referencias, geometrías degeneradas, valores no finitos y duplicados exactos de centerlines, incluso con extremos invertidos.
- Reconstrucción de relaciones y diagnósticos sobre los muros finales.
- Comprobaciones de partición y correspondencia entre decisiones, candidatos y muros canónicos.
- Seguimiento del linaje cuando Call 2 elimina, añade, divide o fusiona muros.
- Finalización antes y después de aplicar correcciones de Call 2.

Versiones utilizadas:

```text
CANONICAL_WALLGRAPH_FINALIZER_V2
RECONSTRUCTION_EVIDENCE_BUNDLE_V2
```

La detección de duplicados exactos y la integridad referencial **no prueban por sí solas** que cada muro físico esté representado correctamente una única vez. Esa correspondencia semántica sigue requiriendo validación visual y geométrica.

## 3. Evidencia conservada y clasificación

Se mantuvo la separación entre:

```text
WALL
ARCHITECTURAL_ELEMENT
EXCLUDED_GRAPHIC
UNRESOLVED
```

Excluir una hipótesis del WallGraph no elimina su evidencia. El bundle conserva evidencia original, candidatos, decisiones de contexto y filtro, mapeos y correcciones.

Las máscaras preservadas se serializan de forma reversible con compresión zlib, base64, dimensiones, tipo de dato y hash. Se conservan también artefactos de junctions y componentes.

Los muros finales incluyen geometría, espesor, confianza, referencias de evidencia, nombres de fuentes, contexto, soporte topológico y linaje. La exportación de revisión añade medidas métricas y segmentos raster para el consumidor de 04.

## 4. Regresión y revisión visual de seis LevelViews

Se comparó la geometría canónica con el baseline V4 guardado, con Call 2 desactivada:

| Caso | Nivel | Muros | Gaps lógicos | Espacios interiores |
|---|---|---:|---:|---:|
| Casa Viri | Planta baja | 49 | 41 | 5 |
| Casa Viri | Planta alta | 28 | 30 | 2 |
| Miguel H | Planta baja | 96 | 79 | 0 |
| Miguel H | Planta alta | 70 | 65 | 2 |
| Miguel V | Planta baja | 34 | 32 | 2 |
| Miguel V | Planta alta | 73 | 49 | 5 |

Se reportaron cero referencias inválidas y coincidencia de geometría con el baseline. El caso Miguel H planta baja, con cero espacios interiores, evidencia que integridad del grafo no equivale a reconstrucción completa de habitaciones.

La validación de esa etapa registró 27 pruebas seleccionadas y 3 pruebas integrales aprobadas. No debe interpretarse como aprobación de toda la suite del repositorio.

Se creó una revisión visual con originales, muros limpios, superposiciones, selector de nivel y controles de visualización:

[Revisión visual de los seis niveles](../../../Backend/app/quantia_spatialV1/tests/output/canonical_visual_review/20260926_140640_572805/index.html).

También se conservaron checkpoints previos y posteriores al finalizador V2, con ZIP de fuentes y manifiestos de hashes, bajo `Backend/app/quantia_spatialV1/documentation/checkpoints/`.

## 5. Depuración y archivo de pruebas antiguas

Por solicitud del usuario se archivaron pruebas y salidas obsoletas, conservando las activas y el baseline.

- Archivo final: 462 archivos, 68,459,029 bytes.
- Destino: `D:\03 INGENIEIRA SISTEMAS\03 RESIDENCIAS PROFESIONALES\REFERENCIAS Y ANEXOS Quantia General\quantia_spatialV1\archivo_pruebas_20260928_092025_final`.
- Incluye manifiesto SHA256 e instrucciones de restauración.
- El primer intento por rutas largas se revirtió antes de completar el archivo final.

La comprobación posterior recogió 134 casos sin errores de colección. Una selección amplia dio 105 aprobados y 3 fallos preexistentes de escala V1.6, presentes antes y después de la limpieza. No se declararon corregidos por este trabajo.

## 6. Revisión del reporte previo de Call 2

Se contrastó el documento proporcionado en Downloads:

`REPORTE_CALL2_PARAMETRIZACION_INTERFAZ04_2026-09-28.md`

SHA256 del original:

```text
c796764482f4acb79b85eec8054cdde595d0b8fdfa1841d6ff948d83eb7b6182
```

El original se conservó. Se creó una revisión en:

`Backend/app/quantia_spatialV1/documentation/REPORTE_CALL2_INTERFAZ04_REVISADO_2026-09-28.md`.

Correcciones conceptuales importantes:

- 04 consume `estructuraEspacial` y segmentos raster para dibujar muros; un contrato exclusivamente métrico no basta.
- Una región propuesta por el modelo no es una puerta, ventana o elemento parametrizado confirmado.
- El conteo de espacios del grafo no sustituye polígonos editables.
- La composición A/B conserva las coordenadas locales, pero no implica que el raster no haya pasado antes por normalización métrica.

## 7. Ejecutor de la segunda llamada

Archivos añadidos:

| Archivo bajo `Backend/app/quantia_spatialV1/tests/` | Función |
|---|---|
| `call2_editor_snapshot.py` | Recuperar snapshot y raster validados, verificando hashes |
| `run_call2_interface04.py` | Preparar petición, smoke, ejecución real o replay y exportación |
| `call2_interface04_export.py` | Aplicar validación/correcciones/finalización y generar paquete para revisión |
| `test_call2_interface04_delivery.py` | Verificar composición multimodal y entrega offline |

La prueba usa el baseline guardado; no vuelve a ejecutar F01 ni toda la extracción documental. Reutiliza el orquestador productivo para validar, aplicar correcciones con linaje y finalizar, sustituyendo las etapas anteriores por el snapshot.

### Entradas de Call 2

- Una imagen PNG compuesta: A, recorte original del LevelView; B, WallGraph walls-only.
- Prompt con estado compacto del grafo y contexto resumido de filtros.
- Coordenadas locales, identificadores y escala.
- Schema de respuesta V2 para deltas, candidatos arquitectónicos y regiones inciertas.

### Proveedor

Se utilizó la configuración del proyecto: **Groq**, modelo **`qwen/qwen3.8-27b`**. No se cambió automáticamente a otro proveedor.

Se identificó una diferencia relevante: la fábrica de proveedores usa Groq por defecto, mientras construir directamente el revisor sin proveedor usa Gemini. El runner inyecta explícitamente el proveedor configurado. No se afirma haber eliminado esa diferencia en todos los consumidores productivos.

Los historiales nuevos se separan por proveedor y hash del nombre del modelo. Se conservan prompt, schema, imagen, respuesta y estado de ejecución.

## 8. Prueba real autorizada y resultado aplicado

Caso: **Casa Viri, planta alta**.  
LevelView: `LEVEL_VIEW_3c23bad8be43edae`.

La ejecución real se realizó después de la autorización explícita del usuario para enviar el recorte y el WallGraph a Groq. El smoke visual pasó. El primer intento de Call 2 recibió un límite temporal HTTP 429; el reintento terminó correctamente.

| Respuesta del modelo | Validación/aplicación |
|---|---|
| Eliminar PW_041, confianza 0.80 | Rechazada: inferior a 0.82 |
| Eliminar PW_087, confianza 0.70 | Rechazada: inferior a 0.82 |
| Región STAIR, confianza 0.80 | Conservada como candidata |
| Región STAIR_HANDRAIL, confianza 0.70 | Conservada como candidata |
| Región incierta, confianza 0.50 | Conservada para revisión |

Resultado final:

- 28 muros, sin cambios geométricos respecto de la entrada.
- 30 gaps lógicos.
- Cero referencias de gap inválidas.
- Cero IDs de muro duplicados.
- Integridad canónica válida.

**La frase del modelo que afirmaba haber eliminado dos muros no describe lo aplicado.** El validador rechazó las dos eliminaciones y conservó ambos muros. Las dos regiones arquitectónicas comparten bbox y requieren revisión semántica.

El archivo `run_status.json` registra `EXPORTED` y modo `LIVE_OR_EXACT_REPLAY`: el runner permite reutilizar una captura idéntica en ejecuciones posteriores. La ejecución aquí descrita obtuvo una respuesta real del proveedor.

## 9. Resultado visual y paquete de entrega

[Abrir comparación visual de Call 2](../../../Backend/app/quantia_spatialV1/tests/output/call2_interface04/groq/401ffb2c754c/casa_viri__02_planta_alta_copia_1/index.html).

La vista compara plano original, baseline y resultado validado. Señala muros finales, eliminaciones rechazadas, candidatos y región incierta.

Carpeta de artefactos:

```text
Backend/app/quantia_spatialV1/tests/output/call2_interface04/
  groq/401ffb2c754c/casa_viri__02_planta_alta_copia_1/
```

Incluye:

```text
original.png                 Raster fuente
call2_input.png              Imagen A/B enviada
prompt.txt / schema.json     Petición reproducible
input_graph.json             Grafo de entrada
review.json                  Respuesta normalizada
validation.json              Aceptaciones y rechazos
final_graph.json             Grafo realmente aplicado
integrity.json               Comprobaciones de integridad
evidence_bundle.json         Evidencia y trazabilidad
interface04.json             Contrato experimental de revisión
run_status.json              Estado y proveedor/modelo
index.html                   Comparación visual
```

El contrato `SPATIAL_INTERFACE04_REVIEW_V1` contiene geometría raster y métrica, espesor, longitud, fuentes, topología y linaje de muros, además de los buckets y candidatos. Marca alturas no disponibles como null y muros no confirmados por el usuario.

Su readiness mantiene `workflowContinuation=false` y `quantification=false`: aún faltan polígonos de espacios, aberturas parametrizadas, alturas y otras definiciones. No se promocionaron candidatos del modelo a elementos confirmados.

## 10. Cambios en recepción 03 → 04

Se añadió `Frontend/src/modules/vivienda/editor/adapters/spatialWorkflowContract.js` y se conectó a la normalización del store.

Esto permite:

- Mantener compatibilidad con el contrato anterior de 03.
- Recibir el paquete experimental de revisión cuando sea entregado al store.
- Conservar evidencia, candidatos, revisión y geometría.
- Asociar muros a claves de nivel.
- Distinguir referencias locales de archivo de URLs utilizables del raster.
- Evitar sustituir un recorte local por una página completa que no coincida con sus coordenadas.

04 muestra el resumen de recepción y evidencia. Para superponer la imagen, el productor debe entregar una URL utilizable del recorte exacto; `original.png` dentro del paquete local no constituye por sí mismo una URL servida por la aplicación.

## 11. Estado real de la pantalla 03 y su backend

La pantalla `03_2AnalisisIAView.vue` sigue utilizando `analizarPlanoDocumento()`, pasando documento, token, página 1 y escala de render, y persiste la respuesta en `estructuraEspacial`.

Al arrancar el backend se encontraron imports rotos de servicios trasladados a `app/legacy/quantia_spatial`. Se corrigieron esas referencias en el endpoint de documentos y en los servicios trasladados para recuperar el arranque.

**Esa reparación mantuvo la ruta legacy; no la convirtió en una ruta SpatialV1/Call 2.** Queda pendiente conectar explícitamente el endpoint de análisis con la cadena validada y demostrar el recorrido completo desde la pantalla.

Tampoco se generalizó en esa pantalla la selección y ejecución de todas las páginas o LevelViews: el código inspeccionado continúa solicitando página 1.

## 12. Pruebas específicas de Call 2

Desde `Backend`:

```powershell
.venv/Scripts/python.exe -B -m pytest app/quantia_spatialV1/tests/test_call2_interface04_delivery.py -q -p no:cacheprovider
.venv/Scripts/python.exe -B -m app.quantia_spatialV1.tests.run_call2_interface04 --offline

# Envía el plano y el grafo al proveedor configurado:
.venv/Scripts/python.exe -B -m app.quantia_spatialV1.tests.run_call2_interface04 --live
```

Se ejecutaron 3 pruebas nuevas y 14 pruebas existentes de contrato, validador y adaptadores de schema: **17 aprobadas** en esa selección.

Las nuevas pruebas cubren conversión raster/métrica, conservación de linaje, rechazo de correcciones de baja confianza, separación de candidatos, rechazo de respuestas de otro nivel y petición multimodal A/B con proveedor sintético y red bloqueada.

La modalidad offline se identifica como respuesta sintética. No demuestra calidad visual o semántica de un proveedor real.

## 13. Pendientes para cerrar 03 → Call 2 → 04

1. Conectar la ruta productiva de análisis de 03 con el orquestador SpatialV1, sin depender de fixtures ni loaders de pruebas.
2. Resolver almacenamiento, recuperación y URLs autenticadas de los recortes locales por LevelView.
3. Gestionar páginas y niveles completos desde 03, incluyendo selección y seguimiento de estado.
4. Definir y validar polígonos de espacios, aberturas y otros elementos parametrizados, conservando incertidumbre.
5. Verificar el tratamiento de unidades de puertas/ventanas en el editor raster.
6. Extender la validación real de Call 2 a más LevelViews; esta prueba real documentada cubre uno, no los seis.
7. Evaluar errores de ubicación y duplicidad de candidatos, no solamente validez del JSON.
8. Demostrar persistencia y consumo real en navegador desde análisis hasta revisión en 04.
9. Mantener explícita la diferencia entre geometría revisable y datos completos para cantidades.

El trabajo deja artefactos reproducibles y una base de recepción, pero no debe presentarse como cierre de la integración productiva ni como reconstrucción semántica completa de todos los elementos del plano.
