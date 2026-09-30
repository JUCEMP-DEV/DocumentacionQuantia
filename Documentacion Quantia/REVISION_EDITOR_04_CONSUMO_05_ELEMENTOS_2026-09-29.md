# Revisión integral de Interfaz 04 y contrato de consumo para 05

> Actualización posterior: esta auditoría describe el estado previo. La implementación del contrato común y de las herramientas frecuentes, con sus límites y pruebas, se encuentra en [IMPLEMENTACION_EDITOR_04_CONSUMO_05_2026-09-29.md](IMPLEMENTACION_EDITOR_04_CONSUMO_05_2026-09-29.md). El schema runtime activo es `QUANTIA_04_05_V1`; el schema canónico DRAFT de esta auditoría continúa como propuesta más amplia.

**Fecha:** 2026-09-29.  
**Referencia:** [Pipeline.md](Pipeline.md), secciones 19, 23, 25 y 27.  
**Alcance:** inspección de código de ambas variantes de 04, adaptadores, validación geométrica, Call 2 y las 101 reglas locales V1.8.  
**Estado de esta entrega:** auditoría y especificación de contrato. El schema adjunto es un diseño propuesto; no sustituye todavía las rutas productivas ni certifica una integración completa.

## 1. Dictamen

04 no entrega todavía todos los datos necesarios para cuantificar el alcance completo. Existe geometría arquitectónica editable, pero faltan clasificación constructiva, elementos estructurales, acabados por superficie, instalaciones y validación de completitud.

La solución debe respetar estas responsabilidades:

- **02:** definir escenario, alcance y sistemas constructivos.
- **03:** aportar evidencia, hipótesis y elementos detectados, conservando incertidumbre.
- **04:** construir/revisar entidades, resolver sus propiedades y validar geometría y relaciones.
- **Backend:** derivar medidas e inferir conceptos desde ese modelo, con reglas y trazabilidad.
- **05:** revisar propuestas, exclusiones y bloqueos; no volver a capturar medidas disponibles en 04.

Ni todas las cantidades se obtienen de una planta arquitectónica ni todos los datos pendientes deben pedirse al usuario. Primero se resuelven de evidencia, relaciones geométricas y definiciones existentes; solo lo realmente indeterminado se señala en 02/04.

## 2. Archivos de consumo establecidos

| Archivo | Uso |
|---|---|
| [CONTRATO_CONSUMO_04_05_V1.schema.json](Contratos/CONTRATO_CONSUMO_04_05_V1.schema.json) | Estructura propuesta de entidades, estado, procedencia y completitud |
| [CONSUMO_04_05_V1.plantilla.json](Contratos/CONSUMO_04_05_V1.plantilla.json) | Plantilla vacía explícitamente no lista para cálculo; no es un proyecto real |
| [MATRIZ_REQUISITOS_MOTOR_04_05_V1.json](Contratos/MATRIZ_REQUISITOS_MOTOR_04_05_V1.json) | Los 101 conceptos con unidad, estrategia, requisitos, dependencias y exclusiones |

La matriz se obtiene del archivo local de reglas, no de nombres inventados. Incluye hash de la fuente para detectar cambios. Su indicador de cobertura se refiere solamente al adaptador nuevo `spatial_quantity_inputs.py`, no a todos los caminos legacy del motor.

Regeneración:

```powershell
Backend/.venv/Scripts/python.exe -B Backend/scripts/build_04_05_contract_audit.py
```

Versiones que deben distinguirse:

1. `SPATIAL_INTERFACE04_REVIEW_V1`: paquete experimental de Call 2 para revisar muros.
2. `QUANTIA_04_TO_05_DRAFT_V1`: borrador descargable actual; su preparación para cálculo siempre es falsa.
3. `QUANTIA_04_05_CANONICAL_V1_DRAFT`: contrato de diseño establecido en esta auditoría para ampliar el consumo completo.

