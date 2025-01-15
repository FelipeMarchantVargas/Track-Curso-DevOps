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

7. (Opcional) Para ejecutar Docker sin sudo, agrega tu usuario al grupo docker:

```
sudo usermod -aG docker $USER
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
