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
