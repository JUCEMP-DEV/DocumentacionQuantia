- **EJE ↔ tramo real de CENTERLINE — NO IMPLEMENTADO correctamente.** El `AxisGridResolver` localiza ejes, pero actualmente `_axis_segment()` los extiende por todo el `LevelView`. Todavía falta convertirlos en `ViewAxisOccurrence` limitados al tramo donde realmente existe muro. Esto es importante porque ya habíamos definido que un mismo eje puede existir en una planta/tramo y no aparecer donde no existe muro.
    
- **EJES ↔ ESPACIO — NO IMPLEMENTADO.** Gemini ya puede describir un recinto como “entre ejes 1–2 / A–M”, pero esa información sigue siendo texto semántico. No estamos transformando esas referencias en una región geométrica esperada y comparándola con el polígono obtenido.
    
- **COTAS ↔ ESPACIO — NO IMPLEMENTADO.** Las cotas ya ayudan a resolver escala y retícula, pero todavía no validan ancho, largo, área o límites de un `Space`. De hecho, el `ContractSpace` actual ni siquiera posee `dimension_ids`. Una geometría puede cerrar y aun contradecir sus cotas sin que el gate la rechace.
    
- **CENTERLINE ↔ cara interior del muro ↔ área útil — NO IMPLEMENTADO.** Actualmente el cierre trabaja principalmente sobre líneas centrales. Eso sirve para topología, pero el área arquitectónica final debería derivarse de las caras interiores considerando el espesor. Falta separar claramente `topological polygon` de `usable-space polygon`.
    
- **MURO ↔ lado izquierdo/derecho ↔ ESPACIO — PARCIAL.** La reconstrucción conserva conceptos como `left_labels/right_labels`, pero todavía no existe una validación canónica que obligue a que un muro `DIVIDER` tenga recinto interior a ambos lados y un `PERIMETER` tenga interior/exterior. Este enlace puede ser uno de los validadores más fuertes para encontrar divisorios faltantes.
    
- **SEMÁNTICA ↔ TOPOLOGÍA — PARCIAL.** Ya conservamos relaciones `SHARES_WALL_WITH`, `COMMUNICATES_WITH`, `ADJACENT_TO`, etc., pero el Probe actual básicamente asigna semántica a faces mediante centro del bbox y solape. No comprueba que `SHARES_WALL_WITH(A,B)` tenga realmente un muro compartido ni que `COMMUNICATES_WITH(A,B)` tenga una apertura entre ambos. El contrato semántico ya contiene esas relaciones, pero aún no gobiernan la validación geométrica.
    
- **OPENING ↔ host wall ↔ dos espacios — PARCIAL.** `HostWallMatcher` ya existe y puede asociar una propuesta con un muro, pero todavía no cerramos la cadena completa `host_wall_id → offset_start/end → width → space_A/space_B`. Esto coincide con lo que habíamos detectado anteriormente: un opening final necesita quedar parametrizado longitudinalmente sobre su muro, no mediante un bbox arbitrario. chat3
    
- **JUNCTIONS ↔ validación individual — NO IMPLEMENTADO.** Actualmente utilizamos principalmente longitud total de `dangles`. Falta clasificar cada endpoint como `T`, `L`, `X`, continuidad, opening, terminación arquitectónica válida o error. Ahora un extremo abierto solamente contribuye a una métrica global.
    
- **EVIDENCIA NEGATIVA / CONTRADICCIONES — NO IMPLEMENTADO como sistema.** Tenemos confidence y evidencia positiva, pero no una matriz explícita que diga, por ejemplo: “esta hipótesis parece muro, pero contradice eje + cota + semántica + relación de espacios”. Necesitamos que las contradicciones resten soporte, no solamente que las coincidencias sumen.
    
- **PARTICIÓN COMPLETA DEL FOOTPRINT — PARCIAL.** Ya tenemos `area_conservation_ratio`, faces, dangles y detección de subsegmentación. Pero todavía no validamos completamente que `Σ espacios + patios/vacíos/huecos = footprint válido`, sin overlaps, sin zonas inexplicadas y distinguiendo correctamente patios, huecos de escalera, dobles alturas, etc.
    
- **COHERENCIA ENTRE NIVELES — NO IMPLEMENTADO.** PB y PA todavía se validan esencialmente como `LevelView` independientes. Ejes, núcleo de escalera, vacíos y ciertos alineamientos verticales podrían actuar como evidencia cruzada, sin obligar a que ambas plantas sean iguales.
    
- **SPACE VALIDATION GATE completo — NO IMPLEMENTADO.** Este es el faltante principal. El `closure_score` actual solo considera cuatro factores: conservación de área, separación semántica, asignación semántica y calidad de dangles. Todavía no participan ejes, cotas, wall-side, junctions, openings, relaciones semánticas ni coherencia entre niveles.