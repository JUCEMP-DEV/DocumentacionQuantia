# Implementación del editor 04 y consumo de 05

Fecha: 29 de septiembre de 2026.

## Flujo implementado

Con planos: **01 → 02 → 03 → archivo `QUANTIA_03_04_V1` → 04 → consumo `QUANTIA_04_05_V1` → 05**.

Manual: **01 → 02 → 04 → consumo `QUANTIA_04_05_V1` → 05**. Esta ruta no necesita ejecutar 03 ni llamar a un modelo de IA.

La fase 02 ya dirigía el modo `dibujar` a 04. Se conserva esa navegación. El editor obtiene del proyecto los niveles, alturas explícitas y terreno definido; no deduce elevaciones superiores a partir de una altura promedio. Conserva además el contexto de alcance, intervención y sistemas constructivos. El indicador de pasos omite 03 cuando se eligió dibujo manual.

04 utiliza el mismo modelo métrico para ambos orígenes. La vista de revisión del plano conserva el raster y la evidencia; su botón **Abrir editor métrico común** abre las herramientas completas una vez que existen coordenadas métricas. Internamente se reutiliza `04DisenoManualView.vue`, conservando `sourceMode=plan` cuando corresponde. La coincidencia del nombre del archivo con el modo manual no cambia la procedencia del modelo.

## Archivos de consumo

### Entrada de 03

`buildPhase03Delivery()` reúne el resultado espacial y los contextos de 01/02 en un solo sobre. La persistencia de la vista 03.2 ya usa ese sobre. Su recepción conserva una copia en `estructuraEspacial.intake03`, incluyendo candidatos, gráficos excluidos y demás evidencia disponible.

- [Schema de entrada 03 → 04](../CONTRATOS/CONSUMO_03_04_V1.schema.json).
- En 04: **Importar archivo único de 03** y **Descargar origen de 03**.
- Importar sustituye el modelo de la sesión tras una confirmación si ya existe geometría. No sustituye silenciosamente las decisiones actuales de 01/02; el contexto original permanece en `intake03`.
- Los proyectos legacy siguen siendo legibles por el adaptador. La importación de archivo requiere el nuevo sobre y geometría métrica; un bbox o segmento raster no basta.

Esto no reemplaza el motor de análisis de 03 ni conecta por sí mismo el runner experimental de Call 2 a una nueva ruta productiva. No se modificaron F03, Mask, Topology ni la baseline PostFilter V4.

### Entrega de 04

**Validar y descargar consumo de 05** llama a `POST /api/motor/consumo-04` y descarga `QUANTIA_04_05.json`.

- [Schema runtime 04 → 05](../CONTRATOS/CONSUMO_04_05_RUNTIME_V1.schema.json).
- [Ejemplo sintético de consumo](../CONTRATOS/EJEMPLO_MANUAL_04_05_V1.json).

Incluye:

| Campo | Contenido |
|---|---|
| `schemaVersion`, `units` | `QUANTIA_04_05_V1`, metros |
| `modelSha256` | Hash determinista del modelo, completitud y contexto de cálculo |
| `sourceMode`, `revision` | Procedencia y revisión disponible |
| `context01`, `context02` | Datos generales, alcance y sistemas constructivos |
| `estructuraEspacial` | Modelo íntegro, alias compatibles, fuentes y evidencia conservada |
| `openings` | Vanos con intervalo métrico, validación y correspondencia de catálogo |
| `stairs` | Escaleras con desnivel recalculado, tramos, huecos vinculados y barandales |
| `derivedInputsByConcept` | Mediciones que efectivamente pudo resolver el backend |
| `derivationTrace` | Regla aplicada e IDs de origen por concepto |
| `issues` | Entidades pendientes, motivo y fase donde resolverlas |
| `status` | Pendiente de revisión o listo para evaluación de reglas; no certifica presupuesto completo |

La exportación y `simulate_modulo()` llaman a **la misma función `build_consumption()`**. La consolidación de resultados ya llama a `simulate_modulo()`, por lo que comparte esa derivación. 05 sigue enviando el estado actual al backend: no es necesario descargar y volver a subir el archivo para calcular.

Las mediciones derivadas no se almacenan como entradas manuales permanentes: se recalculan en cada simulación. Las entradas explícitas previamente existentes conservan su precedencia según el motor vigente.

El schema anterior `QUANTIA_04_05_CANONICAL_V1_DRAFT` permanece como propuesta de diseño más amplia. **No es el schema runtime activado en esta entrega.** Se eligió un sobre compatible que conserva el estado espacial existente, evitando una migración destructiva de proyectos. El antiguo botón de borrador en la vista del plano se sustituyó por la entrega común.

## Herramientas añadidas y corregidas

### Inspector constructivo

Se selecciona el elemento en el lienzo o en la lista **Información constructiva para 05**. Permite **Guardar pendiente** o **Confirmar propiedades**.

