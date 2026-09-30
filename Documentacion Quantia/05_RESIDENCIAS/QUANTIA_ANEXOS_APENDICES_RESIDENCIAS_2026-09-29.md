# QUANTIA — ANEXOS Y APÉNDICES PARA EL REPORTE DE RESIDENCIA PROFESIONAL

**Proyecto:** Sistema inteligente QUANTIA  
**Programa:** Ingeniería en Sistemas Computacionales  
**Institución:** Instituto Tecnológico Superior P’urhépecha  
**Fecha de corte documental:** 29 de septiembre de 2026  
**Alcance de este archivo:** exclusivamente anexos y apéndices del Informe de Residencia Profesional.

---

## 1. Criterio institucional que debe respetarse

La Guía para el Informe de Titulación Integral ITSP 2026 distingue dos tipos de material complementario:

- **Anexos:** material relevante proveniente de otros autores, instituciones o fuentes externas. Debe aportar validez, claridad o profundidad y estar citado previamente dentro del cuerpo del informe.
- **Apéndices:** material relevante elaborado por el propio residente/investigador durante el desarrollo del proyecto.

Para QUANTIA, la mayor parte de la evidencia técnica generada durante la residencia —diagramas propios, matrices, contratos, resultados de pruebas, JSON, capturas, reportes de validación, cronologías, hashes y documentación de versiones— corresponde a **APÉNDICES**, no a anexos.

No debe agregarse ningún anexo o apéndice que no sea mencionado previamente en el cuerpo del informe.

---

# 2. ANEXOS

Los anexos deberán limitarse a documentación externa realmente utilizada o citada en el informe.

## ANEXO A. Documentación institucional de JUCEMP

**Estado:** PENDIENTE DE DOCUMENTACIÓN.

Incluir únicamente si existe evidencia institucional verificable:

- constancia o documento que identifique formalmente a JUCEMP;
- misión, visión y valores, si están documentados;
- organigrama oficial;
- datos generales de la organización;
- documentación del área donde se desarrolló la residencia.

**No generar ni completar con supuestos.**

---

## ANEXO B. Documentos institucionales del proceso de residencia

**Estado:** PENDIENTE / DEPENDE DEL EXPEDIENTE ACADÉMICO.

Posibles documentos:

- oficio de asignación o aceptación;
- oficio de liberación;
- autorización de impresión;
- formatos oficiales relacionados con la residencia;
- documentos de seguimiento que deban integrarse como evidencia externa.

Estos documentos no son generados por QUANTIA.

---

## ANEXO C. Normativa, lineamientos o especificaciones externas empleadas

**Estado:** INCLUIR SOLO LAS REALMENTE CITADAS.

Puede contener extractos o documentos externos utilizados para justificar decisiones técnicas, por ejemplo:

- normas aplicables a construcción;
- lineamientos técnicos;
- especificaciones de fabricantes;
- documentación externa de tecnologías empleadas;
- esquemas o tablas oficiales de fuentes institucionales.

Debe evitarse copiar documentación extensa que no haya sido utilizada directamente en el desarrollo o discusión del proyecto.

---

## ANEXO D. Información estadística o geográfica externa

**Estado:** OPCIONAL.

Solo debe integrarse si el informe utiliza explícitamente información externa de:

- INEGI;
- Data México;
- dependencias gubernamentales;
- estudios regionales;
- estadísticas del sector de la construcción;
- datos contextuales de la Meseta Purépecha.

El antecedente de investigación regional puede utilizarse como fuente documental, pero los productos elaborados por el residente a partir de dicho antecedente deben colocarse en apéndices.

---

# 3. APÉNDICES

Los siguientes materiales corresponden directamente al trabajo desarrollado en QUANTIA y deben constituir la principal evidencia técnica del informe.

---

## APÉNDICE A. Matriz de trazabilidad entre objetivos, actividades, productos y evidencia

**Estado:** EXISTE PARCIALMENTE; ACTUALIZAR.

Debe relacionar:

| Objetivo específico | Actividad realizada | Módulo QUANTIA | Producto | Evidencia | Estado |
|---|---|---|---|---|---|
| Diagnóstico del proceso | Levantamiento del flujo | General | Flujo funcional | Diagramas y documentación | Validado |
| Diseño de arquitectura | Separación Quantia / PU-Core | Arquitectura | Contratos y arquitectura | Diagramas y archivos | Validado |
| Interpretación documental | OCR, RAG y Spatial | Documentos / Spatial | Evidencia estructurada | JSON, imágenes y pruebas | En evolución |
| Cuantificación | Motor V1.8 | Motor | Cantidades por concepto | Reglas y pruebas | Implementado |
| Integración económica | PU-Core | Presupuesto | PU y costo directo | Publicaciones y BD | Implementado |
| Interfaz | Workflow 01–06 | Frontend | Flujo de usuario | Capturas y rutas | Parcial |

