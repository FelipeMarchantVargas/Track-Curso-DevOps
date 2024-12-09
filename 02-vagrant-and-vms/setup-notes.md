# Guía de VMs con vagrant

## Requisitos Previos

Antes de comenzar asegúrate de tener instalados estos programas:

- VirtualBox
- Vagrant

### Instación Vagrant

#### Windows

1. Ir a la [página de Vagrant](https://developer.hashicorp.com/vagrant/install).
2. Seleccionar el SO windows y descargar el archivo correspondiente.
3. Ejecutar el archivo descargado y seguir las instrucciones del instalador.
4. Verificar la instalación con el comando

```
vagrant --version
```

#### MacOS

0. Si no tienes Homebrew, dirígete a su [página](https://brew.sh/) para instalarlo.
1. Cuando tengas Homebrew, ejecuta

```
brew install vagrant
```

2. Verifica la instalación con el comando

```
vagrant --version
```

#### Linux (Ubuntu)

1. Ejecuta los siguientes comandos

```
sudo apt update
sudo apt install vagrant
```

2. Verifica la instalación con

```
vagrant --version
```

### Instalación VirtualBox

#### Windows y MacOS

1. Dirígete a la [página de VirtualBox](https://www.virtualbox.org/wiki/Downloads)
2. Descarga la versión adecuada para tu SO.

#### Linux (Ubuntu)

1. Ejecuta los siguientes comandos

```
sudo apt update
sudo apt install virtualbox
```

2. Verifica la instalación de VirtualBox con

```
virtualbox --help
```

## Configuración de Vagrant

### Búsqueda de boxes de Vagrant

1. Lo primero que necesitamos, es tener una box del SO que vamos a virtualizar. Para eso, nos vamos a dirigir a buscar una box en la [página de Vagrant](https://portal.cloud.hashicorp.com/vagrant/discover).
2. Vamos a seleccionar la box que queramos y vamos a copiar su nombre.
   2.1. Por ejemplo, si yo quiero la box de "ubuntu/bionic64", copiaré ese nombre.

### Inicialización de un proyecto

1. Desde la terminal, vamos a crear una carpeta para trabajar. Lo hacemos ejecutando los comandos

```
mkdir mi_proyecto
cd mi_proyecto
```

2. Desde ese lugar, vamos a ejecutar el comando

```
vagrant init <nombre_box>
```

donde <nombre_box> es el nombre de la box que obtuvimos desde la página de Vagrant.

_Si yo quiero usar el box de "ubuntu/bionic64", usaré el comando_

```
vagrant init ubuntu/bionic64
```

Con eso, se creará un Vagrantfile en la carpeta mi_proyecto.
**Nota: Se facilitó un ejemplo de Vagrantfile en el mismo directorio que este archivo.**

### Levantar la máquina virtual

Si quieremos levantar la máquina virtual, ya podemos hacerlo. Solo necesitamos ejecutar

```
vagrant up
```

en el directorio donde está el Vagrantfile y se levantará la máquina.
Además, podemos revisar el estado de todas nuestras VM con VirtualBox.

### Otros Comandos importantes

Si queremos detener nuestra VM, podemos hacerlo con el comando

```
vagrant halt
```

Si queremos destruir la imágen que creamos de la VM, solo debemo usar el comando

```
vagrant destroy
```

Si la VM está corriendo pero queremos recargarla, podemos usar el comando

```
vagrant reload
```

Si queremos conectarnos a la máquina virtual que está corriendo, debemos usar

```
vagrant ssh
```

## Configuraciones avanzadas de Vagrant

En un Vagrantfile básico, podemos encontrar lo siguiente:

```
Vagrant.configure("2") do |config|
    config.vm.box = "ubuntu/bionic64"
end
```

donde _config.vm.box_ sirve para especificar el box que usará ese Vagrantfile.

Ahora, podemos agregrle otras cosas a nuestra VM, como una dirección ip estática, cierta cantidad de procesadores, cierta memoria RAM, etc. A continuación se explica cómo hacerlo.

### Configuración de direcciones ip privadas

Para asignar una dirección ip privada estática a nuestra VM, solo debemos usar el comando

```
config.vm.network "private_network", ip: "xxx.xxx.xxx.xxx"
```

y modificando la ip a la que nosotros queramos.
Un ejemplo de esto puede ser

```
config.vm.network "private_network", ip: "192.168.60.12"
```

**Nota: Las últimas versiones de Vagrant limitan el rango de direcciones ip que se pueden usar para esto. Hasta la fecha, este rango va desde 192.168.56.1 hasta 192.168.63.254, o por lo menos a mi me ha funcionado así.**

### Configuración de direcciones ip públicas.

Si ya determinamos una dirección ip privada, pero queremos que nuestra VM aparezca como un dispositivo físico ante nuestro router, se puede aplicar el comando

```
config.vm.network "public_network"
```

Este comando hará puente entre la dirección ip privada de la VM, y el mundo exterior.

### Carpetas sincronizadas

Si queremos que la VM tenga acceso a alguna carpeta de la máquina host, podemos usar las carpetas sincronizadas. Para eso, tenemos esta configuración

```
config.vm.synced_folder "../data", "/vagrant_data"
```

donde el primer argumento "../data" especifica la carpeta del host que será compartida a la VM, y el segundo argumento "/vagrant_data" es la carpeta de la VM en la que se montará la información del host.

### Asignación de Recursos

Si queremos signarle una cantidad personalizada de recursos a las VM, podemo usar el siguiente comando

```
config.vm.provider "virtualbox" do |vb|
   vb.memory = "1024"
   vb.cpus = 2
end
```

Esto hará que se le asigne **1024 MB** de memoria RAM y **2** procesadores a la VM.

### Aprovisionamiento

Si queremos que, cuando arranque la VM, esta ejecute algunos comandos automáticamente, podemos hacerlo con el comando de aprovisionamiento

```
config.vm.provision "shell", inline: <<-SHELL
    <comando 1>
    <comando 2>
    ...
SHELL
```

Esto hará que, al iniciar la VM, se ejecutarán los comandos listados **en orden**.
Un ejemplo de esto puede ser

```
config.vm.provision "shell", inline: <<-SHELL
  apt-get update
  apt-get install -y apache2
SHELL
```

Lo cual ejecutará los comandos _apt-get update_ y _apt-get install -y apache2_ al arrancar la VM.

### Otras Configuraciones adicionales

Para investigar más a fondo sobre las configuraciones adicionales que ofrece Vagrant, puedes hacerlo en su [documentación](https://developer.hashicorp.com/vagrant/docs).

## Hosting de Wordpress con Vagrant (Ubuntu)

### Configuración de Wordpress dentro de la VM

Si queremos crear un sitio con Wordpress en una VM con Ubuntu, se puede correr la VM con

```
vagrant up
```

luego entrar a la VM con

```
vagrant ssh
```

y luego seguir [este tutorial](https://ubuntu.com/tutorials/install-and-configure-wordpress#1-overview) de setup para Wordpress con Ubuntu.

Pero también podemos automatizar este proceso. Veamos cómo.

### Automatización de Setup de Wordpress con Ubuntu

Si queremos automatizar el setup de Wordpress con Ubuntu, podemos aprovechar el aprovisionamiento de Vagrant para que haga todos los pasos del [tutorial](https://ubuntu.com/tutorials/install-and-configure-wordpress#1-overview) apenas arranque la máquina.
Para eso, vamos a editar la parte de aprovisionamiento. yo lo dejaré de la siguiente manera:

```
   config.vm.provision "shell", inline: <<-SHELL
	sudo apt update
	sudo apt install apache2 \
                 ghostscript \
                 libapache2-mod-php \
                 mysql-server \
                 php \
                 php-bcmath \
                 php-curl \
                 php-imagick \
                 php-intl \
                 php-json \
                 php-mbstring \
                 php-mysql \
                 php-xml \
                 php-zip -y
	sudo mkdir -p /srv/www
	sudo chown www-data: /srv/www
	curl https://wordpress.org/latest.tar.gz | sudo -u www-data tar zx -C /srv/www
	cp /vagrant/wordpress.conf /etc/apache2/sites-available/wordpress.conf
	sudo a2ensite wordpress
	sudo a2enmod rewrite
	sudo a2dissite 000-default
	sudo service apache2 reload
	mysql -u root -e 'CREATE DATABASE wordpress;'
	mysql -u root -e 'CREATE USER wordpress@localhost IDENTIFIED BY "admin123";'
	mysql -u root -e 'GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER ON wordpress.* TO wordpress@localhost;'
	mysql -u root -e 'FLUSH PRIVILEGES;'
	sudo -u www-data cp /srv/www/wordpress/wp-config-sample.php /srv/www/wordpress/wp-config.php
	sudo -u www-data sed -i 's/database_name_here/wordpress/' /srv/www/wordpress/wp-config.php
	sudo -u www-data sed -i 's/username_here/wordpress/' /srv/www/wordpress/wp-config.php
	sudo -u www-data sed -i 's/password_here/admin123/' /srv/www/wordpress/wp-config.php
   SHELL
```

Recuerda que esto es solo un ejemplo, donde modifiqué la contraseña del usuario de la DB por "admin123". Tu puedes usar la contraseña que quieras.
Además, en la sección de "Configurar Apache con WordPress", donde se solicita cambiar un archivo "wordpress.conf", este se creó en el mismo directorio con el contenido del tutorial:

```
<VirtualHost *:80>
    DocumentRoot /srv/www/wordpress
    <Directory /srv/www/wordpress>
        Options FollowSymLinks
        AllowOverride Limit Options FileInfo
        DirectoryIndex index.php
        Require all granted
    </Directory>
    <Directory /srv/www/wordpress/wp-content>
        Options FollowSymLinks
        Require all granted
    </Directory>
</VirtualHost>
```

### Acceder al sitio de Wordpress

Ya sea si lo hayas automatizado o no, si seguiste todos los pasos del [tutorial de ubuntu](https://ubuntu.com/tutorials/install-and-configure-wordpress#1-overview), ya debería estar corriendo la página de Wordpress hosteada en la VM.
Para acceder a su página, deberás haber asignado una ip privada a la VM previamente. Puedes acceder a esa VM que le asignaste, o en su defecto, puedes entrar en la VM con

```
vagrant ssh
```

y dentro de la VM, obtener sus conexiones con

```
ip addr show
```

Lo cual mostrará las diferentes conexiones que tiene la VM.
Puedes acceder con cualquiera de las direcciones ip que se señalan, menos con la NAT.

Si todo salió bien, deberías poder acceder a la página de Wordpress sin problemas!
