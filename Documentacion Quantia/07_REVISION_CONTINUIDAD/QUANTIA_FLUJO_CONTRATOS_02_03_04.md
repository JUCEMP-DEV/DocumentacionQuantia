# QUANTIA V2L — Flujo 02→03→04 e integración con QuantiaSpatialV1

**Fecha:** 2026-10-01  
**Baseline revisado:** QuantiaV2L `de634dd544ef103abcfc7dab64c37a5f020d57a0` · QuantiaSpatialV1 `b8177a3e9420789fde97e65c2799fc602f192c19`

> QuantiaV2L es el consumidor principal. QuantiaSpatialV1 es un subproceso que propone geometría; no gobierna el flujo completo.

## 1. Decisión principal

La interfaz **03.2 debe quedar reducida a interacción y estado**:

- iniciar/reintentar Spatial;
- informar progreso/estado;
- mostrar resultado resumido;
- conservar qué documento se analizó;
- permitir avanzar a 04 cuando la ejecución termine.

03.2 **no debe decidir** rasterización, escala, OCR interno, F01/F01.5/F02, reconstrucción, PostFilter, WallGraph o Call 2.

El flujo debe poder continuar aunque Spatial termine `COMPLETED`, `PARTIAL` o `FAILED`. La corrección/completado corresponde a 04.

## 2. Panorama completo

```text
01 Proyecto / Alcance
        ↓
02 Cómo se construirá
        ↓
   ┌────┴─────────────┐
   │                  │
SUBIR PLANO        DIBUJAR
   │                  │
03.1 Carga          04 Manual
   │                  │
03.2 Estado           │
   │                  │
SpatialV1             │
   │                  │
04 Desde plano ───────┘
        ↓
MODELO 04 CANÓNICO
        ↓
05 Cálculo de cantidades
        ↓
06 Presupuesto
```

Las dos vías deben converger en **el mismo modelo espacial de 04**.


## 3. Estado verificado de QuantiaV2L

### 01ProyectoAlcanceView.vue

Ya persiste:

- proyecto;
- ubicación: estado, municipio, localidad, dirección;
- tipo de intervención;
- alcance/modalidad;
- partidas/módulos aplicables.

No se debe enviar a Spatial información personal de cliente/prestador que no aporta al análisis.

### 02ComoSeConstruiraView.vue

Actualmente captura:

- topografía;
- desnivel/profundidad;
- acceso;
- condición de terreno;
- sistema estructural;
- cimentación;
- tipo de losa;
- modo de diseño;
- servicios;
- demolición cuando aplica.

**Pendiente:** el Vue del repositorio aún no captura:

- `anchoTerrenoM`;
- `largoTerrenoM`;
- `areaTerrenoM2`.

Los campos ya existen en `viviendaStore.datosGeneralesObra`, por lo que la modificación necesaria es de captura/persistencia en 02.

### viviendaStore.js

El store ya contiene `registro`, `clasificacion`, `alcance`, `preliminares`, `datosGeneralesObra`, `estructuraEspacial`, `validacionEspacial`, módulos y resultado.

Además, cuando cambian datos estructurales de `datosGeneralesObra`, invalida `estructuraEspacial` y estados posteriores. Esa regla debe conservarse.

## 4. Problema actual de 03.2

`03_2AnalisisIAView.vue` mezcla interfaz con lógica del subproceso:

- `MIGUEL_H_FILE_NAME`;
- `MIGUEL_H_RENDER_SCALE`;
- `resolvePdfRenderScale()`;
- `pdfRenderScale`;
- `ocrStrategy`;
- `procesarDocumento()`/RAG como requisito previo;
- ejecución Spatial;
- persistencia;
- gate de navegación.

Además, el botón Continuar depende de `spatialReady`. Si Spatial falla o queda incompleto, el flujo puede quedar bloqueado antes de 04.

Eso debe cambiar: **Spatial debe terminar su intento; 04 debe resolver el resto.**

## Resolución que se esta implementando
			                    03.2 ANÁLISIS ESPACIAL
			
			              ┌───────────────────────┐
			              │         94%           │
			              │ ÉXITO RECONSTRUCCIÓN │
			              └───────────────────────┘
			
			AVANCE DE EJECUCIÓN
			████████████████████████████ 100%
			Análisis terminado
			
			ESCALA Y MÉTRICA        ████████████████████ 100%  ✓
			PERÍMETRO               ███████████████████░  96%  ✓
			MUROS                    ██████████████████░░  91%  Revisión
			ESPACIOS                 █████████████████░░░  86%  Revisión
			ABERTURAS                ███████████████░░░░░  76%  Pendiente
			CORRELACIÓN GLOBAL       ██████████████████░░  92%  ✓
			
			Estado Spatial: REVIEW
			2 elementos requieren revisión en Diseño de la vivienda
			
			                    [ Continuar a 04 ]
