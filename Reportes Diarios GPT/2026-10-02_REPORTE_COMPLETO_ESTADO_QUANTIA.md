# Reporte Diario GPT — Estado integral Quantia

**Fecha:** 2026-10-02  
**Proyecto:** Quantia V2L / QuantiaSpatialV1 / DocumentacionQuantia  
**Tipo:** reporte de continuidad, modificaciones, errores, evidencia y pendientes  
**Estado:** ACTIVO — previo a auditoría test → motor

---

## 1. Referencias exactas de trabajo

### QuantiaSpatialV1
- Repositorio: `JUCEMP-DEV/QuantiaSpatialV1`
- `main`: `93db041a8d8dda0e7a50dfd7685bc34c6b157061`
- Punto anterior: `b8177a3e9420789fde97e65c2799fc602f192c19`
- Commit de checkpoint: `checkpoint: estado local Spatial previo a auditoria test-motor`

### QuantiaV2L
- Repositorio: `JUCEMP-DEV/QuantiaV2L`
- `main`: `5db89a2d047dd2c194a72512b500469236f7f083`
- Punto anterior: `de634dd544ef103abcfc7dab64c37a5f020d57a0`
- Commit de checkpoint: `checkpoint: estado local V2L previo a auditoria integracion Spatial`

### DocumentacionQuantia
- Repositorio: `JUCEMP-DEV/DocumentacionQuantia`
- `main` verificado antes de este reporte: `7c1ed44381e2bd99d0832cf2daadcbef22d758b6`
- Base remota previa a la reconciliación: `043680618670ef7a3f6ca1e92673fef7aa071fe8`
- Los 2 commits locales fueron rebasados correctamente sobre `origin/main`.
- Punto de retorno local creado: `checkpoint/docs-pre-sync-2026-10-02` → `3a7a4d51176b7eebfd2bfa117a2e34dc91bc6728`.

---

## 2. Estado ejecutivo actual

El flujo productivo QuantiaV2L → QuantiaSpatialV1 ya está conectado a nivel de endpoint, servicio de integración y UI 03.2. La interfaz 03.2 fue desacoplada de decisiones internas como `pdf_render_scale`, OCR, fases del motor y escala arquitectónica.

Sin embargo, la ejecución integrada real con `PlantaBaja Miguel V.pdf` continúa terminando en:

- `execution.status = FAILED`
- `spatialStatus = UNRESOLVED`
- `successPercent = null`
- sin `LevelViews`
- sin geometría publicable.

La evidencia posterior demuestra que F01.5, F02 y la reconstrucción posterior sí funcionan con el mismo documento cuando el runner aporta previamente la semántica de nivel que históricamente estaba dentro de los tests/harnesses.

La causa raíz que debe auditarse ahora no es simplemente “Gemini falló”, sino la diferencia entre:

1. lo que el motor recibe en producción;
2. lo que los tests históricos aportaban como bootstrap;
3. qué parte de ese bootstrap era fixture específico;
4. qué parte era lógica general de runtime que nunca migró al motor.

Esta auditoría es el siguiente punto de trabajo prioritario.

---

# 3. Modificaciones realizadas

## 3.1 QuantiaV2L — Backend

Entre `de634dd...` y `5db89a2...` se modificaron/agregaron principalmente:

- `Backend/app/api/v1/endpoints/documentos.py`
- `Backend/app/services/quantia_spatial_integration_service.py`

### Nuevo servicio de integración Spatial

Se agregó `QuantiaSpatialIntegrationService`, que actualmente:

- recibe bytes reales del documento;
- valida MIME PDF/JPG/PNG;
- usa `QuantiaSpatialEngine`;
- ejecuta F01 → F01.5 → F02;
- normaliza raster métrico;
- crea `ProjectLevelScaleNormalizer`;
- ejecuta `QuantiaSpatialV1ProcessEngine.run_level()`;
- usa `call2_mode="AUTO"`;
- genera una entrega `SPATIAL_INTERFACE04_REVIEW_V1`;
- separa `execution` de `quality`;
- no inventa un `successPercent`;
- publica warnings/errors;
- conserva buckets, Call2 candidates, unresolved y logical gaps;
- entrega metadatos de origen y estado de raster métrico.

Constantes actualmente introducidas en esta frontera:

```text
PDF_BOOTSTRAP_RENDER_SCALE = 0.7935
TARGET_GEOMETRY_PX_PER_M = 90.0
```

