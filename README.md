# Docker Commandos, Ayuda & Consejos

### Comandos mostrar y comandos de administración

```
$ docker
```

### Docker información de versión

```
$ docker version
```

### Mostrar información como el número de contenedores, etc.

```
$ docker info
```

# CONTENEDORES

### Crear una ejecución de un contenedor en primer plano

```
$ docker container run -it -p 80:80 nginx
```

### Crear y ejecutar un contenedor en segundo plano

```
$ docker container run -d -p 80:80 nginx
```

### Nombrar contenedores

```
$ docker container run -d -p 80:80 --name nginx-server nginx
```

### CONSEJO: Lo que hizo

- Busca una imagen llamada nginx en el caché de imágenes
- Si no se encuentra en la memoria caché, busca el repositorio de imágenes predeterminado en Dockerhub
- Bajá la (última versión), almacenada en el caché de imágenes
- Inicia un nuevo contenedor
- Especificamos tomar el puerto 80- en el host y reenviarlo al puerto 80 en el contenedor
- Podríamos hacer "$ docker container run --publish 8000:80 --detach nginx" para usar el puerto 8000
- Podemos especificar versiones como "nginx:1.09"

### Listar contenedores en ejecución

```
$ docker container ls
```

Ó

```
$ docker ps
```

### Listar todos los contenedores (incluso si no se están ejecutando)

```
$ docker container ls -a
```

### Detener el contenedor

```
$ docker container stop [ID]
```

### Detiene todos los contenedores en funcionamiento

```
$ docker stop $(docker ps -aq)
```

### Eliminar contenedor (no se pueden eliminar los contenedores en funcionamiento, primero debe detenerse)

```
$ docker container rm [ID]
```
Ó

```
$ docker rm [ID]
```

### Para eliminar un contenedor en ejecución, usa forzar (-f)

```
$ docker rm -f [ID]
```

### Eliminar múltiples contenedores

```
$ docker rm [ID] [ID] [ID]
```

### Eliminar todos los contenedores

```
$ docker rm $(docker ps -aq)
```

### Obtener logs (Usar nombre o ID)

```
$ docker container logs [NAME]
```

### Lista de procesos que se ejecutan en contenedor

```
$ docker container top [NAME]
```

#### CONSEJO: Sobre contenedores

Los contenedores Docker a menudo se comparan con las máquinas virtuales, pero en realidad son solo procesos que se ejecutan en su sistema operativo host. En Windows / Mac, Docker se ejecuta en una mini-VM para ver los procesos que necesitará para conectarse directamente a eso. Sin embargo, en Linux puede ejecutar "ps aux" y ver los procesos directamente

# COMANDOS DE IMAGEN

### Lista las imágenes

```
$ docker image ls
```
Ó

```
$ docker images
```

### Desplegar imágenes

```
$ docker pull [IMAGE]
```

### Remover imagen

```
$ docker image rm [IMAGE]
```
Ó

```
$ docker rmi [IMAGE]
```

### Remover todas las imágenes

```
$ docker rmi $(docker images -a -q)
```

#### CONSEJO: Sobre imágenes

- Las imágenes son aplicaciones bianarias y dependencias con metadatos sobre los datos de la imagen y cómo ejecutar la imagen.
- Las imágenes no son un sistema operativo completo. Sin núcleo, módulos de núcleo (controladores)
- Host proporciona el núcleo, gran diferencia entre VM

### Algunos ejemplos de creación de contenedores

NGINX:

```
$ docker container run -d -p 80:80 --name nginx nginx (-p 80:80 es opcional ya que se ejecuta en 80 por defecto)
```

APACHE:

```
$ docker container run -d -p 8080:80 --name apache httpd
```

MONGODB:

```
$ docker container run -d -p 27017:27017 --name mongo mongo
```

MYSQL:

```
$ docker container run -d -p 3306:3306 --name mysql --env MYSQL_ROOT_PASSWORD=123456 mysql
```