La separación que debemos fijar en el contrato es:
		execution
		├─ status
		│  ├─ IDLE
		│  ├─ RUNNING
		│  ├─ COMPLETED
		│  ├─ PARTIAL
		│  └─ FAILED
		│
		└─ progressPercent       ← cuánto ha avanzado la ejecución
		
		quality
		├─ successPercent        ← qué tan correcta/completa quedó la reconstrucción
		│
		└─ components
		   ├─ scale
		   ├─ perimeter
		   ├─ walls
		   ├─ spaces
		   ├─ openings
		   └─ correlation

# 5. Contrato 1 — 02→03

## Nombre

`QUANTIA_02_03_SPATIAL_CONTEXT_V1`

Aunque la frontera es 02→03, toma datos generados en 01 y 02.

**Propietario:** QuantiaV2L.

Spatial no debe leer directamente el store Vue.

## Objetivo

Entregar a Spatial solo información previamente declarada por el usuario que sirva para:

- validar observaciones;
- detectar contradicciones;
- apoyar interpretación;
- conservar contexto.

Nunca debe forzar la geometría observada para hacerla coincidir con los datos declarados.

## Estructura propuesta

```text
QUANTIA_02_03_SPATIAL_CONTEXT_V1
├─ schemaVersion
├─ source = USER_DECLARED
├─ projectRevision
├─ project
│  ├─ location
│  ├─ interventionType
│  └─ scope
├─ site
│  ├─ widthM
│  ├─ lengthM
│  ├─ areaM2
│  ├─ topography
│  ├─ slopeOrDepthM
│  ├─ terrainCondition
│  └─ accessType
├─ construction
│  ├─ structuralSystem
│  ├─ foundationType
│  └─ slabType
├─ services
│  ├─ water
│  ├─ drainage
│  ├─ electricity
│  ├─ gas
│  └─ telecommunications
├─ existingConstruction
│  └─ demolition data when applicable
└─ provenance
```

## Reglas

No pasar directamente:

- `engineInputs` completo;
- `engineInputsByConcept` completo;
- cliente/prestador;
- teléfono/correo;
- defaults no declarados;
- campos posteriores a 03;
- derivados presentados como `USER_DECLARED`.

Caso crítico: `factorAjuste = 1` existe por defecto y no debe convertir un contexto vacío en “dato declarado”.

## Adaptación a Spatial

SpatialV1 ya posee `models/project_site_context.py`.

La frontera debe ser:

```text
store QuantiaV2L
      ↓
QUANTIA_02_03_SPATIAL_CONTEXT_V1
      ↓
SpatialInputAdapter
      ↓
PROJECT_SITE_CONTEXT_V1
      ↓
QuantiaSpatialV1
```

`tipoLosa`, topografía, acceso, condición del terreno y servicios deben clasificarse explícitamente:

- si ayudan a Spatial → ampliar su contrato interno;
- si solo son de cuantificación → conservarlos en QuantiaV2L, sin forzarlos dentro de `ProjectSiteContext`.

La relación de evidencia debe ser:

```text
USER_DECLARED + DOCUMENT_OBSERVED + GEOMETRY_OBSERVED
                         ↓
               MATCH / CONFLICT / UNKNOWN
```

Terreno declarado, footprint PB y footprint PA son entidades diferentes.


# 6. Estado real de QuantiaSpatialV1

## Motor F01→F02

`QuantiaSpatialEngine` ya realiza:

```text
documento
  ↓
F01 LevelViews
  ↓
F01.5 evidencia
  ↓
F02 perímetro
  ↓
ScaleEvidenceResolver
  ↓
RasterDensityPolicy
  ↓
Canonical Metric Raster
```

La escala final se calcula con evidencia del documento. Por tanto `pdf_render_scale` no es decisión de 03.2.

## Reconstrucción posterior

`QuantiaSpatialV1ProcessEngine` realiza:

```text
F01.5/F02
   ↓
Adaptive Reconstruction
   ↓
PostFilter
   ↓
Canonical WallGraph
   ↓
Call 2 cuando corresponde
   ↓
final_wall_graph
```

## Brecha actual

Aún falta un orquestador productivo único que una:

```text
QuantiaSpatialEngine
+
QuantiaSpatialV1ProcessEngine
+
salida consumible por QuantiaV2L 04
```