Estas constantes deben ser auditadas porque el objetivo arquitectónico es que las decisiones internas de raster/escala pertenezcan al motor y no terminen convertidas en reglas permanentes de integración.

### Endpoint `analizar-plano`

El endpoint documental ahora invoca `QuantiaSpatialIntegrationService.analyze()` con:

- bytes reales;
- MIME;
- document ID;
- file name;
- `project_site_context=None`.

Por tanto la conexión técnica existe, pero el contrato `QUANTIA_02_03_SPATIAL_CONTEXT_V1` todavía no está conectado a producción.

### Comportamiento de salida

Si no existen `LevelViews`, el servicio devuelve una entrega terminal `FAILED`, no una excepción de transporte.

Si F01/F02 produce nivel pero no WallGraph publicable, devuelve `PARTIAL`.

Si existen muros, actualmente la entrega queda `PARTIAL/REVIEW`, porque todavía faltan contratos cerrados de espacios, openings, ejes/cotas y correlación global.

---

## 3.2 QuantiaV2L — Frontend

Entre `de634dd...` y `5db89a2...` se modificaron:

- `Frontend/src/modules/vivienda/services/documentosApiService.js`
- `Frontend/src/modules/vivienda/views/workflow/02ComoSeConstruiraView.vue`
- `Frontend/src/modules/vivienda/views/workflow/03_1CargaDocumentosView.vue`
- `Frontend/src/modules/vivienda/views/workflow/03_2AnalisisIAView.vue`

### Fase 02

La versión actual sí captura y persiste:

- `anchoTerrenoM`;
- `largoTerrenoM`;
- `areaTerrenoM2`;
- topografía;
- sistema estructural;
- tipo de cimentación;
- tipo de losa;
- modo de diseño.

Esto corrige una afirmación antigua de la documentación que todavía indicaba que ancho/largo/área del terreno estaban pendientes.

### 03.1 Carga de documentos

La vista quedó centrada en:

- subir documentos;
- listar documentos;
- eliminar documentos;
- continuar a 03.2.

La carga documental permanece separada del análisis espacial.

### 03.2 Análisis IA

La vista fue ampliamente reconstruida.

Cambios vigentes:

- ya no decide `pdf_render_scale`;
- ya no decide estrategia OCR;
- ya no decide escala arquitectónica;
- ya no controla fases internas de Spatial;
- llama directamente a `analizarPlanoDocumento()`;
- maneja estados `IDLE / RUNNING / COMPLETED / PARTIAL / FAILED`;
- separa progreso de ejecución y calidad;
- conserva `spatialStatus`;
- muestra warnings/errors;
- muestra componentes de calidad:
  - scale;
  - perimeter;
  - walls;
  - spaces;
  - openings;
  - correlation;
- conserva `successPercent=null` cuando el backend no tiene una métrica real;
- permite que `FAILED` entre al editor común/manual;
- permite que `PARTIAL` también use editor común cuando no existe geometría espacial suficiente;
- evita conservar geometría vieja cuando una nueva ejecución falla.

### Persistencia temporal 03 → 04

Todavía se usa temporalmente:

`buildPhase03Delivery(...)`

sobre el boundary vigente de V1.

El propio código indica que esto debe sustituirse cuando `spatialWorkflowContract.js` publique el receptor `QUANTIA_03_04_V2`.

---

## 3.3 QuantiaSpatialV1

Entre `b8177a3...` y `93db041...` el checkpoint nuevo solo contiene:

- modificación de `.obsidian/workspace.json`;
- eliminación de `README.md`.

No se registraron cambios técnicos del motor en ese commit.

La eliminación de `README.md` debe revisarse para confirmar si fue intencional.

### Estado técnico verificado del motor actual

El motor actual ya contiene:

```text
QuantiaSpatialEngine
documento
→ F01
→ F01.5
→ F02
→ normalización métrica
```

y posteriormente:

```text
QuantiaSpatialV1ProcessEngine
→ Adaptive Reconstruction
→ PostFilter
→ Canonical WallGraph
→ Call 2 opcional
→ final_wall_graph
```

`QuantiaSpatialV1ProcessEngine` no contiene reglas específicas por Casa Viri/Miguel H/Miguel V.

### Call 2

El modo actual soporta:

- `OFF`
- `AUTO`
- `FORCE`

AUTO se activa si:

- el route plan requiere multimodal review; o
- el unresolved ratio es >= 0.15.

Cuando Call 2 modifica muros, el canonical graph se recalcula para evitar referencias topológicas obsoletas.

---

# 4. Evidencia decisiva de pruebas

## 4.1 Producción integrada actual

Con el documento real `PlantaBaja Miguel V.pdf`, el endpoint respondió HTTP 200, por lo que NO fue un timeout de frontend.

La entrega fue:

```text
execution.status = FAILED
spatialStatus = UNRESOLVED
progressPercent = 100
successPercent = null
LevelViews = 0
```

Warnings principales observados:

- PyMuPDF no encontró marcadores vectoriales de nivel;
- Gemini no pudo descubrir los niveles de la página;
- no existe evidencia suficiente para identificar niveles;
- normalización métrica requiere PDF + LevelView resoluble;
- Spatial no produjo LevelViews utilizables.

## 4.2 Probe B1

Mismo motor actual + mismo PDF, pero aportando:

```text
known_level_names_by_page = {1: ["Planta Baja"]}
isolated_pages = {1}
enable_gemini_discovery = False
```

Resultado:

```text
LevelViews = 1
Nombre = Planta Baja
Página = 1
Raster = 2048 x 1372
B1 = PASS
```

Conclusión: F01 puede producir correctamente el LevelView cuando recibe la semántica de bootstrap que históricamente aportaba el harness.

## 4.3 Probe B2

Mismo documento con bootstrap histórico + F01.5 real Gemini:

```text
LevelViews = 1
Levels = 1
Metric normalization = NORMALIZED
Evidence = 1556
F02 state = VALID
Perimeter walls = 10
B2 = PASS
```

También se observó:

- OCR falló por falta de Tesseract;
- las demás fuentes siguieron operando;
- F02 reconstruyó el ciclo exterior;
- no se forzó una escala métrica global cuando la evidencia dimensional no era suficiente;
- Perimeter Raster Reprojector reprojectó sin redetección.

## 4.4 Probe B3

Reconstrucción posterior con Call 2 OFF:

```text
Walls = 39
Logical gaps = 40
Interior spaces = 2
B3 = PASS
```

Conclusión: el bloqueo de la integración aparece antes de este tramo. El flujo downstream puede trabajar cuando F01 recibe un bootstrap válido.

---

# 5. Hallazgo principal — lógica histórica dentro de tests

El archivo archivado:

`archive/spatial-baseline-pre-clean-2026-10-01/tests/quantia_case_loader.py`

demuestra que los casos históricos no ejecutaban el mismo escenario que hoy ejecuta producción.

### Casa Viri

El loader:

- recuperaba localization replay;
- extraía nombres de nivel;
- pasaba `known_level_names_by_page`;
- usaba localization replay;
- usaba extraction replay;
- deshabilitaba discovery real.

### Miguel H

El loader:

- fijaba `("Planta Baja", "Planta Alta")`;
- pasaba `known_level_names_by_page`;
- usaba localization/extraction replay;
- deshabilitaba discovery.

### Miguel V

El loader pasaba explícitamente:

```python
known_level_names_by_page={1: [str(item["level_name"])]}
isolated_pages={1}
enable_gemini_discovery=False
enable_gemini_extraction=True
gemini_extraction_payloads_by_page={1: replay_payload}
```

Por tanto los tests que validaron históricamente Miguel V NO validaban una ejecución limpia:

`document bytes → F01 Discovery → LevelView`

sino:

`document bytes + nombre de nivel + aislamiento + replay → LevelView → downstream`.

Este hallazgo obliga a auditar tests/harnesses antes de decidir dónde corregir.

---

# 6. Errores actuales / bloqueos reales

## E-01 — F01 Discovery no produce LevelView en integración real

**Estado:** ABIERTO / PRIORIDAD MÁXIMA.

Producción no aporta `known_level_names_by_page` ni `isolated_pages`; activa `enable_gemini_discovery=True`.

Cuando no hay marcadores vectoriales, `_should_discover_pdf_page()` obliga a Discovery Gemini.

El fallo se reduce públicamente a:

`Gemini no pudo descubrir los niveles de la página.`

El detalle original de `SpatialVisionProviderError` queda encapsulado y no aparece completo en la entrega pública.

### Acción requerida

Auditar:

