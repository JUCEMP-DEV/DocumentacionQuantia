# Reporte de cambios: geometría de Interfaz 04 y motor de cantidades de Interfaz 05

**Fecha:** 2026-09-29  
**Proyecto:** QuantiaV2L  
**Referencia funcional:** [Pipeline.md](../01_PROCESO_GENERAL/PIPELINE_QUANTIA.md), especialmente secciones 19, 23, 25 y 27.  
**Estado:** integración automática parcial implementada y probada. No representa el cierre de los 101 conceptos.

## 1. Problema reportado

Después de dibujar geometría en 04, la pantalla 05 mostraba conceptos bloqueados por entradas faltantes o el mensaje «El motor no generó conceptos disponibles para revisar».

Entre los conceptos señalados estaban CIM-001, CIM-003, CIM-003A, CIM-005 y CIM-006. Las entradas solicitadas incluían `geometria_area_confirmada`, `tramos_dala_desplante_confirmados` y `geometria_longitud_confirmada`.

## 2. Diagnóstico comprobado

El flujo manual de 04 exporta espacios, muros, aberturas y medidas a `estructuraEspacial`. La pantalla 05 envía ese contrato al backend al simular los módulos.

El motor agrega contexto general, pero sus reglas resuelven requisitos concretos desde el contexto y `engineInputsByConcept`. Faltaba una derivación que transformara la geometría canónica en esas entradas específicas. Tener un área general o un muro dibujado no completaba automáticamente los requisitos de cada regla.

Además, 05 construye su tabla con `proposedConcepts` y `confirmedConcepts`. Por ello podía presentar una tabla vacía aunque el backend sí hubiera encontrado conceptos, todos bloqueados o no aplicables.

Prueba diagnóstica previa, con datos sintéticos y sin modificar proyectos guardados:

| Solicitud | Resultado |
|---|---|
| Espacio y muro confirmados, sin entradas por concepto | 11 conceptos encontrados, 0 propuestos y 8 bloqueados |
| Mismo modelo y un área explícita de prueba para CIM-001 | CIM-001 propuesto con 3 m² |

Esto comprobó que el cálculo funcionaba al recibir el requisito; el problema estaba en la conexión entre geometría y entradas del motor. No constituye una inspección del estado exacto de la sesión del usuario.

## 3. Corrección del enfoque anterior

Inicialmente añadí un formulario en 05 para asignar mediciones de cimentación. Esa solución trasladaba al usuario una responsabilidad que el pipeline atribuye al motor y fue rechazada por el usuario.

Tras revisar el documento maestro:

- Retiré el formulario de mediciones de 05.
- Eliminé `ConceptMeasurementPanel.vue`, `conceptMeasurements.js` y su prueba específica.
- Mantuve 05 como revisión de propuestas, bloqueos y procedencia.
- Implementé la derivación automática en el backend.

La confirmación de cantidades propuestas sigue siendo parte del flujo; no equivale a recapturar las medidas que ya contiene 04.

## 4. Implementación actual

### 4.1 Derivación automática

Archivo nuevo: `Backend/app/services/spatial_quantity_inputs.py`.

La función `automatic_spatial_inputs()` recibe el contrato espacial y las condiciones constructivas. Devuelve entradas por concepto y un registro de procedencia.

Características:

- Consume entidades confirmadas y coordenadas métricas.
- Resuelve niveles mediante IDs y claves del contrato.
- Une polígonos por nivel para evitar sumar superficies superpuestas dos veces.
- Une ejes de muro para evitar duplicar longitudes coincidentes.
- Excluye del cálculo de pisos las categorías exteriores reconocidas y niveles identificados como azotea/roof.
- Recalcula desde el modelo recibido en cada simulación.
- No inventa anchos de cimentación, profundidades ni apoyos aislados.

### 4.2 Cobertura implementada

| Concepto | Entrada derivada | Condiciones |
|---|---|---|
| CIM-008 | `geometria_area_confirmada` | Unión de polígonos de piso confirmados en planta baja; obra nueva |
| ACA-001 | `geometria_area_confirmada` | Unión de polígonos de piso confirmados por nivel; obra nueva |
| CIM-005 | `tramos_dala_desplante_confirmados` | Ejes confirmados de planta baja; sistema tradicional/mampostería y cimentación continua; obra nueva |
| CIM-002 | `tramos_zapata_corrida_confirmados` | Condiciones anteriores y zapata corrida |
| CIM-006 | `geometria_longitud_confirmada` | Condiciones anteriores y mampostería corrida |

La derivación de ejes interpreta la selección de mampostería portante de 02 como condición para proponer longitudes de cimentación continua; excluye muros marcados explícitamente como no portantes. Esta interpretación requiere validación funcional con proyectos reales, especialmente donde existan divisiones no portantes sin clasificar.

Los casos de remodelación no reciben automáticamente toda la geometría del edificio como superficie intervenida.

### 4.3 Integración con las reglas

Archivo: `Backend/app/services/motor_simulation_service.py`.

La derivación se ejecuta dentro de `simulate_modulo()`, después de incorporar los controles de proyecto y antes de evaluar los conceptos.

- Las entradas explícitas existentes conservan prioridad.
- Las entradas derivadas completan requisitos ausentes por concepto.
- `contextSnapshot.geometryInference` registra únicamente derivaciones aplicadas.
- Se mantienen las validaciones de activación, dependencias y exclusiones.
- Una medida derivada no se convierte por sí sola en cantidad confirmada.

El flujo queda:

