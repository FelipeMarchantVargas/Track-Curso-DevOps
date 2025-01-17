# Guía de Docker con VMs

## ¿Qué es Docker?

Docker es una plataforma de software que permite crear, probar y desplegar aplicaciones rápidamente. Utiliza contenedores para empaquetar todo lo que necesita una aplicación (código, dependencias, librerías, etc.) en un único entorno portátil. Esto asegura que las aplicaciones se ejecuten de manera consistente en cualquier sistema operativo o infraestructura.

## Requisitos Previos

Antes de comenzar, asegúrate de tener instalados los siguientes programas:

- Un sistema operativo compatible (Linux, Windows, macOS).
- Una herramienta de virtualización como VirtualBox (si planeas usar Docker con Docker Desktop en sistemas donde se requiere).

## Instalación

### Instalación de Docker Desktop

#### Windows

1. Ve a la [página de descarga de Docker Desktop](https://www.docker.com/products/docker-desktop).
2. Descarga el instalador para Windows.
3. Ejecuta el archivo descargado y sigue las instrucciones del instalador.
4. Una vez instalado, abre Docker Desktop y asegúrate de habilitar la integración con WSL 2 (si estás en Windows 10/11).
5. Verifica la instalación ejecutando:

```
docker --version
```

#### MacOS

1. Ve a la [página de descarga de Docker Desktop](https://www.docker.com/products/docker-desktop).
2. Descarga el instalador para macOS (Intel o Apple Silicon, según tu hardware).
3. Arrastra el archivo descargado a tu carpeta de Aplicaciones y ábrelo.
4. Acepta los términos y condiciones, y espera a que se complete la instalación.
5. Verifica la instalación ejecutando:

```
docker --version
```

#### Linux (Ubuntu)

1. Actualiza los paquetes de tu sistema:

```
sudo apt update
sudo apt upgrade
```

2. Instala las dependencias necesarias:

```
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

3. Añade la clave GPG oficial de Docker:

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

4. Añade el repositorio de Docker:

```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

5. Instala Docker Engine:

```
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io
```

6. Verifica la instalación ejecutando:

```
docker --version
```

7. (Opcional) Para ejecutar Docker sin sudo:

```
sudo chmod 666 /var/run/docker.sock
```

Reinicia la sesión para aplicar los cambios.

## Comandos básicos

### Gestión de contenedores

- Listar contenedores en ejecución:

```
docker ps
```

- Listar todos los contenedores (incluidos detenidos):

```
docker ps -a
```

- Crear y ejecutar un contenedor:

```
docker run <imagen>
```

Por ejemplo:

```
docker run hello-world
```

- Detener un contenedor:

```
docker stop <id_contenedor>
```

- Eliminar un contenedor:

```
docker rm <id_contenedor>
```

- Ver los logs de un contenedor:

```
docker logs <id_contenedor>
```

### Gestión de imágenes

- Listar imágenes locales:

```
docker images
```

- Descargar una imagen:

```
docker pull <imagen>
```

Por ejemplo:

```
docker pull nginx
```

- Eliminar una imagen:

```
docker rmi <id_imagen>
```

- Mapeo de puertos:

```
docker run -p <puerto>:<puerto>
```

- Carpetas dinámicas:

```
docker run -v <carpeta local>:<carpeta virtual>
```

### Volúmenes

- Crear un volumen:

```
docker volume create <nombre_volumen>
```

- Listar volúmenes:

```
docker volume ls
```

- Eliminar un volumen:

```
docker volume rm <nombre_volumen>
```

### Redes

- Listar redes:

```
docker network ls
```

- Crear una red:

```
docker network create <nombre_red>
```

- Conectar un contenedor a una red:

```
docker network connect <nombre_red> <id_contenedor>
```

- Desconectar un contenedor de una red:

```
docker network disconnect <nombre_red> <id_contenedor>
```

## Crear imágenes

Para crear una imagen, se puede hacer con un Dockerfile, el cual puede llevar la siguiente estructura:

```
FROM ubuntu
RUN apt-get update
RUN apt-get install -y python3 python3-pip python3-flask
COPY app.py /opt/app.py
ENTRYPOINT FLASK_APP=/opt/app.py flask run --host=0.0.0.0
```

_Esto se basa en el [siguiente repositorio](https://github.com/CursoJenkins0/app-flask)_

Luego, solo basta con usar el comando:

```
docker build . -t <nombre imagen>
```

y la imagen estará lista para correr con docker run!!

## Docker Compose

### ¿Qué es Docker Compose?

Docker Compose es una herramienta que permite definir y administrar aplicaciones multicontenedor. Utiliza un archivo `docker-compose.yml` para configurar todos los servicios necesarios, lo que facilita el desarrollo y la orquestación local.

### Comandos básicos de Docker Compose

- **Levantar servicios**:

```
docker-compose up
```

Este comando levanta todos los servicios definidos en el archivo docker-compose.yml.

- Levantar servicios con reconstrucción:

```
docker-compose up --build
```

Reconstruye las imágenes antes de iniciar los servicios.

- Detener servicios:

```
docker-compose down
```

Detiene y elimina los servicios, redes y volúmenes creados.

- Ver servicios en ejecución:

```
docker-compose ps
```

- Ver logs de servicios:

```
docker-compose logs
```

- Ejecutar comandos dentro de un servicio:

```
docker-compose exec <nombre_servicio> <comando>
```

### Variables de entorno

Docker Compose permite usar archivos .env para parametrizar valores como puertos, nombres de contenedores, redes, etc. Un archivo .env podría verse así:

```
APP_PORT=5000
DB_PORT=5432
DB_NAME=mi_base_datos
```

En el archivo docker-compose.yml, puedes referenciar estas variables así:

```
services:
  web:
    build:
      context: .
    ports:
      - "${APP_PORT}:5000"
  db:
    image: postgres
    environment:
      POSTGRES_DB: ${DB_NAME}
    ports:
      - "${DB_PORT}:5432"
```