- F01;
- helpers;
- tests históricos;
- runners;
- políticas de aislamiento;
- semántica de nivel;
- fallback de identificación;
- observabilidad de Discovery.

No mover todavía lógica a V2L sin demostrar su propiedad arquitectónica.

---

## E-02 — Diferencia entre tests y runtime productivo

**Estado:** ABIERTO / PRIORIDAD MÁXIMA.

Los tests históricos aportaban entradas que producción ya no aporta.

Debe clasificarse cada dependencia como:

- fixture específico del caso;
- infraestructura de test;
- lógica general de runtime;
- contexto legítimo de integración;
- observabilidad.

No se debe copiar ciegamente `Planta Baja`, rutas, IDs o datos de Miguel V al motor.

---

## E-03 — OCR local sin Tesseract

**Estado:** ABIERTO / SECUNDARIO.

En B2:

`TesseractNotFoundError`

F01.5 conservó las otras fuentes y F02 llegó a VALID, por lo que actualmente NO es la causa raíz del bloqueo principal.

Debe decidirse más adelante si:

- Tesseract será dependencia obligatoria;
- OCR será opcional;
- existirá proveedor alternativo.

---

## E-04 — `project_site_context` no está conectado

**Estado:** ABIERTO.

El endpoint productivo actualmente llama:

`project_site_context=None`.

El contrato `QUANTIA_02_03_SPATIAL_CONTEXT_V1` sigue pendiente.

---

## E-05 — contrato 03 → 04 todavía temporal

**Estado:** ABIERTO.

Coexisten:

- `SPATIAL_INTERFACE04_REVIEW_V1`;
- `QUANTIA_03_04_V1`;
- objetivo `QUANTIA_03_04_V2`;
- `estructuraEspacial`.

No existe todavía una única versión contractual cerrada.

---

## E-06 — readiness no está cerrado

**Estado:** ABIERTO.

Backend Spatial delivery publica:

```text
workflowContinuation = false
quantification = false
```

mientras 03.2 ya permite que un estado terminal continúe al editor.

Debe definirse semánticamente la diferencia entre:

- “Spatial no está listo para cuantificar”;
- “el workflow sí puede continuar a 04”.

Actualmente ambos conceptos todavía pueden confundirse.

---

## E-07 — calidad global todavía no calculada

**Estado:** ABIERTO.

`successPercent` permanece `null`.

Esto es correcto mientras no exista una métrica real; no se debe fabricar un porcentaje.

Pendiente definir el cálculo global a partir de factores verificables.

---

## E-08 — componentes de modelo aún incompletos

**Estado:** ABIERTO.

La entrega actual publica vacíos:

- `puertas`;
- `ventanas`;
- `espacios`;
- `ejes`;
- `cotas`;
- `escaleras`.

El motor puede tener diagnósticos parciales, pero el contrato productivo aún no los publica como entidades finales consumibles.

---

## E-09 — correlación global todavía no implementada

**Estado:** ABIERTO.

Pendiente consolidar una validación cruzada entre:

- ejes;
- cotas;
- muros;
- centerlines;
- espacios;
- openings;
- semántica;
- geometría;
- perímetro;
- contexto del predio.

La documentación ya empezó a definir esta red, pero no existe todavía como gate productivo cerrado.

---

## E-10 — progreso en tiempo real no existe aún

**Estado:** ABIERTO.

El endpoint `analizar-plano` es síncrono.

03.2 puede mostrar estado RUNNING localmente, pero el backend termina entregando `progressPercent=100` al finalizar.

Pendiente, si se requiere progreso real:

```text
POST run → runId
GET status / polling o SSE
```

---

## E-11 — Call 2 no está validado aún en la integración completa actual

**Estado:** ABIERTO.

El servicio productivo usa `call2_mode="AUTO"`.

La prueba B3 decisiva fue ejecutada con Call 2 OFF.

Debe validarse por separado:

- activación AUTO;
- provider;
- correcciones;
- delta validation;
- re-finalización del WallGraph;
- salida contractual.

---

## E-12 — replay histórico vs firma fail-closed

**Estado:** ABIERTO.

El sistema actual exige firma explícita de replay:

- prompt SHA;
- schema SHA;
- raster SHA;
- MIME;
- project site context SHA.

Helpers históricos anteriores pueden contener replays sin la nueva firma y no deben reutilizarse silenciosamente.

---

## E-13 — documentación desactualizada respecto al código

