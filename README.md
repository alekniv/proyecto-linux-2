# Proyecto Servidores Linux

### Introducción

En este proyecto, vamos a implementar y gestionar un conjunto de servidores Linux que alojarán servicios críticos como Nginx, Apache Tomcat, NFS, Memcached, RabbitMQ y MariaDB. Estos servicios serán desplegados en máquinas virtuales (VMs) utilizando Vagrant, una herramienta de automatización que simplifica la creación y configuración de entornos de desarrollo.

### Objetivos

**- Automatización:** Utilizar Vagrant para automatizar el proceso de creación y configuración de máquinas virtuales que alojarán distintos servicios. Esto asegura que el entorno sea fácil de reproducir y gestionar.

**- Implementación de Servicios:** Aprovisionar servicios esenciales como servidores web (Nginx, Apache Tomcat), sistemas de archivos distribuidos (NFS), almacenamiento en caché (Memcached), mensajería (RabbitMQ) y bases de datos (MariaDB).

**- Configuración y Gestión:** Configurar y gestionar cada servicio para que funcione de manera óptima en un entorno virtualizado. Esto incluye la configuración de seguridad, rendimiento y conectividad entre los servicios.

**- Documentación:** Documentar el proceso de configuración y los comandos utilizados, para que otros desarrolladores o administradores puedan replicar y entender la infraestructura.

### Requisitos

**- Oracle VM VirtualBox**

**- Vagrant**

**- VSCode**

**- GIT**

### Entorno de Desarrollo

### Diagrama de Arquitectura

### Desarrollo

#### Git

Creación de carpeta ``linux-server-project``.

```bash
Alejandro@DESKTOP-8HIC0IN MINGW64 ~/Desktop (master)
$ mkdir linux-server-project
```

Nos situamos en la carpeta ``linux-server-project``.

```bash
Alejandro@DESKTOP-8HIC0IN MINGW64 ~/Desktop (master)
$ cd linux-server-project/
```

Iniciamos el proyecto con ``Vagrant``.

```bash
Alejandro@DESKTOP-8HIC0IN MINGW64 ~/Desktop/linux-server-project (master)
$ vagrant init
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
```

Este comando nos creará automaticamente en la carpeta un archivo `Vagrantfile`.
Comprobamos que el archivo se haya creado correctamente.

```bash
Alejandro@DESKTOP-8HIC0IN MINGW64 ~/Desktop/linux-server-project (master)
$ ls
Vagrantfile
```

Instalación de plugin `vagrant-hostmanager`.

```bash
Alejandro@DESKTOP-8HIC0IN MINGW64 ~/Desktop/linux-server-project (master)
$ vagrant plugin install vagrant-hostmanager
Installing the 'vagrant-hostmanager' plugin. This can take a few minutes...
Installed the plugin 'vagrant-hostmanager (1.8.10)'!
```

Abrimos el proyecto en `Visual Studio Code`.

```bash
Alejandro@DESKTOP-8HIC0IN MINGW64 ~/Desktop/linux-server-project (master)
$ code .
```

```ruby
Vagrant.configure("2") do |config|
  config.hostmanager.enabled = true 
  config.hostmanager.manage_host = true
  
### DB vm  ####
  config.vm.define "db01" do |db01|
    db01.vm.box = "eurolinux-vagrant/centos-stream-9"
    db01.vm.box_version = "9.0.43"
    db01.vm.hostname = "db01"
    db01.vm.network "private_network", ip: "192.168.56.15"
    db01.vm.provider "virtualbox" do |vb|
     vb.memory = "1024"
   end

  end
  
### Memcache vm  #### 
  config.vm.define "mc01" do |mc01|
    mc01.vm.box = "eurolinux-vagrant/centos-stream-9"
    mc01.vm.box_version = "9.0.43"
    mc01.vm.hostname = "mc01"
    mc01.vm.network "private_network", ip: "192.168.56.14"
    mc01.vm.provider "virtualbox" do |vb|
     vb.memory = "1024"
   end
  end
  
### RabbitMQ vm  ####
  config.vm.define "rmq01" do |rmq01|
    rmq01.vm.box = "eurolinux-vagrant/centos-stream-9"
    rmq01.vm.box_version = "9.0.43"
    rmq01.vm.hostname = "rmq01"
    rmq01.vm.network "private_network", ip: "192.168.56.13"
    rmq01.vm.provider "virtualbox" do |vb|
     vb.memory = "1024"
   end
  end
  
### tomcat vm ###
   config.vm.define "app01" do |app01|
    app01.vm.box = "eurolinux-vagrant/centos-stream-9"
    app01.vm.box_version = "9.0.43"
    app01.vm.hostname = "app01"
    app01.vm.network "private_network", ip: "192.168.56.12"
    app01.vm.provider "virtualbox" do |vb|
     vb.memory = "1024"
   end
   end
   
  
### Nginx VM ###
  config.vm.define "web01" do |web01|
    web01.vm.box = "ubuntu/jammy64"
    web01.vm.hostname = "web01"
  web01.vm.network "private_network", ip: "192.168.56.11"
  web01.vm.provider "virtualbox" do |vb|
     vb.gui = true
     vb.memory = "1024"
   end
end
  
end
```

Creamos y levantamos las maquinas virtuales definidas en el `Vagrantfile`.

```bash
Alejandro@DESKTOP-8HIC0IN MINGW64 ~/Desktop/linux-server-project (master)
$ vagrant up
```

Verificamos el estado de las VMs.

```bash
Alejandro@DESKTOP-8HIC0IN MINGW64 ~/Desktop/linux-server-project (master)
$ vagrant status
Current machine states:

db01                      running (virtualbox)
mc01                      running (virtualbox)
rmq01                     running (virtualbox)
app01                     running (virtualbox)
web01                     running (virtualbox)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.
```

Nos conectamos a un VM `app01` mediante el siguiente comando:

```bash
Alejandro@DESKTOP-8HIC0IN MINGW64 ~/Desktop/linux-server-project (master)
$ vagrant ssh app01
This box was generated by EuroLinux

Any suggestions are welcome at https://github.com/EuroLinux/cloud-images-rfc/

Happy using.
To delete this message use:
echo '' > /etc/motd
[vagrant@app01 ~]$
```

Salimos de la VM `app01`.

```bash
[vagrant@app01 ~]$ exit
logout
```

Apagamos las VMs.

```bash
Alejandro@DESKTOP-8HIC0IN MINGW64 ~/Desktop/linux-server-project (master)
$ vagrant halt
==> web01: Attempting graceful shutdown of VM...
==> app01: Attempting graceful shutdown of VM...
==> rmq01: Attempting graceful shutdown of VM...
==> mc01: Attempting graceful shutdown of VM...
==> db01: Attempting graceful shutdown of VM...
```