No se deben intercambiar automáticamente. Antes de activar el tercer contrato se requieren adaptadores, migración de proyectos y pruebas de ida y vuelta. El endpoint vigente recibe `estructuraEspacial`, datos generales y controles; todavía no acepta este nuevo sobre como reemplazo directo.

## 3. Estado real del editor

| Capacidad | Modo manual | Modo plano | Pendiente |
|---|---|---|---|
| Espacios | Rectángulos/polígonos, propiedades y confirmación | Revisión de espacios recibidos | Huecos, superposición, intervención y completitud por nivel |
| Muros | Creación, propiedades, relaciones y reconciliación | Dibujo mediante segmentos raster | Material, rol portante explícito y acabados por cara |
| Puertas | Creación sobre muro; ancho, alto, posición y giro | Consumo visual, sin flujo equivalente de edición | Subtipo, uso, material y enlace de catálogo |
| Ventanas | Creación sobre muro; ancho, alto y antepecho | Consumo visual | Tipo, material, vidrio, altura contra muro y completitud |
| Portón de garaje | Sin entidad/tipo específico | Call 2 tampoco tiene `GARAGE_DOOR` | Clasificación, operación, anfitrión y catálogo |
| Escaleras | Existe `createStair()` y serialización básica | Sin render/herramienta específica localizada | Crear, editar y validar tramos, descansos, niveles y hueco de losa |
| Niveles | Altura y elevación | Selector y normalización de claves | Coordenadas comunes entre plantas y verificación de desfase vertical |
| Precisión | Medición, snap y restricciones geométricas básicas | Principalmente selección/desplazamiento | Paridad de herramientas para plano y manual |
| Historial | Deshacer/rehacer del editor manual | No es equivalente al flujo manual | Invalidar resultados y confirmaciones al modificar entidades relacionadas |
| Consumo de 05 | Exporta entidades al store | Conserva el contrato recibido | Resolver todos los requisitos por concepto en backend |

Nombrar un espacio «escalera» no crea una escalera parametrizada. Un bbox `STAIR` de Call 2 tampoco constituye su geometría final.

## 4. Problemas concretos encontrados

### 4.1 Tipificación y pérdida de información

`createDoor()` no conserva campos de material, uso, tipo de operación o portón. `createWindow()` tampoco conserva clasificación de material/vidrio. Añadir esos datos a una respuesta del modelo sin actualizar schema, normalización y adaptadores puede perderlos al guardar/reabrir.

`createStair()` conserva niveles origen/destino, posición, giro, ancho, geometría y tipo; no define tramos, huellas, contrahuellas, descansos ni huecos de losa.

### 4.2 Validación de vanos incompleta

`validateOpening()` comprueba anfitrión, posición normalizada, ancho y encaje longitudinal. Sin embargo:

- Permite ancho desconocido mientras se edita; no basta como validación de preparación para cálculo.
- No comprueba allí altura, antepecho + altura frente a la altura del muro ni coincidencia de nivel.
- Trata el solapamiento solo en una dimensión longitudinal; una ventana superior y una puerta inferior requieren análisis en la cara del muro, no únicamente sobre su eje.
- Distingue ventana por existencia de `sillHeightM`, no por una familia explícita extensible.

### 4.3 Posición desconocida convertida en cero

En `openings.js`, `Number.isFinite(Number(position))` considera válido `null`, porque `Number(null)` vale cero. En funciones cuyo argumento tiene `position=null` por defecto, eso puede seleccionar el inicio del muro en vez de proyectar el punto del clic. Es un defecto a corregir antes de ampliar las herramientas de aberturas.

### 4.4 Remapeo de anfitrión

Existe remapeo al dividir/reconciliar muros. El fallback de la vista manual elige un muro paralelo cercano por propietarios y proyección, sin comprobar en ese tramo una distancia máxima o empate. Debe declarar asociación incierta cuando no exista un anfitrión inequívoco y revocar confirmaciones dependientes.

### 4.5 Métrico y raster