Debe actualizarse con el estado real al cierre del reporte.

---

## APÉNDICE B. Cronología técnica del desarrollo de QUANTIA

**Estado:** EXISTE; ACTUALIZAR HASTA EL CIERRE.

Debe conservar hitos verificables, entre ellos:

- definición inicial de arquitectura;
- catálogo V1.7;
- migración a V1.8;
- publicación de precios desde PU-Core;
- desarrollo de frontend;
- implementación de documentos/OCR/RAG;
- separación de Quantia Spatial;
- F01;
- F01.5;
- F02;
- evolución de F03;
- Canonical Metric Raster;
- Wall Canonicalization;
- Call 2;
- contrato hacia interfaz 04;
- motor de cantidades;
- integración de presupuesto;
- validaciones y regresiones.

No debe presentar como terminado aquello que siga en prueba.

---

## APÉNDICE C. Arquitectura general de QUANTIA

**Estado:** DEBE GENERARSE COMO ARTEFACTO FINAL.

Debe incluir al menos:

```text
Usuario
  ↓
Frontend
  ↓
Workflow Quantia Vivienda
  ↓
Documentos / OCR / RAG
  ↓
Quantia Spatial
  ↓
Editor espacial
  ↓
Motor de cantidades V1.8
  ↓
PU-Core
  ↓
Presupuesto / resultados
  ↓
Supabase
```

Debe distinguir:

- QUANTIA;
- Quantia Vivienda;
- Quantia Spatial;
- PU-Core;
- Supabase;
- frontend;
- backend;
- almacenamiento documental;
- servicios de IA.

---

## APÉNDICE D. Flujo funcional completo de QUANTIA Vivienda

**Estado:** DEBE GENERARSE / ACTUALIZARSE.

Flujo vigente:

```text
01 Proyecto y alcance
02 Cómo se construirá
03.1 Carga de documentos
03.2 Análisis IA / Spatial
04A Diseño desde plano
04B Dibújalo tú
05 Cálculo de cantidades
06 Presupuesto y resultados
```

Debe documentarse también la convergencia:

```text
Plano analizado ─┐
                 ├─→ estructura espacial común → cantidades
Dibujo manual ───┘
```

---

## APÉNDICE E. Catálogo Maestro QUANTIA V1.8

**Estado:** VALIDADO.

Debe contener evidencia del estado canónico:

- 101 conceptos totales;
- 99 activos;
- 2 inactivos: `EST-008` y `EST-010`;
- UUID canónicos;
- claves únicas;
- incorporación de `CIM-003A`;
- unidades;
- partidas;
- modos de cuantificación;
- reglas principales.

No es necesario insertar las 101 reglas completas dentro del cuerpo del informe; pueden ir en este apéndice o en un archivo electrónico complementario.

---

## APÉNDICE F. Reglas del motor de inferencia y cuantificación V1.8

**Estado:** IMPLEMENTADO; DOCUMENTAR.

Fuente técnica principal:

`Backend/data/engine/Reglas_Motor_Inferencia_Quantia_V2_V1_8_DEFINITIVAS.json`

Debe incluir:

- versión del schema;
- versión de reglas;
- fuentes de datos permitidas;
- prioridades de evidencia;
- estados:
  - `PROPOSED`;
  - `CONFIRMED`;
  - `BLOCKED_MISSING_INPUT`;
  - `BLOCKED_CONFLICT`;
  - `NOT_APPLICABLE`;
  - `INACTIVE`;
- activation rules;
- required inputs;
- dependencies;
- exclusions;
- inference strategies;
- validaciones universales.

También debe incorporarse una tabla índice de las estrategias de inferencia implementadas.

---

## APÉNDICE G. Integración QUANTIA ↔ PU-Core

**Estado:** VALIDADO EN SU ARQUITECTURA.

Debe documentarse la relación:

```text
Quantia.catalog_concepts.id
        =
PU-Core.cards.external_concept_id
        =
published/current_unit_prices.external_concept_id
```

Debe incluir evidencia de:

- tarjetas;
- insumos;
- matrices;
- publicación/versionado;
- costo directo;
- precios publicados;
- separación entre cantidad y precio.