- Muros: material, función portante/divisoria, espesor y altura propia o heredada del nivel.
- Puertas: familia, anfitrión, uso, material, apertura, hojas, posición y dimensiones.
- Ventanas: anfitrión, material, apertura, acristalamiento, posición, dimensiones y antepecho.
- Espacios: intervención y acabado de piso cerámico, sin acabado o pendiente. La confirmación de geometría y uso sigue en el inspector del espacio.
- Escaleras: niveles, sistema, forma, tramos, descansos y relaciones descritas abajo.

### Portones

La herramienta **Portón** coloca un vano `GARAGE_DOOR` sobre un muro y lo identifica como acceso vehicular. Su familia no se infiere por ancho. Se representa en naranja y conserva dimensiones y operación al guardar/reabrir.

Se admiten operaciones abatible, corrediza, plegable, seccional, enrollable y basculante. La representación en planta es esquemática; no reproduce el mecanismo completo.

El catálogo vigente no contiene un suministro específico de portón. Se entrega `catalogStatus=UNMAPPED`, con geometría y evidencia disponibles. No se sustituye por una puerta principal ni por una protección de herrería. El registro de portones independientes de un muro y los componentes de una puerta peatonal integrada siguen pendientes.

### Escaleras

**Escalera** dibuja una huella rectangular dentro de un espacio existente. El inspector permite:

1. Elegir niveles de origen y destino con elevaciones explícitas.
2. Definir forma recta, L, U u otra por tramos.
3. Agregar tramos mediante sus puntos de inicio/final, ancho y número de contrahuellas.
4. Agregar descansos editando sus vértices.
5. Vincular un hueco de losa en destino usando la huella dibujada.
6. Definir barandal izquierdo, derecho, ambos lados o ausencia explícita y su material.

Se recalculan altura a salvar, contrahuella y huella desde esos datos. Los tramos y descansos se representan en planta. Una entidad de escalera se ve en sus dos niveles, sin crear dos suministros. El backend verifica la huella y que los tramos quepan en ella.

El hueco vinculado queda en la entrega para la futura losa; **no se afirma que ya se cuantifique una losa completa ni su concreto/armado**. No hay concepto de escalera completa en el catálogo revisado. El barandal metálico por tramo sí produce HER-002 con longitud inclinada calculada en 3D. Los barandales de descansos, retornos y remates requieren geometría adicional y no se añaden por defecto.

### Validaciones e interacción

- `null` dejó de convertirse en posición cero al colocar un vano.
- Un clic sobre un muro llega a la herramienta de abertura activa.
- Es posible dibujar una escalera dentro de un espacio: las figuras existentes ya no bloquean esos eventos.
- Se revisan anfitrión, nivel, intervalo longitudinal, altura y antepecho contra la altura del muro.
- Los solapamientos se evalúan en la cara del muro: una ventana por encima de una puerta puede ser válida.
- Los anfitriones ambiguos se dejan pendientes; no se selecciona silenciosamente un muro paralelo lejano.
- Se conservan vanos huérfanos al reabrir para poder reasociarlos y revisarlos.
- Cambiar niveles invalida confirmaciones dependientes. Los cambios del editor vuelven parcial la completitud de aberturas/escaleras; debe revisarse nuevamente antes de calcular sus inventarios.

## Derivación disponible

| Datos de 04 y condiciones | Concepto / entrada |
|---|---|
| Polígonos confirmados de planta baja, obra nueva | CIM-008 |
| Ejes confirmados y explícitamente portantes, sistema y cimentación compatibles | CIM-005 y CIM-006 o CIM-002 |
| Piso explícitamente confirmado como cerámico, obra nueva | ACA-001 |
| Ventana de aluminio con vidrio y dimensiones válidas | CAN-001: ancho × alto |
| Puerta interior de madera | CAR-001: una pieza por conjunto |
| Puerta principal de madera | CAR-002: una pieza por conjunto |
| Puerta de aluminio y vidrio | CAN-002; no duplica CAR-002 |
| Barandal metálico de tramo de escalera confirmado | HER-002: longitud inclinada |

Las nuevas derivaciones arquitectónicas se limitan a obra nueva. Una remodelación requiere alcance por elemento antes de medir toda la vivienda. El inventario de aberturas debe estar revisado y sus entidades válidas/confirmadas; no se presenta un conteo parcial como si fuera completo.

Se corrigieron dos supuestos anteriores: **un muro no es automáticamente portante** y **todo piso no es automáticamente cerámico**. Los proyectos anteriores pueden requerir confirmar esas propiedades en 04.

## Ejercicio manual

