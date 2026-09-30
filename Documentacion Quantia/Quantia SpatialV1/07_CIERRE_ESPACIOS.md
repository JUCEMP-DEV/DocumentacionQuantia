# Cierre lógico y generación de espacios

[[06_CALL2_Y_VALIDACION|← Anterior]] · [[00_INDICE|Índice]] · [[08_CORRELACIONES|Siguiente →]]

## Objetivo

Cerrar espacios sin una nueva llamada IA.

---

## Canonicalización residual

### Objetivo

Eliminar caras/paralelos todavía presentes.

### Correlación

```text
misma orientación
+ separación compatible con espesor
+ solape longitudinal
+ contexto compatible
= mismo muro físico
```

### Estado

**EXPERIMENTAL**

---

## Junction Snap

### Objetivo

Conectar extremos que representan la misma intersección.

### Prioridad

```text
intersección geométrica real
→ orientación dominante
→ proyección H/V
→ tolerancia según escala
```

No debe crear diagonales artificiales sólo porque dos extremos estén cerca.

### Estado

**PENDIENTE DE CIERRE**

---

## Logical Gap Closure

Los openings no deben convertirse en muro físico.

```text
GEOMETRÍA FÍSICA
muro ─────      ───── muro

GEOMETRÍA TOPOLÓGICA
─────────────────────
```

La segunda línea existe sólo para cerrar el espacio.

### Estado

**EXPERIMENTAL FUNCIONAL**

---

## Polygonize

### Entrada

- centerlines físicas;
- cierres lógicos;
- perímetro.

### Proceso

- construir red topológica;
- polygonizar;
- descartar slivers;
- relacionar polígonos con muros.

### Salida

Polígonos candidatos de espacio.

### Herramientas

- Shapely
- `LineString`
- `unary_union`
- `snap`
- `polygonize`
- `polygonize_full`

---

## Validaciones

### Conservación del footprint

```text
área cerrada ≈ área arquitectónica base
```

No usar automáticamente área total del terreno.

### Cobertura

```text
suma de espacios válidos / footprint
```

### Balance geométrico

```text
espacios + muros + patios/vacíos ≈ footprint
```

### Dangles

Medir longitud total de extremos abiertos.

### Protección perimetral

Una eliminación que rompe perímetro debe volver a REVIEW salvo evidencia fuerte.

### Separación semántica

```text
espacios semánticos detectados
↔
polígonos geométricos
```

## Estado experimental observado

Miguel V PB:

```text
Cobertura geométrica aproximada: 99 %
Separación semántica: ~33 %
```

Conclusión:

> El footprint puede cerrarse, pero todavía faltan particiones interiores correctas.
