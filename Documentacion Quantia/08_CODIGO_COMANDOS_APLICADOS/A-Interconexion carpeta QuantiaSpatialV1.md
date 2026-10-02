```
# 2. Crear el enlace tipo Junction
New-Item `
  -ItemType Junction `
  -Path "D:\03 INGENIEIRA SISTEMAS\03 RESIDENCIAS PROFESIONALES\QuantiaDesarrollo\QuantiaV2L\Backend\app\QuantiaSpatialV1" `
  -Target "D:\03 INGENIEIRA SISTEMAS\03 RESIDENCIAS PROFESIONALES\QuantiaSpatialV1"

# 3. Verificar
Get-Item "D:\03 INGENIEIRA SISTEMAS\03 RESIDENCIAS PROFESIONALES\QuantiaDesarrollo\QuantiaV2L\Backend\app\QuantiaSpatialV1" |
    Select-Object FullName, LinkType, Target
```