Regla que debe quedar explícita:

```text
QUANTIA determina QUÉ y CUÁNTO.
PU-Core aporta el PRECIO UNITARIO.
```

---

## APÉNDICE H. Estructura de base de datos y persistencia en Supabase

**Estado:** DOCUMENTACIÓN DISPONIBLE; CONSOLIDAR.

Debe incluir únicamente tablas relevantes y relaciones principales, por ejemplo:

- catálogo;
- unidades;
- reglas;
- publicaciones de precios;
- proyectos/cotizaciones;
- documentos;
- resultados;
- referencias de usuario;
- información persistida del workflow.

Incluir diagrama simplificado de relaciones, no un volcado completo innecesario de la base.

---

## APÉNDICE I. Arquitectura y contratos de Quantia Spatial

**Estado:** EN DESARROLLO; DOCUMENTAR POR MADUREZ.

Debe incluir el flujo:

```text
F01
→ F01.5
→ resolución de escala
→ Canonical Metric Raster
→ F02
→ Wall Canonicalization
→ Single-Line WallGraph
→ Call 2
→ Call 3
→ RoomGraph / Reconciliation
→ Functional Building Model
```

Debe diferenciar claramente:

- implementado;
- validado;
- experimental;
- pendiente.

No declarar Call 3, RoomGraph ni ReconciliationEngine como terminados.

---

## APÉNDICE J. Contratos principales de datos de Quantia Spatial

**Estado:** DEBE CONSOLIDARSE.

Incluir definición resumida de:

- `LevelView`;
- `RawEvidence`;
- `Wall`;
- `Junction`;
- `Gap`;
- `Opening`;
- `Space`;
- `WallGraph`;
- contratos de revisión;
- procedencia;
- estados de validación.

Debe registrarse la versión de cada contrato cuando exista.

---

## APÉNDICE K. Persistencia, replay y trazabilidad de llamadas multimodales

**Estado:** IMPLEMENTADO EN DISTINTAS ETAPAS; CONSOLIDAR.

Debe documentar:

- prompts;
- schemas;
- modelo/proveedor;
- fecha;
- hash;
- imagen enviada;
- respuesta cruda;
- respuesta normalizada;
- estado;
- validación;
- replay exacto cuando corresponda.

Históricos y registros no deben sobrescribirse.

---

## APÉNDICE L. Casos de validación espacial

**Estado:** ACTIVO.

Casos principales:

```text
Casa Viri
Miguel H
Miguel V
```

Debe incluir por cada caso:

- archivo fuente;
- niveles;
- LevelViews;
- propósito de la prueba;
- versión del motor;
- resultado;
- evidencia visual;
- errores encontrados;
- estado final.

Los tres casos deben compararse con criterios equivalentes.

---

## APÉNDICE M. Resultados de pruebas automatizadas y regresiones

**Estado:** ACTUALIZAR AL CORTE FINAL.

No insertar simplemente salidas completas de pytest.

Debe generar una matriz resumida:

| Subsistema | Prueba | Versión | Casos | Resultado | Observaciones |
|---|---|---|---|---|---|

Separar:

- unitarias;
- regresión;
- integración;
- probes;
- pruebas visuales;
- pruebas reales con proveedor externo.

Debe especificarse cuando una selección de pruebas aprobada no representa toda la suite.

---

## APÉNDICE N. Evidencia visual de reconstrucción de planos

**Estado:** GENERAR SELECCIÓN CURADA.

Debe contener imágenes comparables para:

- plano original;
- LevelView;
- perímetro;
- reconstrucción walls-only;
- WallGraph;
- Call 2;
- elementos detectados;
- resultado antes/después cuando aplique.

No incluir todas las imágenes generadas durante el desarrollo. Seleccionar únicamente las que demuestren una decisión, mejora, error o validación mencionada en el informe.

---

## APÉNDICE O. Segunda llamada multimodal — Call 2

**Estado:** EXPERIMENTAL / EN VALIDACIÓN.

Debe incluir:

- objetivo;
- contrato de entrada;
- imagen A/B;
- prompt;
- schema;
- proveedor/modelo;
- deltas devueltos;
- reglas de aceptación/rechazo;
- resultado realmente aplicado;
- comparación visual.

Debe diferenciar siempre:

```text
respuesta del modelo
≠
corrección aceptada por el validador
```

La prueba real documentada de Casa Viri planta alta debe aparecer como evidencia experimental, no como cierre general del sistema.

---