**Estado:** ABIERTO.

`QUANTIA_FLUJO_CONTRATOS_02_03_04.md` todavía afirma que Fase 02 no captura:

- ancho;
- largo;
- área del terreno.

El código actual de `02ComoSeConstruiraView.vue` sí captura y persiste esos campos.

También `CURRENT_STATE.md` todavía indica:

`repo técnico Quantia SpatialV1: PENDIENTE DE CREACIÓN/CONEXIÓN`

aunque el repositorio ya existe, está conectado y se encuentra sincronizado.

La documentación debe actualizarse después de cerrar esta auditoría.

---

## E-14 — README de Spatial eliminado en el último checkpoint

**Estado:** REVISAR.

El diff `b8177a3 → 93db041` elimina `README.md`.

Debe confirmarse si fue intencional antes de consolidar otra versión.

---

## E-15 — archivos Obsidian/plugin versionados

**Estado:** REVISAR.

DocumentacionQuantia agregó:

- `.obsidian/workspace.json`;
- plugins completos de Obsidian Git;
- plugin Terminal;
- configuración local;
- `Sin título.md` vacío.

No bloquea Quantia, pero conviene decidir qué debe permanecer bajo control de versiones y qué debe pasar a `.gitignore`.

---

# 7. Problemas ya descartados o resueltos

## Timeout frontend

Descartado como causa raíz del fallo Spatial actual.

El POST regresó HTTP 200 y una entrega válida con estado `FAILED`.

## Gemini API key

Verificada como configurada durante el diagnóstico.

## Fallback Gemini

Verificado en el provider actual:

- principal: `gemini-3.7-flash`;
- fallback: `gemini-3.5-flash`.

No debe duplicarse otro fallback sin revisar primero el provider existente.

## Divergencia DocumentacionQuantia

Resuelta.

Estado inicial:

`ahead 2, behind 7`.

Se creó punto de retorno, se ejecutó rebase sobre `0436806`, se resolvió manualmente el único conflicto de:

`07_REVISION_CONTINUIDAD/01_CONTRATO_SPATIAL_FASE04.md`

y el working tree terminó limpio.

## Ejecución accidental de comandos fuera del repo

Detectada durante el rebase documental.

No se aplicaron operaciones destructivas. Se volvió a la ruta correcta antes de continuar.

---

# 8. Pendientes priorizados

## P0 — Auditoría test → motor

Siguiente tarea inmediata.

Revisar de forma exhaustiva:

- `tests/**`;
- `archive/.../tests/**`;
- runners;
- loaders;
- fixtures;
- adapters;
- providers;
- configuración previa a `QuantiaSpatialEngine.run()`.

Construir matriz:

```text
Lógica
→ ubicación histórica
→ equivalente actual en motor
→ uso productivo
→ clasificación
→ acción
```

Clasificaciones:

```text
FIXTURE_ONLY
TEST_INFRA_ONLY
MOTOR_RUNTIME
INTEGRATION_CONTEXT
OBSERVABILITY
```

Objetivo: identificar exactamente qué lógica general necesaria quedó atrapada en tests.

---

## P1 — Cerrar F01 productivo

Después de la auditoría:

- resolver identificación/bootstrap sin hardcode por caso;
- definir política de página aislada;
- conservar Discovery Gemini cuando aporte valor;
- definir fallback si Discovery no resuelve;
- mejorar error real/telemetría;
- probar entrada limpia con Miguel V;
- luego repetir Casa Viri y Miguel H.

Criterio mínimo:

`document bytes → LevelView(s)`

sin dependencia oculta de test.

---

## P2 — Revalidar pipeline completo

Cuando F01 quede cerrado:

```text
F01
→ F01.5 real
→ F02
→ normalización
→ Adaptive Reconstruction
→ PostFilter
→ Canonical WallGraph
→ Call 2 AUTO
```

sobre los tres casos estándar.

No ajustar tests por caso.

---

## P3 — Cerrar correlación espacial

Definir e implementar la red:

```text
MUROS ↔ CENTERLINES ↔ EJES
  ↕          ↕          ↕
ESPACIOS ↔ COTAS ↔ SEMÁNTICA
  ↕          ↕
OPENINGS ↔ GEOMETRÍA
  ↕
PERÍMETRO / PREDIO
```

Debe producir evidencia de MATCH / CONFLICT / UNKNOWN y no solo geometría.

---

