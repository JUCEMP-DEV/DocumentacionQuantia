Instalar requerimientos
1. Entrar al backend de QuantiaV2L
		cd "/d/03 INGENIEIRA SISTEMAS/03 RESIDENCIAS PROFESIONALES/QuantiaV2L/Backend"

2. Verificar Python 3.13
		python --version

Si "python" no responde pero tienes el launcher de Windows:
		py -3.13 --version

3. Crear entorno virtual
		py -3.13 -m venv .venv

4. Activarlo desde Git Bash
		source .venv/Scripts/activate

5. Actualizar instalador
		python -m pip install --upgrade pip setuptools wheel

6. Instalar TODOS los requirements actuales de QuantiaV2L
		python -m pip install -r requirements.txt

7. Instalar dependencias que QuantiaSpatialV1 utiliza actualmente
y que todavía NO están declaradas en Backend/requirements.txt
		python -m pip install pymupdf opencv-python shapely pytest

8. Verificar las dependencias importantes
		python -c "import pydantic, pymupdf, cv2, numpy, shapely, PIL, pytesseract, requests; print('DEPENDENCIAS SPATIAL OK')"

9. Verificar pytest
		python -m pytest --version

10. Verificar que QuantiaSpatialV1 sea importable desde QuantiaV2L
		python -c "from app.quantia_spatialV1.engine import QuantiaSpatialEngine; print('QUANTIA SPATIAL IMPORT OK')"
# Comandos para Backend
	 Fast Api
	 pip install fastapi uvicorn
	uvicorn main:app --reload   
	 estable el entorno si ya esta instalado
		 .\.venv\Scripts\Activate.ps1 
	 