El modo plano dibuja muros desde segmentos raster; las aberturas usan coordenadas de muro y dimensiones métricas. Debe existir una transformación única por nivel antes de habilitar todas las herramientas sobre el plano. No basta con multiplicar unas entidades y dejar otras en metros.

### 4.6 Motor y cobertura actual

La derivación nueva cubre condicionalmente CIM-002, CIM-005, CIM-006, CIM-008 y ACA-001. No existe aún derivación automática completa de todas las entidades arquitectónicas a los 101 conceptos.

También deben validarse las hipótesis del adaptador actual: una selección global de mampostería no prueba que cada divisorio sea portante; un piso no es automáticamente cerámico. Material y rol por entidad deben restringir las propuestas y sus exclusiones antes de declarar esa cobertura consolidada.

## 5. Contrato común: datos que 04 debe producir

### 5.1 Identidad y coordenadas

- Proyecto, revisión y hash del modelo geométrico.
- Versiones del schema, reglas y catálogo.
- Metros como unidad canónica; raster únicamente para visualización y referencia.
- Niveles con ID, orden, elevación y altura.
- Origen XY común o transformaciones explícitas entre niveles.
- Estados `UNKNOWN`, `PROPOSED`, `CONFIRMED`, `CONFLICT`, `EXCLUDED`.
- Evidencia por entidad y procedencia de cada valor derivado o corregido.

El schema de esta entrega representa entidades en metros y propone `LOCAL_XY_DOWN_Z_UP`. No define todavía el manifiesto completo de imágenes/transformaciones de 03; debe conservarse aparte y vincularse por nivel.

### 5.2 Espacios y superficies

- Polígono exterior y huecos interiores.
- Uso, nivel y relación con muros.
- Estado de intervención: nuevo, conservar, demoler, modificar o desconocido.
- Acabado y material por piso, plafón, azotea o cara de muro.
- Altura de cobertura donde no se recubra toda la superficie.

Áreas y perímetros se calculan en backend desde geometría. No se confunde huella, área construida, piso, plafón y losa.

### 5.3 Muros

- Centerline único, nivel, espesor, altura y material.
- Rol portante/divisorio/desconocido, separado del rol exterior/interior.
- Espacios a cada lado, aberturas y acabados por cara.
- Evidencia y clasificación confirmada o propuesta.

Longitud = distancia métrica entre extremos. Área bruta = longitud × altura solo cuando el modelo sea compatible con esa representación. Muros escalonados o inclinados requieren perfil/superficie explícita, fuera del schema básico de esta entrega.

### 5.4 Aberturas unificadas

Una sola colección `openings` con `kind`:

```text
DOOR / WINDOW / GARAGE_DOOR / OPEN_PASSAGE / UNKNOWN
```

Cada entidad debe llevar anfitrión, intervalo longitudinal, altura, antepecho, uso, material, operación y procedencia. El ancho se deriva de `offsetEndM - offsetStartM`; no debe haber dos anchos contradictorios.

Migración del editor vigente: `position` es la fracción central t sobre el muro. Para longitud L y ancho w:

```text
offsetStartM = t × L − w/2
offsetEndM   = t × L + w/2
```

No se usa un t por defecto para dar por localizado un vano desconocido.

## 6. Identificación de puertas y ventanas

### Desde planos

1. Conservar candidato y evidencia de 03: símbolo, texto, bbox y geometría local.
2. Clasificar familia propuesta con geometría/contexto semántico; Call 2 puede aportar candidatos, pero no confirma dimensiones.
3. Buscar muro anfitrión compatible por nivel, distancia, orientación y extensión.
4. Proyectar límites del vano sobre el eje del muro; un bbox de símbolo no equivale a ancho de apertura.
5. Resolver altura y antepecho desde cotas, cuadros de puertas/ventanas, cortes o definiciones del proyecto.
6. Si hay ambigüedad, mostrar el candidato en 04, pedir resolver esa propiedad y conservar su estado pendiente.
7. Publicar la entidad canónica únicamente con estado y procedencia explícitos.