## APÉNDICE P. Contrato Spatial → Interfaz 04

**Estado:** EN INTEGRACIÓN.

Incluir:

`SPATIAL_INTERFACE04_REVIEW_V1`

y/o la versión vigente al cierre.

Debe documentar:

- geometría raster;
- geometría métrica;
- muros;
- espesor;
- longitud;
- procedencia;
- topología;
- candidatos;
- readiness;
- elementos faltantes.

Debe quedar explícito cuándo:

```text
workflowContinuation = false
quantification = false
```

y por qué.

---

## APÉNDICE Q. Evidencia del frontend y workflow

**Estado:** ACTUALIZAR.

Debe incluir capturas representativas de:

- proyecto y alcance;
- sistema constructivo;
- carga documental;
- análisis IA;
- editor desde plano;
- editor manual;
- cantidades;
- presupuesto;
- imprimibles.

Cada captura debe tener:

- número;
- descripción;
- estado;
- fecha o versión;
- relación con el procedimiento descrito.

---

## APÉNDICE R. Contrato y funcionamiento del editor espacial

**Estado:** IMPLEMENTADO; DOCUMENTAR.

Debe incluir:

- schema `quantia-editor-1.0`;
- levels;
- terrain;
- spaces;
- walls;
- doors;
- windows;
- stairs;
- annotations;
- WallGraph del editor;
- host wall de openings;
- undo/redo;
- snap/grid;
- serialización;
- validaciones.

---

## APÉNDICE S. Relaciones espaciales e invalidación en cascada

**Estado:** IMPLEMENTADO.

Documentar:

```text
N
S
E
W
__EXTERIOR__
```

y el flujo de invalidación:

```text
clasificacion
→ alcance
→ preliminares
→ datos_generales
→ estructura_espacial
→ colindancias
→ validacion_espacial
→ modulos
→ revision_inferencia
→ resumen
→ resultado
```

Debe mostrarse por qué modificar una etapa invalida resultados posteriores dependientes.

---

## APÉNDICE T. Evidencia del motor de cantidades

**Estado:** IMPLEMENTADO; VALIDAR CON CASOS FINALES.

Para varios conceptos representativos se debe conservar:

- concepto;
- regla de activación;
- input requerido;
- evidencia;
- estrategia;
- cantidad;
- unidad;
- estado;
- confirmación del usuario;
- PU;
- total.

Esto demuestra la trazabilidad:

```text
evidencia
→ geometría/dato
→ regla
→ cantidad
→ concepto
→ PU
→ importe
```

---

## APÉNDICE U. Evidencia del presupuesto y resultados

**Estado:** IMPLEMENTADO.

Incluir ejemplo completo con:

- costo directo;
- indirectos;
- financiamiento;
- utilidad;
- IVA;
- presupuesto total;
- conceptos;
- cantidades;
- PU;
- materiales cuando exista breakdown;
- mano de obra cuando exista breakdown.

No inventar desglose de materiales o mano de obra si la tarjeta no lo proporciona.

---

## APÉNDICE V. Reportes e imprimibles

**Estado:** REVISAR AL CIERRE.

Documentar las salidas:

```text
/vivienda/print/presupuesto
/vivienda/print/materiales
/vivienda/print/mano-obra
```

Incluir muestras únicamente cuando estén funcionando con datos válidos.

---

## APÉNDICE W. Limitaciones técnicas y pendientes

**Estado:** OBLIGATORIO ACTUALIZAR.

Debe concentrar pendientes reales, entre ellos:

- integración productiva completa 03 → SpatialV1 → 04;
- ejecución completa de LevelViews desde frontend;
- almacenamiento/URLs de recortes;
- Call 2 validada en más casos reales;
- openings;
- grounding dimensional de puertas/ventanas;
- RoomGraph;
- ReconciliationEngine;
- cierre de espacios;
- exportación DXF/SVG/IFC/BIM;
- definición de matrices de instalaciones;
- compatibilidades constructivas aún pendientes.

Este apéndice evita que el informe presente trabajo experimental como concluido.

---

## APÉNDICE X. Índice de archivos técnicos y artefactos reproducibles

**Estado:** DEBE GENERARSE.

Debe crearse una tabla maestra con:

| Archivo / artefacto | Ruta | Fecha | Versión | SHA-256 | Función | Estado |
|---|---|---|---|---|---|---|

Incluir únicamente artefactos relevantes para reproducibilidad:

- reglas V1.8;
- catálogos;
- checkpoints;
- reportes;
- contratos;
- pruebas;
- archivos de salida;
- visualizaciones;
- manifests.

