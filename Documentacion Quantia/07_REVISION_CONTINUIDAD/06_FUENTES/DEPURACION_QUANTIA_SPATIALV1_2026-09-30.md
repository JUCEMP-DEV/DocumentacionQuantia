# Depuración del paquete Quantia SpatialV1 — 2026-09-30

[[../00_INDICE|← Revisión y continuidad]]

## Objetivo

Separar el árbol operativo vigente de históricos, probes, outputs anteriores, checkpoints y archivos generados, sin perder trazabilidad.

## Punto de retorno

ZIP original:

`QuantiaSpatialV1.zip`

SHA256:

`104caaafdf5fea667fe9e80892e1319a1251460cb7ac27f481f22f27c0a41597`

## Resultado

- 226 movimientos hacia `REFERENCIAS_PROCESO/`.
- 161 archivos Python operativos comparados contra el ZIP original: sin cambios.
- Código productivo modificado: ninguno.
- Probes Qwen/Hugging Face retirados de la suite activa: 2.
- `experiments/` separado como referencia.
- caches Python separados del árbol operativo.

## Estructura de referencias

```text
REFERENCIAS_PROCESO/
├── 01_DOCUMENTACION_HISTORICA/
├── 02_CHECKPOINTS_MANIFIESTOS/
├── 03_PRUEBAS_PROBES_HISTORICOS/
├── 04_OUTPUTS_HISTORICOS/
├── 05_EXPERIMENTOS/
└── 99_CACHE_GENERADO/
```

## Evidencia vigente conservada

- `canonical_integrity_v2`
- `canonical_visual_review`
- `quantia_spatial_v1_process`
- `call2_interface04/gemini`
- `space_closure_probe_v1`
- `space_closure_constraint_probe_v2`

## Separado como referencia

- históricos/versionados anteriores;
- return points y manifests antiguos;
- checkpoints empaquetados;
- outputs Adaptive antiguos;
- Call 2 V2 anterior;
- pruebas/output Qwen-HF;
- Groq/offline de Interface04;
- código `experiments/`;
- caches Python.

## Validación

- `compileall`: PASS.
- El paquete aislado no puede completar toda la colección pytest porque depende de `app.core`, externo a este ZIP.
- El ZIP original aislado presenta la misma limitación.
- Original: 104 tests recogidos, 18 errores de importación por entorno.
- Depurado: 104 tests recogidos, 16 errores; los 2 errores menos corresponden a los 2 probes Qwen/HF archivados.
- No se ejecutaron proveedores ni llamadas de red.

## Paquete depurado

SHA256:

`f8984294d5700856a3c15df306e7fdbe866bcc3d2c0bdc00759d7011975a8d23`

Incluye:
- `documentation/CLEANUP_MANIFEST_2026-09-30.json`
- `documentation/DEPURACION_2026-09-30.md`
- `documentation/STRUCTURE_TREE_CURRENT.txt`
- `.gitignore`

## Política para el futuro repo Quantia SpatialV1

El repo técnico debe usar como fuente principal código, tests, schemas y evidencias vigentes.

Los outputs históricos pesados y caches quedan como referencia local y no deben crecer dentro del historial Git salvo necesidad explícita.
