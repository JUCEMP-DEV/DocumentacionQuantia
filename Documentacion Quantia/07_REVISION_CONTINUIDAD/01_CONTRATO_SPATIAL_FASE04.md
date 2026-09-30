# Revisión — Contrato Spatial → Fase 04

[[00_INDICE|← Revisión y continuidad]]

**Fecha:** 2026-09-30  
**Estado:** EN REVISIÓN  
**Objetivo:** identificar lo que falta antes de cerrar el contrato de consumo de Quantia Spatial hacia Fase 04.

> Este documento es de seguimiento. No es el contrato canónico.

## Flujo objetivo

```text
SPATIAL
   ↓
nivel + coordenadas
   ↓
muros
   ↓
espacios
   ↓
openings
   ↓
escaleras
   ↓
evidencia / lineage
   ↓
validation / readiness
   ↓
FASE 04
```

## Información disponible

| Bloque | Información actual | Estado |
|---|---|---|
| Identidad | LevelView, nivel, revisión/versión | DEFINIDO |
| Coordenadas | Raster + métrico | DEFINIDO |
| Plano base | Raster original / referencia visual | DISPONIBLE |
| Muros | ID, centerline raster/métrica, espesor, longitud, rol, confianza, evidencia y linaje | CONSUMIBLE |
| Validación | Integridad, procedencia y estado | DISPONIBLE |
| Call 2 | Correcciones aplicadas/rechazadas, candidatos y unresolved | DISPONIBLE |
| Gaps | Gaps lógicos y referencias a muros | DISPONIBLE |
| Espacios | Polígono raster/métrico, boundary walls y área | EXPERIMENTAL |
| Puertas/ventanas | Candidatos visuales | PENDIENTE PARAMETRIZACIÓN |
| Escaleras | Región/candidato | PARCIAL |
| Ejes/cotas | Evidencia existente | PENDIENTE CORRELACIÓN |

## Pendientes para cerrar el contrato

### 1. Muros

Validar:
- particiones interiores faltantes;
- muros reales eliminados;
- paralelos/caras residuales;
- junctions finales.

**Estado:** PARCIAL.

### 2. Espacios

Resolver:

```text
SPACE semántico
↕
polygon
↕
boundaryWallIds
↕
dimensiones / área
```

**Estado:** EXPERIMENTAL.

### 3. Cotas

Correlacionar cada `DIMENSION` con:
- muro correcto;
- espacio correcto;
- orientación;
- unidad;
- evidencia.

**Estado:** PENDIENTE.

### 4. Openings

Convertir candidato visual en elemento parametrizado:

```text
type
host_wall_id
offset_start
offset_end
opening_width
center_on_wall
orientation
confidence
```

**Estado:** PENDIENTE.

### 5. Escaleras

Resolver:
- geometría final;
- eje/tramo;
- dirección;
- landings;
- nivel origen/destino.

**Estado:** PARCIAL.

### 6. Validation / Readiness

Definir cuándo Fase 04 puede considerar cada bloque:
- válido;
- review;
- experimental;
- incompleto.

**Estado:** PENDIENTE DE CIERRE.

### 7. Schema único

Consolidar las denominaciones actuales:

```text
SPATIAL_INTERFACE04_REVIEW_V1
QUANTIA_03_04_V1
estructuraEspacial
```

en un único contrato canónico Spatial → 04 con un solo `schemaVersion`.

**Estado:** PENDIENTE.

## Orden de revisión

```text
MUROS
→ ESPACIOS
→ COTAS
→ OPENINGS
→ ESCALERAS
→ VALIDATION / READINESS
→ SCHEMA FINAL
```

## Criterio de cierre

El contrato no se considera terminado hasta que:
- la geometría sea coherente con el plano;
- los elementos estén correlacionados;
- raster y métrico estén alineados;
- exista trazabilidad;
- el consumidor 04 pueda distinguir datos válidos de experimentales;
- exista un único schema/version contractual.