---

## APÉNDICE Y. Evidencia del antecedente de investigación

**Estado:** YA EXISTE INFORMACIÓN BASE.

Debe sintetizar el antecedente:

**“Inteligencia artificial como herramienta de innovación económica en comunidades indígenas de Michoacán: propuesta de fortalecimiento productivo en Cherán, Paracho y Nahuatzen”.**

Incluir únicamente lo necesario para demostrar:

- origen contextual de QUANTIA;
- problemas administrativos/tecnológicos observados;
- relación con adopción tecnológica;
- evolución hacia una solución aplicada al sector de construcción.

Los datos de ese estudio no deben utilizarse como métricas de precisión o desempeño de QUANTIA.

---

## APÉNDICE Z. Matriz final de estado del proyecto

**Estado:** GENERAR AL CIERRE DE RESIDENCIA.

Último apéndice recomendado porque permite cerrar documentalmente QUANTIA sin inventar avances.

| Componente | Implementado | Validado | Experimental | Pendiente | Evidencia |
|---|---:|---:|---:|---:|---|
| Arquitectura QUANTIA | Sí | Sí | No | No | Arquitectura y contratos |
| Catálogo V1.8 | Sí | Sí | No | No | Migración / BD |
| PU-Core | Sí | Parcial/según producto | No | Revisión final | Publicaciones |
| Frontend 01–06 | Sí | Parcial | No | Integración final | Capturas / código |
| OCR/RAG | Sí | Parcial | No | Validación final | Logs / pruebas |
| Spatial F01 | Sí | Sí | No | No | Tests |
| Spatial F01.5 | Sí | Sí | No | No | Evidencia |
| Spatial F02 | Sí | Sí como baseline | No | Ajustes funcionales posteriores | Visuales |
| Wall Canonicalization | Sí | Parcial | Sí | Sí | Probes |
| Call 2 | Sí como prueba | Parcial | Sí | Generalización | Resultados |
| Openings / Call 3 | Parcial | No | Sí | Sí | Mini-Engine |
| RoomGraph | No final | No | Sí | Sí | Prototipos |
| ReconciliationEngine | No final | No | Sí | Sí | Diseño |
| Editor | Sí | Parcial | No | Integración | Frontend |
| Motor V1.8 | Sí | Sí en reglas | No | Casos finales | JSON/tests |
| Presupuesto | Sí | Parcial | No | E2E final | Resultados |

Esta matriz debe actualizarse usando únicamente el estado real al momento de entregar el informe.

---

# 4. Material que debe generarse antes de cerrar los apéndices

Para cerrar correctamente la documentación de residencia todavía deben producirse o consolidarse los siguientes artefactos:

1. Diagrama final de arquitectura QUANTIA.
2. Diagrama final del flujo 01–06.
3. Diagrama específico Quantia ↔ PU-Core ↔ Supabase.
4. Diagrama final del pipeline Spatial vigente.
5. Matriz final de trazabilidad objetivos–actividades–evidencia.
6. Cronología actualizada hasta la fecha de cierre.
7. Tabla maestra de catálogo V1.8.
8. Índice de estrategias del motor V1.8.
9. Diagrama simplificado de base de datos.
10. Tabla comparativa de Casa Viri, Miguel H y Miguel V.
11. Matriz consolidada de pruebas.
12. Selección curada de evidencias visuales.
13. Evidencia completa de Call 2.
14. Contrato final de entrega Spatial → 04.
15. Capturas finales del frontend.
16. Ejemplo reproducible de cantidad → PU → presupuesto.
17. Índice de artefactos con hashes.
18. Matriz final de estado del proyecto.
19. Listado explícito de limitaciones y trabajo pendiente.
20. Índice general de anexos y apéndices utilizado por el documento final.

---

# 5. Criterio final de integración al reporte

En el cuerpo del informe deben aparecer únicamente los resultados y explicaciones necesarios para comprender el desarrollo.

Cuando una evidencia sea extensa, debe citarse desde el texto:

```text
...como se observa en el Apéndice L...
...la matriz completa se presenta en el Apéndice A...
...los resultados reproducibles se incluyen en el Apéndice M...
```

Los apéndices son evidencia del trabajo realizado, no sustituyen la explicación del Capítulo II ni la discusión del Capítulo III.

Los anexos deberán incorporarse únicamente cuando exista material externo verdaderamente utilizado y citado.

---

**Fin del archivo de anexos y apéndices.**
