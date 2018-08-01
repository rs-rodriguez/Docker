#Para mayor informacion visar el link siguiente.
https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-using-the-repository

# Commandos necesarios para instalar docker
# install certificates
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
# installacion de las keys necesarias.
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
# verifica las llaves
sudo apt-key fingerprint 0EBFCD88
# para ubunto instalamos los repos.
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
# (lsb_release -cs) hace mencion a la version de la distro
# Instalamos docker version para la comunidad.
sudo apt-get install docker-ce
# revisar version de docker
	docker --version

# informacion de docker.
	docker info

# Agregar el usuario al grupo de docker
	sudo usermod -aG docker (username)

# Correr un contenedor
	docker container run hello-world

# muestra la ayuda del comando system
	docker system --help
# muestra el espacio utilizado por docker en el systema
	docker system df

# Los conenedores tienen estados a diferencia de las imagenes
# los cuales son:
# Iniciados, pausados, detenidos, terminados; los contenedores, cambian su contenido a traves de sus ejecusion.

# Acciones en las imagenes y en contenedores:
# Las imagenes tinen: 			Los contenedores:
#	halar - Pull				Ejecutar - Run
#	Empujar - Push				Detener - Stop
#	Contruir - build			Reiniciar - Restart
#	Salvar - Save				Inspeccionar - Inspect
#	Cargar - Load				Estadisticas - Stats

# Estos mismos comandos los podemos encotrar con 
	docker image --help
	docker container --help

# docker store es donde se almacenan las imagenes.
https://store.docker.com/search?q=&type=image&source=verified
# si queremos buscar imagenes en el repositorio desde consola tecleamos
	docker search ubuntu

# docker tiene definidas etiquetas para poder saber que imagen vamos a descargar
# docker pull ${name:version}
	docker pull ubuntu:18.04

# para ver la ayuda de docker container
	docker container run --help | more

# Formato para ejecutar un contenedor
	#Usage:  docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]

# Run container with docker. junto con el directorio de ubuntu
# crea un contenedor en base una imagen, lista lo que esta en el directorio raiz y se detiene.
	docker container run ubuntu:17.04 ls -la / 

# Listar contenedores que estan en nuestra maquina.
docker container ls --help # muestra la ayuda con respecto a ls.
# Para listar todos los contenedores independiente el estado, usar:
	docker container ls -a
# Comando para correr un contenedor y mostrar todos los procesos del sistema.
	docker container run ubuntu:17.04 top -b
# Para el siguiente linea solo agregaremos para palabra --detach con la cual prodemos correr una imagen la cual podra seguir viviendo, mientras nosotros realizamos otras acciones.
	docker container run --detach ubuntu:17.04 top -b
# Listar contenedores.
	docker container ls

# Mostrar los log de un contenedor. docker container logs ${nombre_contenedor}
	docker container logs admiring_visvesvaraya
	docker container logs --follow admiring_visvesvaraya # permite ver lo que esta pasando dentro del contenedor, sin detenerse 

# Eliminar un contenedor. docker container rm (name_contenedor)
	docker container rm  admiring_visvesvaraya
# Listar imagenes 
	docker image ls
#	Eliminar imagenes
	docker image rm 

# Las imagenes no se pueden eliminar si estan siendo usados por contenedores, al igual que un contenedor en ejecusion no se puede eliminar.

# Guardar imagenes docker 
	docker imagen save --output ubuntu:18.04 ubuntu:18.04 

# restore imagen (carga de imagen desde el sistema)
	docker image load --input ubuntu:18.04

# Dockerfile es un archivo en el cual ponemos todo lo necesario para crear una imagen para docker.
# crear dockerfile
# instrucciones de contruccion de dockerfile
	vim Dockerfile
# ponemos los siguiente

	FROM ubuntu:18.04
	ENTRYPOINT echo "Bienvenidos al Curso de Docker"

# Luego contrimos la imagen con: el (.) indica donde esta nuestro fichero de dockerfile. 
	docker image build --tag ejemplo .
	docker image build -t ejemplo .	
# Ver Historico de las imagenes 
	docker image history ejemplo
# Modifica el comportamiento de inicio del contenedor.
	docker container run --entrypoint "/bin/ls" ejemplo -la

# Listar contenido de una carpeta 
	docker container run --entrypoint "/bin/ls" ejemplo -la /ejemplo
 

# Buenas practicas, 
	evitar instalar paquetes no necesarios,
	cada contenedor debe cumplir un solo proposito, 
	minimizar el numero de capas como sea posible.
	escribir lineas cortas y utilziar \ para mejorar el rendimiento.
	poner nuestro ficheros en un control de versiones
	Ejemplo:
	RUN apt-get update && apt-get install -y \
	brz \
	cvs \
	git \
	mercurial \
	subversion

#

# Volumenes de datos.
# Los volumenes son estructuras de archivos mantenidos en uno o mas contenedores.

	docker container run --name ejemplo-contenedor -v /home/docker/ejemplo/datos/:/ejemplo -it ubuntu:18.04 /bin/bash