# INFORMACIÓN DEL CONTENEDOR

### Ver información en contenedor

```
$ docker container inspect [NAME]
```

### Propiedad específica (--format)

```
$ docker container inspect --format '{{ .NetworkSettings.IPAddress }}' [NAME]
```

### Estadísticas de rendimiento (cpu, mem, network, disk, etc)

```
$ docker container stats [NAME]
```

# ACCESO A CONTENEDORES

### Crear nuevo contenedor nginx y bash en

```
$ docker container run -it --name [NAME] nginx bash
```

- i = interactivo mantener abierto STDIN si no está conectado
- t = tty - Abrir solicitud

**Para Git Bash, usa "winpty"**

```
$ winpty docker container run -it --name [NAME] nginx bash
```

### Correr/Crear un contenedor con Ubuntu

```
$ docker container run -it --name ubuntu ubuntu
```

**(no bash porque usa ubuntu bash por defecto)**

### También puede hacerlo para que cuando salga del contenedor no se quede usando el indicador -rm

```
$ docker container run --rm -it --name [NAME] ubuntu
```

### Acceda a un contenedor ya creado, comience con -ai

```
$ docker container start -ai ubuntu
```

### Use exec para editar la configuración, etc

```
$ docker container exec -it mysql bash
```

### Alpine es una distribución muy pequeña de Linux buena para Docker

```
$ docker container run -it alpine sh
```

(usa sh porque no incluye bash)
(alpine usa apk por su gestora de paquetes - puedes instalar bash si quieres)

# REDES

### "bridge" or "docker0" es la red predeterminada

### Obtener puerto

```
$ docker container port [NAME]
```

### Listar red

```
$ docker network ls
```

### Inspeccionar red

```
$ docker network inspect [NETWORK_NAME]
("bridge" is default)
```

### Crear red

```
$ docker network create [NETWORK_NAME]
```

### Crear contenedor en red

```
$ docker container run -d --name [NAME] --network [NETWORK_NAME] nginx
```

### Conectar el contenedor existente a la red

```
$ docker network connect [NETWORK_NAME] [CONTAINER_NAME]
```

### Desconectar el contenedor de la red

```
$ docker network disconnect [NETWORK_NAME] [CONTAINER_NAME]
```

### Separar la red del contenedor

```
$ docker network disconnect
```

# ETIQUETADO DE IMÁGENES & SUBIR A DOCKERHUB

# las etiquetas son etiquetas que apuntan a una ID de imagen

```
$ docker image ls
```

Verás que cada imagen tiene una etiqueta

### Reetiquetar imagen existente

```
$ docker image tag nginx btraversy/nginx
```

### Subir a dockerhub

```
$ docker image push bradtraversy/nginx
```

### Si se niega, haz

```
$ docker login
```

### Agregar etiqueta a nueva imagen

```
$ docker image tag bradtraversy/nginx bradtraversy/nginx:testing
```

### DOCKERFILE PARTES

- FROM - El sistema operativo utilizado. Común es alpine, debian, ubuntu
- ENV - Variables de entorno
- RUN - Ejecutar comandos/scripts de shell, etc
- EXPOSE - Puertos para exponer
- CMD - El comando final se ejecuta cuando inicia un nuevo contenedor desde la imagen
- WORKDIR - Establece el directorio de trabajo (también podría usar 'RUN cd /some/path')
- COPY # Copia archivos del host al contenedor

### Crear imagen desde dockerfile (reponame puede ser lo que sea)

### Desde el mismo directorio que Dockerfile

```
$ docker image build -t [REPONAME] .
```

#### CONSEJO: CACHÉ Y PEDIDO

- Si vuelve a ejecutar la compilación, será rápido porque todo se almacena en caché.
- Si cambia una línea y vuelve a ejecutarla, esa línea y todo lo demás no se almacenarán en caché
- Mantenga las cosas que más cambian hacia la parte inferior del Dockerfile

