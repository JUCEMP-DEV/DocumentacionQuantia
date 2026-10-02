# 1. Instalar Python 3.13
winget install -e --id Python.Python.3.13

# 2. Cierra COMPLETAMENTE VS Code y vuelve a abrirlo.

# 3. Verificar instalación
python --version
python -m pip --version

# 4. Crear entorno virtual
python -m venv .venv

# 5. Permitir activación solo para esta terminal
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass

# 6. Activar entorno
.\.venv\Scripts\Activate.ps1

# 7. Actualizar herramientas base
python -m pip install --upgrade pip setuptools wheel

# 8. Instalar requirements de QuantiaV2L
python -m pip install -r requirements.txt

# 9. Dependencias adicionales usadas por QuantiaSpatialV1
python -m pip install pymupdf opencv-python shapely pytest

# 10. Verificación
python -c "import pymupdf, cv2, numpy, shapely, pydantic, PIL, pytesseract, requests; print('DEPENDENCIAS SPATIAL OK')"

python -m pytest --version