## P4 — Openings / elementos arquitectónicos

Aplicar la arquitectura acordada:

1. Wall Canonicalization / Single-Line WallGraph;
2. segunda llamada de consolidación por deltas;
3. gaps lógicos clasificados;
4. tercera llamada especializada:
   - DOOR;
   - WINDOW;
   - NOT_OPENING;
5. host wall + posición + ancho;
6. ReconciliationEngine.

No cerrar físicamente gaps que puedan ser openings.

---

## P5 — Espacios

Cerrar:

```text
semantic space
↔ polygon
↔ boundary wall IDs
↔ dimensions
↔ area
↔ openings
```

y distinguir espacio topológico de espacio semántico confirmado.

---

## P6 — Ejes y cotas

Cerrar grounding de:

- eje como centro del muro;
- eje por tramos;
- nomenclatura;
- cota → muro/espacio/eje;
- orientación;
- unidad;
- evidencia.

---

## P7 — Contrato `QUANTIA_02_03_SPATIAL_CONTEXT_V1`

Conectar contexto real desde 01/02.

No enviar datos personales ni defaults como si fueran declarados.

Mantener:

`USER_DECLARED + DOCUMENT_OBSERVED + GEOMETRY_OBSERVED → MATCH / CONFLICT / UNKNOWN`.

---

## P8 — Contrato `QUANTIA_03_04_V2`

Cuando Spatial esté cerrado:

- eliminar transición temporal V1;
- entregar source/assets/evidence/readiness completos;
- evitar que 04 vuelva a rasterizar;
- usar raster canónico producido por Spatial;
- mantener una sola versión contractual.

---

## P9 — Calidad y readiness

Definir métricas reales para:

- scale;
- perimeter;
- walls;
- spaces;
- openings;
- correlation.

Solo después calcular `successPercent`.

Separar claramente:

- calidad;
- progreso;
- readiness para revisión;
- readiness para cuantificación.

---

## P10 — Frontend 03.2

Después de cerrar contrato:

- conectar V2;
- eliminar compatibilidad temporal;
- validar compilación real;
- validar navegación;
- decidir si se implementa ejecución async/polling/SSE.

---

## P11 — Limpieza documental

Actualizar:

- `CURRENT_STATE.md`;
- `CURRENT_VERSION.md`;
- `QUANTIA_FLUJO_CONTRATOS_02_03_04.md`;
- referencias de hashes;
- estado real de captura de terreno;
- estado real de conexión del repo Spatial.

Revisar además:

- `.obsidian/`;
- plugins;
- workspace;
- `Sin título.md`;
- README eliminado de Spatial.

---

# 9. Punto de retorno antes de cambios importantes

No modificar todavía el motor hasta terminar la auditoría.

Puntos conocidos:

```text
QuantiaSpatialV1
93db041a8d8dda0e7a50dfd7685bc34c6b157061

QuantiaV2L
5db89a2d047dd2c194a72512b500469236f7f083

DocumentacionQuantia
7c1ed44381e2bd99d0832cf2daadcbef22d758b6
```

Toda modificación siguiente del motor debe crear un nuevo punto de retorno antes de aplicar cambios estructurales.

---

# 10. Ruta recomendada inmediata

```text
AUDITAR TESTS/HARNESSES
        ↓
clasificar dependencias
        ↓
identificar MOTOR_RUNTIME faltante
        ↓
probar ausencia en motor actual
        ↓
crear punto de retorno
        ↓
migrar solo lógica general
        ↓
test limpio F01
        ↓
B1-equivalente sin bootstrap oculto
        ↓
F01.5/F02
        ↓
reconstrucción
        ↓
Call 2 AUTO
        ↓
contrato 03→04
```

La prioridad no es agregar más heurísticas ni modificar frontend. La prioridad es eliminar la diferencia entre el runtime productivo y la lógica efectiva con la que fueron validados históricamente los casos.

---

## 11. Decisión vigente al cierre de este reporte

**No continuar con correcciones incrementales por caso.**

Primero debe determinarse con evidencia qué responsabilidades generales quedaron en tests, helpers o runners y cuáles deben formar parte del motor productivo.

La prueba decisiva ya existe: el mismo PDF que falla en producción atraviesa F01/F01.5/F02 y reconstrucción cuando recibe el bootstrap histórico. El siguiente cambio debe atacar esa diferencia de arquitectura, no ocultarla en la integración.