`tests/call2_interface04_export.py` es experimental. Actualmente puede exportar muros, pero todavía entrega vacíos `puertas`, `ventanas`, `espacios`, `ejes`, `cotas` y marca `workflowContinuation=false` y `quantification=false`.

No debe copiarse directamente como integración productiva.


# 7. Contrato 2 — 03→04

## Nombre recomendado

`QUANTIA_03_04_V2`

El `QUANTIA_03_04_V1` actual tiene otra semántica. El cambio debe ser versionado y no mezclar V1/V2 en el mismo flujo.

**Propietario:** QuantiaV2L.

Spatial conserva sus contratos internos; un adaptador de QuantiaV2L transforma su resultado.

```text
SpatialV1 internal result
        ↓
SpatialDeliveryAdapter
        ↓
QUANTIA_03_04_V2
        ↓
04
```

## Estructura

```text
QUANTIA_03_04_V2
├─ execution
│  ├─ runId
│  ├─ status = COMPLETED | PARTIAL | FAILED
│  ├─ spatialStatus = VALID | REVIEW | INVALID | UNRESOLVED
│  ├─ engineVersion
│  ├─ warnings[]
│  └─ errors[]
├─ source
│  ├─ documentId
│  ├─ fileName
│  ├─ page/pages
│  ├─ levelViewIds
│  └─ sourceHash
├─ projectContextSnapshot
│  ├─ schemaVersion
│  ├─ revision/hash
│  └─ data
├─ spatialModel
├─ evidence
├─ assets
└─ readiness
```

## spatialModel

Debe llevar todo lo disponible, sin inventar faltantes:

```text
terreno/site
niveles
footprints
espacios/regiones
muros
centerlines
ejes
cotas
puertas
ventanas
escaleras
vacíos
proyecciones
relaciones
gaps/openings
completeness
```

Cada entidad debe conservar, cuando aplique:

```text
id
levelId
geometría raster
geometría métrica
estado
confirmed
confidence
evidence/provenance
relaciones
```

## evidence

Debe conservar:

```text
conflicts
unresolved
candidates
excludedGraphics
logicalGaps
sourceMapping
```

04 necesita saber qué revisar; no basta con la geometría final.


# 8. Raster para 04

Actualmente 04 intenta reconstruir el plano base mediante:

```text
documentId + pageNumber + sourcePdfRenderScale
```

Esto vuelve a acoplar frontend con rasterización.

Debe cambiar a:

```text
assets.canonicalRaster
├─ assetId / endpoint
├─ levelViewId
├─ widthPx
├─ heightPx
└─ sha256
```

04 recupera **el mismo raster canónico producido por Spatial**.

04 no vuelve a calcularlo y no necesita conocer `render_scale`.

# 9. Resultado Spatial y navegación

03.2 debe permitir continuar cuando el intento llega a un estado terminal:

```text
COMPLETED
PARTIAL
FAILED
```

Comportamiento:

```text
COMPLETED → 04 carga propuesta completa/revisable
PARTIAL   → 04 carga lo válido + pendientes
FAILED    → 04 abre editor común con contexto 01–02
```

Una falla de Spatial no debe bloquear el proyecto Quantia.

El gate final de 03.2 debe depender de:

```text
documento seleccionado
+
ejecución Spatial terminal
+
ninguna petición activa
```

No de `spatialReady == true`.


# 10. Convergencia de las dos vías en 04

```text
VÍA PLANO
01 → 02 → 03 → Spatial → QUANTIA_03_04_V2 → 04

VÍA MANUAL
01 → 02 → ProjectConfigAdapter → 04
```

Ambas deben terminar en el mismo `EditorState/estructuraEspacial`.

El origen puede ser:

```text
sourceMode = plan
```

o:

```text
sourceMode = manual
```

pero no deben existir dos modelos espaciales diferentes.

Esto ya está parcialmente encaminado con:

- `04DisenoViviendaView.vue`;
- `04DisenoManualView.vue`;
- `Workflow04Bridge.vue`;
- `viviendaStoreAdapter.js`;
- `projectConfigAdapter.js`.

# 11. Autoridad de datos

Debe quedar fijo:

```text
01/02 = autoridad de configuración del proyecto
Spatial = autoridad de observación/propuesta automática
04 = autoridad del modelo espacial revisado por usuario
05 = consumidor de geometría confirmada + condiciones constructivas
```

Spatial no debe sobrescribir silenciosamente:

- intervención;
- alcance;
- sistema estructural;
- cimentación;
- tipo de losa;
- servicios;
- dimensiones declaradas de terreno.