#						damos nombre al contenedor, -Definimos el volumen                    corremos de forma interactiva.

#Utilizando contenedores como volumenes de datos.
#contedor solo para volumenes de datos.
	docker container run --name vdatos -v /datos busybox
# 												instance busybox

# contedor que utiliza otro contenedor como volumen de datos
	docker container run --name app1 --volumes-from vdatos -it ubuntu:18.04 /bin/bash
# en la carpeta datos crear touch datos/fichero1

# para salir del contenedor sin terminarlo es combinacion de teclas (ctrl + p + q)
# se crea aplicacion 2	
	docker container run --name app2 --volumes-from vdatos -it ubuntu:18.04 /bin/bash

# Para Iniciar un contedor -- 
	docker container start app2

# para entrar a un contenedor iniciado utilizar attach
	docker container attach app1
# Salvar la informacion de los volumenes de docker
	docker container run --rm --volumes-from vdatos -v $(pwd):/backup ubuntu:18.04 tar cvf /backup/backup.tar /datos
# Publicar un servicio con docker, primero descargamos nginx
	docker pull nginx:1.15-alpine
# Publicamos el servicio
	docker container run --rm -d --publish-all nginx:1.11-alpine
	docker container run --rm -d -p 8080:80 nginx:1.15-alpine

# para ver lo que esta pasando en la consola.
	docker container logs -f angry_heyrovsky

#Detener contenedor
	docker container stop quirky_ardinghelli

#Desplegamos appWeb
	docker build -t miweb . # construir
	docker container run  --name miweb-contenedor -d -p 8080:80 miweb # publicar

#publicar una imagen en docker cloud, docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
	docker image tag miweb lideralter/miweb
#Luego nos logueamos en docker, con docker login
		docker login 
# pedira el username, password
# hacer push
	docker push lideralter/miweb
# Dockerignore es un fichero que .dockerignore
	# comment
		*/temp*
		*/*/temp*
		temp?
		*.md
		!README.md
# Subiendo a docker cloud
# es posible crear aplicaciones desde github, crearndo las aplicaciones en docker cloud
	docker container run -d -p 80:80 lideralter/gamepink
# versionar nuestras imagenes


# Conectando contenedores, utilizando composer
# Conectar manualmente
	docker pull mariadb
	docker container run --name bd-servidor -e MYSQL_ROOT_PASSWORD=mi-clave -d mariadb
	docker container inspect bd-servidor | grep IP
	docker container run --rm --link bd-servidor:mysql -it mariadb /bin/bash
	mysql -h "$MYSQL_PORT_3306_TCP_ADDR" -P "$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"
# Docker compose
	fichero.yml:
		version: '3'
		services:
		  web:
			build: .
			ports:
			 - "5000:5000"
			volumes:
			 - .:/code
		  redis:
			image: "redis:alpine"
	
# Instalar docker compose 
	https://docs.docker.com/compose/install/#install-compose - documentacion
	sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
	sudo chmod +x /usr/local/bin/docker-compose
	docker-compose --help | more # ayuda en docker compose
	docker-compose.yml
		version: '3'
		services:
		  web:
			image: dockercloud/hello-world
		  lb:
			image: dockercloud/haproxy
			links:
			  - web
			volumes:
			  - /var/run/docker.sock:/var/run/docker.sock
			ports:
			 - 80:80

	docker-compose up -d
	docker-compose ps # listar procesos
	
# Escalando servicios
	docker-compose scale --help
	docker-compose scale web=4 # deprecate
	 docker-compose up --scale web=3 -d # new form


# Docker register
# Podemos tener todas las imagenes privadas que podamos decear.
 docker-compose.yml
	version: '3'

	services:
	  registry:
		 restart: always
		 image: registry:2
		 ports:
		   - 5000:5000
		 volumes:
		   - ./data:/var/lib/registry
# iniciando registry
	docker-compose up -d
# Configurando registry al cliente.
	sudo vim /etc/docker/daemon.json
	{
			"insecure-registries": ["192.168.1.104:5000"]
	}

# reiniciamos docker
	sudo service docker restart
	docker info
	:
	Insecure Registries:
		 192.168.1.104:5000
		 127.0.0.0/8
# Subimos al docker registry
	# primero creamos el tag apartir de otra imagen 
		docker image tag busybox 192.168.1.105:5000/busybox
	# Luego hacemos un docjer push 
		docker image push 192.168.1.105:5000/busybox

# Agreagr interface web for docker registry
docker-compose.yml
	version: '3'
	
	services:
	  registry:
		 restart: always
		 image: registry:2
		 ports:
		   - 5000:5000
		 volumes:
		   - ./data:/var/lib/registry
	  web-registry:
		 restart: always
		 image: konradkleine/docker-registry-frontend:v2
		 ports:
		   - 8080:80
		 environment:
		   - ENV_DOCKER_REGISTRY_HOST=192.168.1.105
		   - ENV_DOCKER_REGISTRY_PORT=5000




	
	 
	

	

	
	