```text
Condiciones de 02 + contrato espacial de 04
    → contexto del motor
    → derivación geométrica por concepto
    → required_inputs
    → reglas, dependencias y exclusiones
    → propuestas o bloqueos
    → revisión en 05
```

### 4.4 Cambios visibles en 05

Archivo: `Frontend/src/modules/vivienda/views/workflow/05CalculoCantidadesView.vue`.

- Muestra cuántos espacios y muros recibe de 04.
- Permite consultar la procedencia de las mediciones automáticas.
- Distingue conceptos encontrados pero bloqueados de ausencia de propuestas.
- Muestra todos los conceptos bloqueados, no únicamente los primeros cinco.
- Expone las alertas del motor cuando están disponibles.
- Prioriza los bloqueos de la simulación actual frente a resultados anteriores almacenados.
- Conserva entradas explícitas de zapatas cuando existen, evitando reemplazarlas por el conteo alternativo de espacios.

### 4.5 Conservación e invalidación

En `04DisenoManualView.vue` se preservan `engineInputs` y `engineInputsByConcept` durante la actualización de datos generales, que puede reiniciar etapas posteriores.

Se conserva compatibilidad con asignaciones explícitas anteriores. Si una asignación marcada `EDITOR_ASSIGNMENT` referencia geometría que cambió o dejó de estar confirmada, el backend la considera pendiente en lugar de consumir una medida desactualizada.

Las nuevas derivaciones automáticas no dependen de esas asignaciones: se reconstruyen en cada solicitud.

## 5. Verificación ejecutada

### Pruebas automáticas

```powershell
# Desde Backend
.venv/Scripts/python.exe -B -m pytest tests/test_automatic_spatial_inputs.py tests/test_editor_measurement_consumption.py -q -p no:cacheprovider

# Desde Frontend
npm.cmd run build
```

Resultado: **5 pruebas aprobadas** y compilación de producción correcta.

Las pruebas cubren:

- Inferencia sin captura manual por concepto.
- Dedupe de polígonos y ejes coincidentes.
- Recalcular al modificar geometría.
- Restricción por nivel y tipo de intervención.
- Rechazo de geometría no confirmada o únicamente raster.
- Consumo mediante una regla real V1.8 de CIM-008.
- Prioridad de mediciones explícitas y trazabilidad de las derivaciones utilizadas.
- Invalidación de asignaciones antiguas cuando cambia su geometría.

### Solicitud HTTP al backend y catálogo real

Se probó `/api/motor/modulos/cimentacion/simular` con un modelo sintético de 20 m² y un eje de muro de 5 m, sin `engineInputsByConcept`.

| Concepto | Estado devuelto | Cantidad |
|---|---|---:|
| CIM-005 | PROPOSED | 5 m |
| CIM-008 | PROPOSED | 20 m² |
| CIM-001 | BLOCKED_MISSING_INPUT | Sin cantidad: falta área de plantilla |
| CIM-006 | BLOCKED_CONFLICT | Sin cantidad publicable: dependencias pendientes |

La respuesta fue HTTP 200. Esta prueba no guardó una cotización ni modificó un proyecto del usuario.

El backend se reinició con el código actualizado. Su disponibilidad futura depende de que el proceso local continúe activo.

## 6. Pendientes y límites

1. **Cobertura parcial:** no se implementó la derivación automática de los 101 conceptos.
2. **Cimentación detallada:** el modelo arquitectónico no determina por sí solo área de plantilla, ancho de zapata, profundidad de excavación ni número de apoyos aislados.
3. **Clasificación constructiva:** falta cerrar la correspondencia de muros portantes/no portantes, sistemas de muro, superficies de acabado y elementos estructurales.
4. **Remodelación:** falta derivación desde geometría de intervención explícita, evitando cuantificar toda la construcción existente.
5. **Contrato geométrico:** la derivación actual de pisos requiere polígonos métricos con vértices; un área suelta o un contorno raster no los sustituye.
6. **Niveles:** la identificación de planta baja usa las claves admitidas por el adaptador; deben ampliarse mediante normalización del contrato cuando aparezcan otras claves.
7. **Pruebas completas:** falta validar en navegador el recorrido de un proyecto real desde edición, confirmación y guardado hasta cálculo y presupuesto final.
8. **Sesión del usuario:** no se ha auditado la solicitud concreta del proyecto que originó los 19 bloqueos; las pruebas descritas usaron datos controlados.

Estos pendientes deben resolverse en el contrato de 02/04 y en las reglas de consumo del backend. No deben sustituirse por un formulario de cantidades obligatorias en 05 ni por dimensiones estructurales inventadas.

## 7. Archivos principales

| Archivo | Responsabilidad |
|---|---|
| `Backend/app/services/spatial_quantity_inputs.py` | Derivación automática desde geometría |
| `Backend/app/services/motor_simulation_service.py` | Integración, prioridad e invalidación |
| `Backend/tests/test_automatic_spatial_inputs.py` | Regresión de la derivación automática |
| `Backend/tests/test_editor_measurement_consumption.py` | Compatibilidad e invalidación de asignaciones previas |
| `Frontend/src/modules/vivienda/views/workflow/04DisenoManualView.vue` | Conservación de entradas al continuar |
| `Frontend/src/modules/vivienda/views/workflow/05CalculoCantidadesView.vue` | Revisión y diagnóstico del consumo |
| `Documentacion/Integracion_04_05_2026-09-29.md` | Nota inicial de integración |

Este reporte documenta el trabajo de integración del motor con 04/05. No certifica como terminados los demás subsistemas descritos en el pipeline maestro.