Si encuentra contradicción: `CONFLICT`.


# 12. Relación con el motor de cuantificación

QuantiaV2L ya posee `QUANTIA_04_05_V1`, `spatial_consumption.py` y `spatial_quantity_inputs.py`.

05 consume dos familias.

## Geometría confirmada de 04

Entre otros:

- niveles;
- polígonos de espacios;
- áreas;
- muros;
- longitudes/ejes;
- función estructural;
- puertas/ventanas;
- host wall;
- ancho/alto de vanos;
- escaleras;
- acabados;
- completitud;
- IDs y relaciones.

## Condiciones de 01–02

Actualmente intervienen directamente:

```text
tipoIntervencion
alcanceProyecto
sistemaEstructural
tipoCimentacion
tipoLosa
serviciosInstalaciones
```

Por eso 01–02 no deben “desaparecer” al entrar a Spatial.

El contrato 03→04 debe conservar el snapshot que Spatial utilizó, pero el store actual de QuantiaV2L sigue siendo autoridad.

No hace falta crear ahora un tercer contrato nuevo: `QUANTIA_04_05_V1` debe usarse como objetivo de aceptación.


# 13. Qué debe eliminarse de 03.2

Después de validar el nuevo adaptador:

```text
MIGUEL_H_FILE_NAME
MIGUEL_H_RENDER_SCALE
normalizedFileName()
resolvePdfRenderScale()
pdfRenderScale como responsabilidad del Vue
ocrStrategy como decisión del Vue
```

También debe eliminarse la dependencia:

```text
procesarDocumento/RAG → requisito para ejecutar Spatial
```

SpatialV1 procesa el archivo original y posee su propia extracción de evidencia. RAG puede seguir existiendo para otras funciones documentales, pero no debe gobernar Spatial.

03.2 debe conservar solamente:

```text
listar/seleccionar documento
iniciar análisis
estado
reintentar
error/resumen
continuar a 04
```

# 14. Backend de integración

Crear un servicio de integración de QuantiaV2L, por ejemplo:

```text
QuantiaSpatialIntegrationService
```

Responsabilidades:

```text
obtener archivo autorizado
↓
recibir QUANTIA_02_03_SPATIAL_CONTEXT_V1
↓
adaptar a ProjectSiteContext
↓
ejecutar QuantiaSpatialEngine
↓
ejecutar QuantiaSpatialV1ProcessEngine
↓
recoger resultados parciales
↓
generar QUANTIA_03_04_V2
```

El router `documentos.py` solo debe:

```text
validar request
autorizar documento
invocar integración
devolver contrato
```

No debe contener lógica de Spatial.


# 15. Migración del legacy

Actualmente `/api/documentos/{id}/analizar-plano` todavía usa `app.legacy.quantia_spatial`.

No eliminarlo primero.

Orden seguro:

```text
1. construir integración SpatialV1
2. probarla aislada
3. validar contrato 02→03
4. validar contrato 03→04
5. cambiar endpoint a SpatialV1
6. probar QuantiaV2L 03.2→04
7. probar 04→05
8. retirar imports/factory legacy de documentos.py
9. retirar lógica obsoleta del frontend
10. después evaluar eliminación física del legacy
```

# 16. Contrato de extracción Spatial aún pendiente

Antes de considerar cerrado el subproceso Spatial sigue pendiente fortalecer la primera extracción con:

```text
PROPERTY
EXTERIOR_CONTEXT
LEVEL_FOOTPRINTS
SPATIAL_REGIONS
PROJECTIONS / EXCEPTIONS
region_type
coverage
site_relation
```

Esto mejora la calidad de Spatial, pero no debe impedir que la frontera 03→04 pueda representar `PARTIAL`, `FAILED`, `UNKNOWN` y `REVIEW`.


# 17. Procedimiento de implementación

## Fase A — cerrar 02

1. Añadir inputs de ancho, largo y superficie de terreno.
2. Persistir en `datosGeneralesObra`.
3. No presentar área calculada como dato declarado.
4. Verificar invalidación downstream.

## Fase B — contrato 02→03

1. Crear mapper puro/testeable en QuantiaV2L.
2. Entrada: `registro + clasificacion + alcance + preliminares + datosGeneralesObra`.
3. Aplicar whitelist.
4. Añadir procedencia.
5. Excluir defaults falsos y PII.
6. Adaptar a `ProjectSiteContext`.

## Fase C — integración productiva SpatialV1

1. Crear servicio de integración.
2. Unir engine F01→F02 y process engine.
3. No duplicar algoritmos existentes.
4. Devolver siempre un resultado terminal controlado.