### Desde dibujo manual

Seleccionar herramienta y muro; colocar por snap/proyección. La familia se conoce por la herramienta, no por una clasificación posterior. El usuario define propiedades que el dibujo 2D no contiene en el inspector de 04. Las medidas dibujadas se reutilizan automáticamente en 05.

### Puertas

Campos faltantes en UI/modelo: uso interior/principal/vehicular, operación, número de hojas, material y evidencia de especificación. El arco puede sugerir abatimiento, pero no determina material ni altura.

### Ventanas

Campos faltantes: operación, material de marco, acristalamiento y revisión vertical. El antepecho no debe asumirse cero por ausencia del dato.

## 7. Puertas de garaje / portones

Se propone familia explícita `GARAGE_DOOR`; no inferirla solamente por ancho o por estar cerca de un espacio llamado cochera.

Evidencias útiles: acceso vehicular, símbolo de apertura, cuadro de cancelería/herrería y anotación del plano. Operaciones propuestas: corrediza, abatible, plegable, seccional, enrollable o basculante; se conserva `UNKNOWN` cuando no se conoce.

Datos mínimos para geometría: nivel, ancho/intervalo, altura y anfitrión. Un portón puede pertenecer a un muro o a un límite/acceso exterior; por eso el contrato permite `hostWallId` o `hostBoundaryId`. El segundo requiere implementar también el registro de límites, que aún no está formalizado en el schema básico.

Un portón con puerta peatonal integrada no debe contarse como dos aberturas de muro. La hoja peatonal sería un componente del mismo conjunto; ese modelo de componentes también queda pendiente.

**Catálogo local:** no se encontró concepto específico de portón de garaje. Debe publicarse `catalogStatus=UNMAPPED`, conservar su geometría y permitir el descuento del vano válido cuando proceda. No asignarlo automáticamente a CAR-002, CAN-002 o HER-001. La incorporación de un concepto requiere definición de identidad, unidad, regla y PU.

## 8. Escaleras

La escalera es una conexión entre niveles, no solo un área de habitación.

Debe identificarse con un ID único aunque aparezca en dos planos. Su reconocimiento utiliza huellas, dirección de ascenso, descansos, etiquetas y alineación entre plantas. Un candidato `STAIR_HANDRAIL` se vincula como componente/barandal, no como segunda escalera.

Herramientas faltantes en 04:

- Crear escalera recta, L, U o geometría personalizada.
- Asignar niveles origen/destino y sentido de ascenso.
- Editar tramos, ancho, descansos y huellas/contrahuellas.
- Vincular el hueco de losa del nivel superior y los tramos de barandal.
- Mostrar simultáneamente plantas relacionadas y la altura a salvar.

Datos canónicos propuestos:

```text
levelFromId / levelToId / totalRiseM
footprintM / shape / system
flights[]: pathM, widthM, riserCount, riserHeightM, treadDepthM
landings[] / slabVoidIds[] / railingIds[]
```

La diferencia de elevaciones permite obtener altura a salvar; si faltan cotas no se usa una altura típica. Si se conocen altura total y número de contrahuellas puede derivarse su altura; no se escoge automáticamente una configuración normativa.

La superficie en planta de una escalera no equivale a volumen de concreto. Sección, espesor, sistema y armado determinan cálculos distintos. Barandales se miden por su recorrido correspondiente, no siempre por proyección horizontal.

**Catálogo local:** no hay un concepto de escalera completa. HER-002 puede corresponder a barandal metálico cuando esté especificado, pero no cubre toda la escalera. Una descomposición en concreto, acabados o acero debe definirse en reglas específicas y evitar duplicar losas/descansos.

## 9. Traducción al motor: qué debe derivarse automáticamente