1. En **01**, define el proyecto, intervención y alcance.
2. En **02**, selecciona sistemas constructivos y **Dibújalo tú**.
3. En **04**, revisa niveles y alturas. Define elevaciones si habrá escaleras.
4. Dibuja espacios, asigna sus usos y confirma su geometría.
5. Selecciona cada muro y confirma material, espesor y función estructural.
6. Coloca puertas, ventanas y portones con clic sobre sus muros. Completa y confirma sus propiedades.
7. Dibuja las escaleras necesarias, configura sus tramos y revisa niveles/huecos/barandales.
8. Especifica el acabado de piso de cada espacio.
9. Marca **Ya dibujé todas las puertas, ventanas y portones** y **Ya dibujé todas las escaleras (o no existen)** al terminar. Un inventario vacío no se interpreta como ausencia confirmada antes de esa revisión.
10. Pulsa **Validar y descargar consumo de 05** para inspeccionar datos y pendientes. Guarda el proyecto con **Guardar proyecto** si necesitas conservarlo en tu perfil; descargar un JSON no lo guarda en Supabase.
11. Continúa a **05** y genera cantidades. Los problemas de entidades aparecen con sus IDs y fase de resolución. No hay que escribir manualmente las entradas internas del motor en 05.

La prueba ficticia usa un espacio de 12 × 6 m, una puerta interior de madera, una puerta de aluminio y vidrio, una ventana de 1.20 × 1.00 m, un portón y una escalera con desnivel de 3 m y recorrido horizontal de 4 m.

## Comprobaciones realizadas

- **16 pruebas frontend aprobadas**: contrato, recepción de 03, configuración manual desde 01/02, conservación al reabrir, vanos, escaleras, compatibilidad de plano y proyectos.
- **10 pruebas backend aprobadas**: derivación, exclusiones por identidad de puerta, inventarios incompletos, duplicados, solapamientos, acabado explícito, rol portante y correspondencia exportación/simulación.
- **Build de producción Vite aprobado.**
- **Navegador Chrome con perfil aislado:** montaje del editor, dibujo real de portón sobre muro, dibujo de escalera dentro de un espacio, edición/confirmación de portón y desnivel derivado. Sin errores JavaScript registrados.
- **HTTP real con el catálogo configurado:** `/api/motor/consumo-04` respondió 200. Simulación de acabados propuso ACA-001 = 72 m². Simulación de complementarios propuso CAR-001 = 1 pieza, CAN-001 = 1.2 m², CAN-002 = 1 pieza y HER-002 = 5 m.
- No se guardó ese ejercicio ficticio en una cuenta, no se escribieron reglas/catálogos en Supabase y no se hicieron llamadas de IA.

Evidencia local:

- [Captura del editor](../../Backend/tests/output/editor04_browser/editor04.png).
- [Prueba de navegador](../../Backend/tests/output/editor04_browser/smoke.json).
- [Respuesta resumida del catálogo real](../../Backend/tests/output/editor04_browser/live_catalog_smoke.json).
- Harness de pruebas: `Frontend/tests/manual04-harness.html`. Con Vite activo puede abrirse en `http://127.0.0.1:5173/tests/manual04-harness.html`. Usa un modelo ficticio aislado y no persiste el estado de una cuenta.

Comandos de regresión, desde `Backend`:

```powershell
.venv/Scripts/python.exe -m pytest tests/test_spatial_consumption.py tests/test_automatic_spatial_inputs.py tests/test_editor_measurement_consumption.py -q -p no:cacheprovider
```

Desde `Frontend`:

```powershell
node --test tests/constructionWorkflow.test.mjs tests/spatialWorkflowContract.test.mjs tests/planEditorView.test.mjs tests/projects.test.mjs
npm.cmd run build
```

El smoke de navegador está en `Backend/scripts/smoke_editor04_browser.py`: requiere Vite en 5173 y un navegador aislado con CDP en 9334. No utiliza el perfil personal del navegador.

## Límites que permanecen

Esta entrega implementa el circuito común y los elementos arquitectónicos frecuentes. **No significa que los 101 conceptos ya puedan obtenerse automáticamente de cualquier dibujo.**

Continúan pendientes las herramientas completas de cimentación/estructura, losas y descuentos entre superficies, acabados por todas las caras, muebles/puntos de instalaciones, intervención detallada en remodelaciones, geometrías de escalera no representables por la huella y los tramos disponibles, y la ampliación formal del catálogo para portones/escaleras completas.

Los vanos válidos quedan disponibles para descuentos, pero esta entrega no activa automáticamente toda la albañilería sin geometría de castillos ni las otras definiciones que exigen sus reglas. El motor conserva bloqueos de conceptos con requisitos faltantes; el ejemplo real también contiene conceptos ajenos a los elementos parametrizados que permanecen bloqueados. Por tanto, no se certifica aquí el cierre de un presupuesto completo de obra con todos los capítulos.

El backend local se recargó para habilitar la nueva ruta. Sigue siendo un proceso de desarrollo, **no un servicio permanente de Windows**. Para iniciarlo desde `Backend`:

```powershell
.venv/Scripts/python.exe -m uvicorn app.main:app --host 127.0.0.1 --port 8000
```

La configuración existente carga `.env`/`.env.local`; no se trasladaron credenciales al frontend ni a estos archivos de consumo.