# EXTENDER DOCKERFILE

### Dockerfile personalizado para la página html con nginx

```
FROM nginx:latest # Extiende nginx para que todo lo incluido en esa imagen se incluya aquí
WORKDIR /usr/share/nginx/html
COPY index.html index.html
```

### Crear imagen desde Dockerfile

```
$ docker image build -t nginx-website
```

### Corriendo

```
$ docker container run -p 80:80 --rm nginx-website
```

### Etiqueta y subir a Dockerhub

```
$ docker image tag nginx-website:latest btraversy/nginx-website:latest
```

```
$ docker image push bradtraversy/nginx-website
```

# VOLUMENES

### Volumen: hace una ubicación especial fuera del contenedor UFS. Usado para bases de datos

### Montaje de unión -Enlace ruta del contenedor a la ruta del host

### Verificar volúmenes

```
$ docker volume ls
```

### Limpiar volúmenes no utilizados

```
$ docker volume prune
```

### bajar la imagen mysql para probar

```
$ docker pull mysql
```

### Inspeccionar y ver volumen

```
$ docker image inspect mysql
```

### Ejecutar contenedor

```
$ docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True mysql
```

### Inspeccione y vea el volumen en el contenedor

```
$ docker container inspect mysql
```

#### CONSEJO: Monturas

- También verás el volumen debajo de las monturas
- El contenedor obtiene su propia ubicación única en el host para almacenar esos datos
- Fuente: xxx es donde vive en el host

### Verificar volúmenes

```
$ docker volume ls
```

**No hay forma de diferenciar los volúmenes, por ejemplo, con 2 contenedores mysql, por lo que utilizamos volúmenes con nombre**

### Volúmenes nombrados (Añadir -v command)(el nombre aquí es mysql-db que podría ser cualquier cosa)

```
$ docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v mysql-db:/var/lib/mysql mysql
```

### Inspeccionar nuevo volumen con nombre

```
docker volume inspect mysql-db
```

# MONTAJES DE ENLACE

- No se puede usar en Dockerfile, especificado en tiempo de ejecución (también usa -v)
- ... run -v /Users/brad/stuff:/path/container (mac/linux)
- ... run -v //c/Users/brad/stuff:/path/container (windows)

**SUGERENCIA: en lugar de escribir la ruta local, para el directorio de trabajo use $ (pwd): / path / container: en Windows puede no funcionar a menos que esté en su carpeta de usuarios**

### Ejecute y pueda editar el archivo index.html (el directorio local debe tener el Dockerfile y el index.html)

```
$ docker container run  -p 80:80 -v $(pwd):/usr/share/nginx/html nginx
```

### Entra en el contenedor y verifica

```
$ docker container exec -it nginx bash
$ cd /usr/share/nginx/html
$ ls -al
```

### Puede crear un archivo en el contenedor y también se mostrará en el host

```
$ touch test.txt
```

# DOCKER COMPOSE

- Configurar relaciones entre contenedores
- Guarda nuestra configuración de ejecución del contenedor Docker en un archivo fácil de leer
- 2 Partes: archivo YAML (docker-compose.yml) + herramienta CLI (docker-compose)

### 1. docker-compose.yml - Describe soluciones para

- containers
- networks
- volumes

### 2. docker-compose CLI - utilizada para locales dev/test automatización con archivos YAML

### Ejemplo de archivo de composición

```
version: '2'

# igual que
# docker run -p 80:4000 -v $(pwd):/site bretfisher/jekyll-serve

services:
  jekyll:
    image: bretfisher/jekyll-serve
    volumes:
      - .:/site
    ports:
      - '80:4000'
```

### Correr

```
docker-compose up
```

### Puedes correr en segundo plano con

```
docker-compose up -d
```

### Detener

```
docker-compose stop
```

### Limpiar

```
docker-compose down
```