| Elemento confirmado y especificado | Entrada del motor | Conceptos relacionados |
|---|---|---|
| Ventanas de aluminio con vidrio, ancho y alto | `dimensiones_ventanas_confirmadas` | CAN-001 |
| Puerta interior de madera | `cantidad_piezas_confirmada` por concepto | CAR-001 |
| Puerta principal con especificación aplicable | `cantidad_piezas_confirmada` por concepto | CAR-002 |
| Puerta de aluminio y cristal | `cantidad_piezas_confirmada` por concepto | CAN-002 |
| Vanos válidos ligados a muros | `vanos` y perímetros de vano | ALB-001/002/003/004/006, ACA-003 y otros según regla |
| Barandal metálico con recorrido | `tramos_barandal_confirmados` | HER-002 |
| Piso con acabado especificado | Área de superficie por concepto | ACA-001 o ACA-101, con exclusión por superficie |
| Cara de muro con material y acabado | Área bruta y descuentos por cara | Albañilería/acabados según clasificación |
| Losa y hueco de escalera | `poligono_losa`, `huecos_no_losa`, sistema | EST-005/006/007 |

Esta tabla es la especificación de conexión pendiente para esas familias; no declara que todos esos mapeos estén implementados en el adaptador actual.

Reglas esenciales:

- Resolver por identidad/propiedades, nunca solo por el nombre de la colección.
- Mantener exclusión entre conceptos que describen el mismo elemento: una puerta principal de aluminio no debe generar dos suministros sin una regla explícita que los separe.
- Descontar un mismo vano una sola vez de cada superficie aplicable.
- Unir superficies de descuento superpuestas en el plano local del muro antes de restarlas.
- Un elemento sin correspondencia de catálogo no desaparece y no recibe un código aproximado.
- Las reglas de cantidad no utilizan el precio para decidir material, geometría o activación.

## 10. Información que falta por grupos del motor

La matriz JSON enumera todos los requisitos exactos. Resumen de responsabilidad:

| Grupo | Geometría o entidades necesarias | Definiciones adicionales |
|---|---|---|
| Preliminares | Polígono de intervención, excavación, superficies topográficas, demolición | Profundidad, nivel de proyecto, reutilización/material apto y trámites |
| Cimentación | Ejes de apoyo, polígonos de zapata/plantilla, dados, rellenos | Sistema, secciones, dimensiones y clasificación portante |
| Estructura | Castillos/columnas/trabes únicos, losas y huecos | Alturas, secciones, sistema, despiece/peso de acero |
| Albañilería | Muros por material, caras, vanos y castillos | Sistema por muro y tratamientos por cara |
| Acabados | Pisos, plafones, fachadas, azoteas, zonas húmedas | Acabado por superficie, altura de recubrimiento y exclusiones |
| Carpintería/cancelería/herrería | Aberturas, conjuntos y recorridos | Uso, material, componentes y correspondencia de catálogo |
| Instalaciones | Muebles, salidas, puntos y equipos | Servicio activo, tipo de salida, especificación y alcance |
| Complementarios | Andadores, guarniciones y equipamiento | Material, intervención y tipo de elemento |

Contar un baño no prueba automáticamente cuántos inodoros, contactos, regaderas o lavabos contiene. Las propuestas deben tener una regla declarada y permanecer propuestas hasta su revisión.

## 11. Herramientas e inputs prioritarios de 04

### Prioridad 0: cerrar el contrato

1. Unificar esquema del editor manual y del plano analizado.
2. Conservar todos los campos a través de normalización, store, guardado y reapertura.
3. Validación de completitud por familia/nivel; `[]` no significa ausencia confirmada cuando la familia está `UNKNOWN`.
4. Un solo pipeline de derivación en backend, compartido por simulación y consolidación final.
5. Panel de problemas con entidad, motivo, concepto afectado y acceso al inspector correspondiente.

### Prioridad 1: elementos frecuentes

