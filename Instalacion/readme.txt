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


