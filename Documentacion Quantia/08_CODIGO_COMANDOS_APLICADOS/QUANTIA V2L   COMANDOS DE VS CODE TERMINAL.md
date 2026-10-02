# BACKEND
cd "/d/03 INGENIEIRA SISTEMAS/03 RESIDENCIAS PROFESIONALES/QuantiaDesarrollo/QuantiaV2L/backend"
source .venv/Scripts/activate
python -m uvicorn app.main:app --reload

```
.\.venv\Scripts\Activate.ps1
python -m uvicorn app.main:app --reload --host 127.0.0.1 --port 8001
```
# FRONTEND
Se debe de instalar la libreria para Vue

cd "/d/03 INGENIEIRA SISTEMAS/03 RESIDENCIAS PROFESIONALES/QuantiaDesarrollo/QuantiaV2L/frontend"

Instalar dependencias Instalar Node.js LTS + npm
	winget install -e --id OpenJS.NodeJS.LTS

Levantar los servicios
	npm run dev

# REQUERIMIENTOS BACKEND
cd "/d/03 INGENIEIRA SISTEMAS/03 RESIDENCIAS PROFESIONALES/QuantiaDesarrollo/QuantiaV2L/backend"
source .venv/Scripts/activate
python -m pip install -r requirements.txt
python -m pip check

# REQUERIMIENTOS FRONTEND
cd "/d/03 INGENIEIRA SISTEMAS/03 RESIDENCIAS PROFESIONALES/QuantiaDesarrollo/QuantiaV2L/frontend"
npm install
# o, si package-lock.json ya está correcto:
npm ci