1. Inspector común de aberturas con tipo, anfitrión, dimensiones, material y operación.
2. Herramienta de portón y revisión de candidato ambiguo.
3. Herramienta de escalera por tramos, niveles y hueco de losa.
4. Material y rol estructural por muro; no mezclar interior/exterior con portante/divisorio.
5. Validación de ancho/altura/antepecho y solapamiento en dos dimensiones.

### Prioridad 2: ampliar cuantificación

1. Superficies y acabados por cara.
2. Losas, huecos y doble altura.
3. Elementos estructurales y cimentación respaldados por proyecto.
4. Muebles y puntos de instalaciones.
5. Intervenciones de demolición/conservación/remodelación por entidad.

Estos inputs pertenecen al modelo de 04 o a las definiciones de 02; 05 no debe solicitar escribir `geometria_area_confirmada` ni otros nombres internos del motor.

## 12. Validaciones semánticas adicionales al JSON Schema

El schema valida estructura de datos, no toda la semántica. Antes de calcular deben comprobarse:

- IDs únicos y referencias existentes.
- Unidades finitas y coherentes; no usar `Number(null)` como dato conocido.
- Polígonos válidos, huecos interiores y límites de nivel correctos.
- Un único centroline por muro físico o conflicto explícito.
- 0 ≤ offsetStart < offsetEnd ≤ longitud del anfitrión.
- Antepecho + altura compatible con altura del muro.
- Nivel del anfitrión igual al de la abertura.
- Exactamente un anfitrión válido, salvo candidato sin resolver.
- Puerta vehicular no reclasificada por tamaño solamente.
- Niveles distintos y elevaciones compatibles para escalera; coherencia de tramos y altura total.
- Completitud antes de interpretar listas vacías como cero descuentos.
- Dedupe de elementos observados en varios documentos/niveles.
- Al cambiar geometría, revisar anfitriones, descuentos y confirmaciones dependientes.

El schema permite valores null y estados pendientes porque debe transportar también modelos incompletos. Validar contra el schema no equivale a estar listo para calcular todos los conceptos.

## 13. Plan de prueba de aceptación

1. Mismo proyecto creado manualmente y recibido de plano produce entidades y cantidades equivalentes.
2. Puerta: mover/dividir muro conserva su asociación o la marca pendiente; nunca la reubica silenciosamente en otro muro.
3. Ventana: área = ancho × alto cuando ambos están resueltos; antepecho no se usa como altura.
4. Puerta y ventana a distintas alturas: validar superposición real en la cara del muro.
5. Portón: se visualiza, descuenta su vano una vez y permanece sin precio si no hay concepto aplicable.
6. Escalera dibujada en dos plantas: un ID y un conjunto físico; hueco de losa descontado una sola vez.
7. Cambiar nivel o altura actualiza medidas derivadas e invalida las propuestas afectadas.
8. Modelo parcial: muestra datos desconocidos y bloquea únicamente conceptos dependientes, sin convertirlos en cero.
9. Guardar/reabrir conserva tipos, material, fuentes, IDs, revisión y relaciones.
10. Simulación y consolidación final utilizan la misma revisión y producen las mismas cantidades antes de ajustes trazados del usuario.

## 14. Evidencia y límites de esta revisión

Se inspeccionaron `04DisenoViviendaView.vue`, `04DisenoManualView.vue`, `QuantiaPlanEditor.vue`, `editorSchema.js`, `openings.js`, `constraints.js`, `serialization.js`, `viviendaStoreAdapter.js`, `spatialWorkflowContract.js`, `call2_schema.py`, `spatial_quantity_inputs.py` y `motor_simulation_service.py`, además de las reglas locales V1.8.

Se generó la matriz completa de 101 conceptos y se comprobaron estructura JSON y referencias internas del schema. No se ejecutó una nueva prueba de interacción en navegador ni se modificó el catálogo o la base de datos.

Esta entrega fija el contrato y la lista concreta de faltantes. Las nuevas herramientas de portones, escaleras y clasificación de materiales siguen pendientes de implementación; no se presentan como funcionalidades ya disponibles.