## Fase D — contrato 03→04

1. Crear `SpatialDeliveryAdapter`.
2. Transformar modelos internos Spatial a modelo consumible por 04.
3. Conservar evidencia, conflictos y activos.
4. Permitir `COMPLETED/PARTIAL/FAILED`.

## Fase E — simplificar 03.2

1. Retirar lógica de escala/Miguel H.
2. Retirar parámetros internos.
3. Separar RAG de Spatial.
4. Cambiar gate de navegación a estado terminal.

## Fase F — converger en 04

Verificar que ruta plano y ruta manual terminen en el mismo `EditorState/estructuraEspacial`.

04 debe poder:

- importar propuesta completa;
- importar propuesta parcial;
- abrir vacío si Spatial falla;
- completar/corregir;
- confirmar el modelo.

## Fase G — validar contra 05

Usar `QUANTIA_04_05_V1` como consumidor de referencia y comprobar geometría + condiciones constructivas.


# 18. Pruebas mínimas

## Contrato 02→03

```text
contexto completo
contexto parcial
sin dimensiones
dimensiones de terreno
defaults vacíos
factorAjuste default
cimentación por definir
servicios desconocidos
```

## Spatial

Mantener regresión con casos históricos y ejecutar Miguel V PB/PA.

## Contrato 03→04

Obligatorias:

```text
COMPLETED → 04 abre propuesta
PARTIAL   → 04 abre propuesta + pendientes
FAILED    → 04 abre editor manual
```

## Convergencia 04

Un modelo proveniente de Spatial y uno dibujado manualmente deben terminar usando el mismo contrato interno.

## 04→05

Verificar que el motor:

- recibe geometría confirmada;
- recibe contexto 01–02;
- deriva cantidades;
- bloquea solo información realmente faltante;
- no inventa dimensiones.


# 19. Archivos principales

## QuantiaV2L

```text
Frontend/src/modules/vivienda/views/workflow/
├─ 01ProyectoAlcanceView.vue
├─ 02ComoSeConstruiraView.vue
├─ 03_1CargaDocumentosView.vue
├─ 03_2AnalisisIAView.vue
├─ 04DisenoViviendaView.vue
├─ 04DisenoManualView.vue
└─ 05CalculoCantidadesView.vue

Frontend/src/modules/vivienda/
├─ store/viviendaStore.js
├─ services/documentosApiService.js
├─ services/motorApiService.js
└─ editor/adapters/
   ├─ spatialWorkflowContract.js
   ├─ viviendaStoreAdapter.js
   └─ projectConfigAdapter.js

Backend/app/api/v1/endpoints/
├─ documentos.py
└─ motor.py

Backend/app/services/
├─ spatial_consumption.py
└─ spatial_quantity_inputs.py
```

## QuantiaSpatialV1

```text
models/project_site_context.py
engine.py
process_engine.py
contracts/spatial_contract.py
reconstruction_core/scale_evidence_resolver.py
reconstruction_core/raster_density_policy.py
tests/call2_interface04_export.py
```


# 20. Criterio de cierre

La integración se considera cerrada cuando:

1. 02 captura dimensiones declaradas de terreno.
2. Existe un único mapper 01–02→Spatial.
3. 03.2 no contiene lógica interna de Spatial.
4. 03.2 no decide `render_scale`.
5. SpatialV1 se ejecuta mediante integración productiva, no legacy.
6. La ejecución retorna `COMPLETED/PARTIAL/FAILED`.
7. Los tres estados pueden llegar a 04.
8. 04 consume el raster canónico ya generado.
9. 03→04 entrega geometría + evidencia + pendientes + snapshot de contexto.
10. Plano y manual convergen en el mismo modelo de 04.
11. 01–02 conservan autoridad sobre configuración.
12. 05 recibe modelo confirmado + condiciones constructivas.
13. Solo entonces se retiran componentes legacy y excepciones de pruebas.

## Secuencia final

```text
cerrar datos de 02
      ↓
QUANTIA_02_03_SPATIAL_CONTEXT_V1
      ↓
integración QuantiaV2L ↔ SpatialV1
      ↓
QUANTIA_03_04_V2
      ↓
simplificar 03.2
      ↓
convergencia de ambas vías en 04
      ↓
validar QUANTIA_04_05_V1
      ↓
retirar legacy
```

El error de `pdf_render_scale` es un síntoma de integración desactualizada; no es el problema central. La prioridad es cerrar correctamente estas dos fronteras sin convertir SpatialV1 en el controlador del flujo general de QuantiaV2L.
