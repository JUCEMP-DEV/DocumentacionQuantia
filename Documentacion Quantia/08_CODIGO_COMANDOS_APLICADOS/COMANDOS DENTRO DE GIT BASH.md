# Comandos para revisar o cambiar entre Repos
	Se requiere que te encuentres en la direccion del repo de forma local 
		ejemp: "C:----direccion de la carpeta"
			git remote -v
			
	Para reconectar un Repo existente Observacion si el repo esta desactualizado es mejor sobre escribirlo (dentro de gitignore va todo lo que no quiere subir al repo)
		1 Verifica el Path local 
			cat .git
		2 Actualizar el PATH correcto
			printf 'gitdir: /RUTA/CORRECTA/AL/GITDIR\n' > .git
		3 Establecer el main
			git config --global --add safe.directory 'direccion -- Path'
		4 Para sobre escribirlo
			cd "direccion Path"

			# Punto de retorno del puntero roto
			cp .git ../QuantiaV2L_git_pointer_backup.txt
			
			# Eliminar únicamente el .git roto
			rm .git
			
			# Crear repo Git nuevamente
			git init -b main
			
			# Verificar .gitignore antes de agregar
			git status --ignored --short
			
			# Registrar el estado LOCAL actual
			git add .
			git status
			
			git commit -m "baseline: estado local vigente QuantiaV2L"
			
			# Conectar al repo existente
			git remote add origin https: Url del Repo
			
			# Confirmar remoto
			git remote -v
			
			# Sobrescribir main remoto con el estado local vigente
			git push -u origin main --force	
			
		5 Crear el respaldo temporal
			printf '\n# Git local recovery\n.git.bak\n' >> .gitignore
			git status --short  (solo para revisar el status)
		6 subir completamente todo al repo 
				git add .
				git commit -m "baseline: estado local vigente QuantiaV2L"		
		7 Sino se encuentra conectado al repo 
			git remote add origin https:URL del Repo
		8 Registrar el estado y luego remplazar el contrenido
			git fetch origin main
			git rev-parse HEAD
			git rev-parse origin/main
		9 finalmente subimos el baseline
			git push -u origin main --force-with-lease


# Comandos para descargar y actulizar el Repo

	establecerse en la ruta del repo 
	cd "Path c:/---------------------------------"
	
	 Solo si vas a cambiar a otra rama (omitir si estas en la rama main)
	*`git switch main`*

Descargar el main que ya modifique (estado actual en el repo)
	*`git pull origin main`*

Verificar la carpeta histórica
	*`ls archive/spatial-baseline-pre-clean-2026-10-01`*

Eliminar la rama local anterior
	*`git branch -D archive/spatial-baseline-pre-clean-2026-10-01`*

Eliminar también la rama remota de GitHub
	*`git push origin --delete archive/spatial-baseline-pre-clean-2026-10-01`*



# Comandos para subir al Repo Github


1. QUANTIA V2L

	cd "/d/03 INGENIEIRA SISTEMAS/03 RESIDENCIAS PROFESIONALES/QuantiaDesarrollo/QuantiaV2L"

		Verificar rama y cambios locales
		git branch --show-current
		git status --short
		git diff --stat

	Agregar TODO lo generado/modificado localmente
		Los Junction, temporales y archivos definidos en .gitignore no se incluirán.
		git add -A

	Revisar exactamente qué se va a guardar
		git status --short
		git diff --cached --stat

	Crear checkpoint local
		git commit -m "chore: actualiza integración Quantia Spatial y flujo 03.2"
	
	Consultar remoto SIN modificar el estado local
		git fetch origin
	
	Comparar local contra GitHub
		git status -sb
		git log --oneline --left-right HEAD...origin/main
	
	Verificación segura antes del push
		BASE=$(git merge-base HEAD origin/main)
		REMOTE=$(git rev-parse origin/main)
	
		if  "$BASE" = "$REMOTE" ; then
		    echo "OK: origin/main es ancestro del estado local. Push seguro."
		    git push origin main
		else
		    echo "DETENER: origin/main contiene cambios que no están en local o las ramas divergieron."
		    echo "No se realizó push."
		fi
		
	Verificación final
		git status
		git rev-parse HEAD
		git rev-parse origin/main



2. QUANTIA SPATIAL V1


	cd "/d/03 INGENIEIRA SISTEMAS/03 RESIDENCIAS PROFESIONALES/QuantiaSpatialV1"
	
	Verificar rama y cambios locales
	git branch --show-current
	git status --short
	git diff --stat
	
	Agregar cambios locales
	git add -A
	
	Revisar antes de guardar
	git status --short
	git diff --cached --stat
	
	Crear checkpoint local
	git commit -m "chore: actualiza QuantiaSpatialV1 y evidencia de validación"
	
	Consultar remoto sin modificar local
	git fetch origin
	
	Comparar local contra GitHub
	git status -sb
	git log --oneline --left-right HEAD...origin/main
	
	Push únicamente si el remoto sigue siendo ancestro del local
	BASE=$(git merge-base HEAD origin/main)
	REMOTE=$(git rev-parse origin/main)
	
	if  "$BASE" = "$REMOTE" ; then
	    echo "OK: origin/main es ancestro del estado local. Push seguro."
	    git push origin main
	else
	    echo "DETENER: origin/main contiene cambios que no están en local o las ramas divergieron."
	    echo "No se realizó push."
	fi
	
	Verificación final
	git status
	git rev-parse HEAD
	git rev-parse origin/main


============================================================
DOCUMENTACIÓN QUANTIA
Repo: JUCEMP-DEV/DocumentacionQuantia
============================================================

BASE="/d/03 INGENIEIRA SISTEMAS/03 RESIDENCIAS PROFESIONALES"

Localizar el clon sin asumir su ruta
DOCS_DIR=(find "BASE" -maxdepth 4 -type d -name "DocumentacionQuantia" 2>/dev/null | head -n 1)

if  -z "$DOCS_DIR" ; then
    echo "ERROR: no se encontró el clon local DocumentacionQuantia."
    exit 1
fi

echo "Repositorio localizado en:"
echo "$DOCS_DIR"

cd "$DOCS_DIR" || exit 1

echo "=== DOCUMENTACION: VERIFICACION "
git remote -v
git branch --show-current
git status --short
git diff --stat

Confirmar que origin corresponde al repo correcto
git remote get-url origin

Incorporar toda la documentación generada/modificada localmente
git add -A

echo "=== DOCUMENTACION: CAMBIOS A COMMIT "
git status --short
git diff --cached --stat

Punto de retorno documental
if ! git diff --cached --quiet; then
    git commit -m "docs: actualiza estado y trazabilidad de Quantia"
else
    echo "Documentacion: no hay cambios locales para commit."
fi

Consultar remoto sin modificar la copia local
git fetch origin

echo "=== DOCUMENTACION: COMPARACION LOCAL/REMOTO "
git status -sb
git log --oneline --left-right HEAD...origin/main

Push únicamente si origin/main sigue siendo ancestro del local
BASE_SHA=$(git merge-base HEAD origin/main)
REMOTE_SHA=$(git rev-parse origin/main)

if  "$BASE_SHA" = "$REMOTE_SHA" ; then
    echo "OK: push seguro."
    git push origin main
else
    echo "DETENER: DocumentacionQuantia local y remoto divergieron."
    echo "NO se realizó pull, rebase ni push."
fi

echo "=== DOCUMENTACION: HASHES "
git rev-parse HEAD
git rev-parse origin/main
git status -sb