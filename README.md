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

Configuramos el archivo `Vagrantfile` dejandolo de la siguiente manera:

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
**Nota:** Más adelante a este archivo se le agregarán scripts en `bash` para automatizar la configuración de cada servicio.


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

Creamos la carpeta `recursos` y nos posicionamos en la misma.

```bash
Alejandro@DESKTOP-8HIC0IN MINGW64 ~/Desktop/linux-server-project (master)
$ mkdir recursos

Alejandro@DESKTOP-8HIC0IN MINGW64 ~/Desktop/linux-server-project (master)
$ cd recursos/
```

Creamos los siguientes archivos y verificamos que se hayan creado correctamente.

```bash
Alejandro@DESKTOP-8HIC0IN MINGW64 ~/Desktop/linux-server-project/recursos (master)
$ touch accountsdb.sql application.properties db_backup.sql logback.xml validation.properties

Alejandro@DESKTOP-8HIC0IN MINGW64 ~/Desktop/linux-server-project/recursos (master)
$ ls
accountsdb.sql          db_backup.sql  validation.properties
application.properties  logback.xml
```

Editamos el archivo `accountsdb.sql` y lo dejamos de la siguiente manera:

```sql
-- MySQL dump 10.13  Distrib 5.7.18, for Linux (x86_64)
--
-- Host: localhost    Database: accounts
-- ------------------------------------------------------
-- Server version	5.7.18-0ubuntu0.16.10.1

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `role`
--

DROP TABLE IF EXISTS `role`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `role` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(45) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `role`
--

LOCK TABLES `role` WRITE;
/*!40000 ALTER TABLE `role` DISABLE KEYS */;
INSERT INTO `role` VALUES (1,'ROLE_USER');
/*!40000 ALTER TABLE `role` ENABLE KEYS */;
UNLOCK TABLES;

--
-- Table structure for table `user`
--

DROP TABLE IF EXISTS `user`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(255) DEFAULT NULL,
  `userEmail` varchar(255) DEFAULT NULL,
  `password` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `user`
--

LOCK TABLES `user` WRITE;
/*!40000 ALTER TABLE `user` DISABLE KEYS */;
INSERT INTO `user` VALUES (4,'admin_vp','admin@visualpathit.com','$2a$11$DSEIKJNrgPjG.iCYUwErvOkREtC67mqzQ.ogkZbc/KOW1OPOpZfY6');
/*!40000 ALTER TABLE `user` ENABLE KEYS */;
UNLOCK TABLES;

--
-- Table structure for table `user_role`
--

DROP TABLE IF EXISTS `user_role`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `user_role` (
  `user_id` int(11) NOT NULL,
  `role_id` int(11) NOT NULL,
  PRIMARY KEY (`user_id`,`role_id`),
  KEY `fk_user_role_roleid_idx` (`role_id`),
  CONSTRAINT `fk_user_role_roleid` FOREIGN KEY (`role_id`) REFERENCES `role` (`id`) ON DELETE CASCADE ON UPDATE CASCADE,
  CONSTRAINT `fk_user_role_userid` FOREIGN KEY (`user_id`) REFERENCES `user` (`id`) ON DELETE CASCADE ON UPDATE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `user_role`
--

LOCK TABLES `user_role` WRITE;
/*!40000 ALTER TABLE `user_role` DISABLE KEYS */;
INSERT INTO `user_role` VALUES (4,1);
/*!40000 ALTER TABLE `user_role` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;
```

Editamos el archivo `application.properties` y lo dejamos de la siguiente manera:

```
#JDBC Configutation for Database Connection
jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://db01:3306/accounts?useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull
jdbc.username=admin
jdbc.password=admin123

#Memcached Configuration For Active and StandBy Host
#For Active Host
memcached.active.host=mc01
memcached.active.port=11211
#For StandBy Host
memcached.standBy.host=127.0.0.2
memcached.standBy.port=11211

#RabbitMq Configuration
rabbitmq.address=rmq01
rabbitmq.port=5672
rabbitmq.username=test
rabbitmq.password=test

#Elasticesearch Configuration
elasticsearch.host =192.168.1.85
elasticsearch.port =9300
elasticsearch.cluster=vprofile
elasticsearch.node=vprofilenode
```

Editamos el archivo `db_backup.sql` y lo dejamos de la siguiente manera:

```sql
-- MySQL dump 10.13  Distrib 5.7.18, for Linux (x86_64)
--
-- Host: localhost    Database: accounts
-- ------------------------------------------------------
-- Server version	5.7.18-0ubuntu0.16.10.1

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `role`
--

DROP TABLE IF EXISTS `role`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `role` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(45) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `role`
--

LOCK TABLES `role` WRITE;
/*!40000 ALTER TABLE `role` DISABLE KEYS */;
INSERT INTO `role` VALUES (1,'ROLE_USER');
/*!40000 ALTER TABLE `role` ENABLE KEYS */;
UNLOCK TABLES;

--
-- Table structure for table `user`
--

DROP TABLE IF EXISTS `user`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(255) DEFAULT NULL,
  `userEmail` varchar(255) DEFAULT NULL,
  `profileImg` varchar(255) DEFAULT NULL,
  `profileImgPath` varchar(255) DEFAULT NULL,
  `dateOfBirth` varchar(255) DEFAULT NULL,
  `fatherName` varchar(255) DEFAULT NULL,
  `motherName` varchar(255) DEFAULT NULL,
  `gender` varchar(255) DEFAULT NULL,
  `maritalStatus` varchar(255) DEFAULT NULL,
  `permanentAddress` varchar(255) DEFAULT NULL,
  `tempAddress` varchar(255) DEFAULT NULL,
  `primaryOccupation` varchar(255) DEFAULT NULL,
  `secondaryOccupation` varchar(255) DEFAULT NULL,
  `skills` varchar(255) DEFAULT NULL,
  `phoneNumber` varchar(255) DEFAULT NULL,
  `secondaryPhoneNumber` varchar(255) DEFAULT NULL,
  `nationality` varchar(255) DEFAULT NULL,
  `language` varchar(255) DEFAULT NULL,
  `workingExperience` varchar(255) DEFAULT NULL,
  `password` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=14 DEFAULT CHARSET=utf8;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `user`
--

LOCK TABLES `user` WRITE;
/*!40000 ALTER TABLE `user` DISABLE KEYS */;

INSERT INTO `user` VALUES (7,'admin_vp','admin@hkhinfo.com',NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,'$2a$11$0a7VdTr4rfCQqtsvpng6GuJnzUmQ7gZiHXgzGPgm5hkRa3avXgBLK')
,(8,'Abrar Nirban','abrar.nirban74@gmail.com',NULL,NULL,'27/01/2002','A nirban','T nirban','male','unMarried','Dubai,UAE','Dubai,UAE','Software Engineer','Software Engineer','Java HTML CSS ','8888888888','8888888888','Indian','english','2 ','$2a$11$UgG9TkHcgl02LxlqxRHYhOf7Xv4CxFmFEgS0FpUdk42OeslI.6JAW'),
(9,'Amayra Fatima','amayra@gmail.com',NULL,NULL,'20/06/1993','K','L','female','unMarried','Dubai,UAE','Dubai,UAE','Software Engineer','Software Engineer','Java HTML CSS ','9999999999','9999999999','India','english','5','$2a$11$gwvsvUrFU.YirMM1Yb7NweFudLUM91AzH5BDFnhkNzfzpjG.FplYO'),
(10,'Aron','aron.DSilva@gmail.com',NULL,NULL,'27/01/2002','M nirban','R nirban','male','unMarried','Dubai,UAE','Dubai,UAE','Software Engineer','Software Engineer','Java HTML CSS ','7777777777','777777777','India','english','7','$2a$11$6oZEgfGGQAH23EaXLVZ2WOSKxcEJFnBSw2N2aghab0s2kcxSQwjhC'),
(11,'Kiran Kumar','kiran@gmail.com',NULL,NULL,'8/12/1993','K K','RK','male','unMarried','SanFrancisco','James Street','Software Engineer','Software Engineer','Java HTML CSS ','1010101010','1010101010','India','english','10','$2a$11$EXwpna1MlFFlKW5ut1iVi.AoeIulkPPmcOHFO8pOoQt1IYU9COU0m'),
(12,'Balbir Singh','balbir@gmail.com',NULL,NULL,'20/06/1993','balbir RK','balbir AK','male','unMarried','SanFrancisco','US','Software Engineer','Software Engineer','Java HTML CSS AWS','8888888111','8888888111','India','english','8','$2a$11$pzWNzzR.HUkHzz2zhAgqOeCl0WaTgY33NxxJ7n0l.rnEqjB9JO7vy'),
(4,'Hibo Prince','hibo.prince@gmail.com',NULL,NULL,'6/09/2000','Abara','Queen','male','unMarried','Electronic City,UAE','Electronic City,UAE','Tester','Freelancing','Python PHP ','9146389863','9146389871','Indian','hindi','3 ','$2a$11$UgG9TkHcgl02LxlqxRHYhOf7Xv4CxFmFEgS0FpUdk42OeslI.6JAR'),
(5,'Aejaaz Habeeb','aejaaz.habeeb@gmail.com',NULL,NULL,'16/02/2001','Imran','Ziya','male','unMarried','AbuDhabi,UAE','AbuDhabi,UAE','Developer','Developer','Azure Devops ','9566489863','9566489863','Indian','hindi','4 ','$2a$11$UgG9TkHcgl02LxlqxRHYhOf7Xv4CxFmFEgS0FpUdk42OeslI.6JAR'),
(6,'Jackie','jackie.chan@gmail.com',NULL,NULL,'28/09/1992','Charles','Chan','male','Married','HongKong,China','HongKong,China','MartialArtist','MartialArtist','KungFu ','9246488863','9246488863','Chinese','Mandrian','1 ','$2a$11$UgG9TkHcgl02LxlqxRHYhOf7Xv4CxFmFEgS0FpUdk42OeslI.6RAR'),
(13,'Srinath Goud','sgoud@gmail.com',NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,'$2a$11$6BSmYPrT8I8b9yHmx.uTRu/QxnQM2vhZYQa8mR33aReWA4WFihyGK');


/*!40000 ALTER TABLE `user` ENABLE KEYS */;
UNLOCK TABLES;

--
-- Table structure for table `user_role`
--

DROP TABLE IF EXISTS `user_role`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `user_role` (
  `user_id` int(11) NOT NULL,
  `role_id` int(11) NOT NULL,
  PRIMARY KEY (`user_id`,`role_id`),
  KEY `fk_user_role_roleid_idx` (`role_id`),
  CONSTRAINT `fk_user_role_roleid` FOREIGN KEY (`role_id`) REFERENCES `role` (`id`) ON DELETE CASCADE ON UPDATE CASCADE,
  CONSTRAINT `fk_user_role_userid` FOREIGN KEY (`user_id`) REFERENCES `user` (`id`) ON DELETE CASCADE ON UPDATE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `user_role`
--

LOCK TABLES `user_role` WRITE;
/*!40000 ALTER TABLE `user_role` DISABLE KEYS */;
INSERT INTO `user_role` VALUES (4,1),(5,1),(6,1),(7,1),(8,1),(9,1),(10,1),(11,1),(12,1),(13,1);
/*!40000 ALTER TABLE `user_role` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;
```

Editamos el archivo `logback.xml` dejandolo de la siguiente manera:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE logback>
<configuration>

    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%date{HH:mm:ss.SSS} [%thread] %-5level %logger{15}#%line %msg\n</pattern>
        </encoder>
    </appender>

    <logger name="com.visualpathit.account">
        <level value="debug"/>
    </logger>

    <logger name="org.springframework">
        <level value="info"/>
    </logger>

    <root>
        <level value="error"/>
        <appender-ref ref="STDOUT"/>
    </root>

</configuration>
```

Editamos el archivo `validation.properties` dejandolo de la siguiente manera:

```
NotEmpty=This field is required.
Size.userForm.username=Please use between 6 and 32 characters.
Duplicate.userForm.username= User has already taken this Username.
Size.userForm.password=Try one with at least 8 characters.
Diff.userForm.passwordConfirm=These passwords don't match.
```
Movemos el archivo `application.properties` al directorio superior `linux-server-project`

```bash
Alejandro@DESKTOP-8HIC0IN MINGW64 ~/Desktop/linux-server-project/recursos (master)
$ mv application.properties ../

Alejandro@DESKTOP-8HIC0IN MINGW64 ~/Desktop/linux-server-project/recursos (master)
$ cd ..

Alejandro@DESKTOP-8HIC0IN MINGW64 ~/Desktop/linux-server-project (master)
$ ls
Vagrantfile  application.properties  recursos/
```

Creación de scripts en bash.

Creamos los archivos `backend.sh`, `memcache.sh`, `mysql.sh`, `nginx.sh`, `rabbitmq.sh`, `tomcat.sh`, `tomcat_ubuntu.sh`.

```bash
Alejandro@DESKTOP-8HIC0IN MINGW64 ~/Desktop/linux-server-project (master)
$ touch backend.sh memcache.sh mysql.sh nginx.sh rabbitmq.sh tomcat.sh tomcat_ubuntu.sh

Alejandro@DESKTOP-8HIC0IN MINGW64 ~/Desktop/linux-server-project (master)
$ ls
Vagrantfile             backend.sh   mysql.sh  rabbitmq.sh  tomcat.sh
application.properties  memcache.sh  nginx.sh  recursos/    tomcat_ubuntu.sh
```
Editamos el archivo `backend.sh`.

```bash
#!/bin/bash
DATABASE_PASS='admin123'

# MEmcache
yum install epel-release -y
yum install memcached -y
systemctl start memcached
systemctl enable memcached
systemctl status memcached
memcached -p 11211 -U 11111 -u memcached -d

# Rabbit
yum install socat -y
yum install erlang -y
yum install wget -y
wget https://www.rabbitmq.com/releases/rabbitmq-server/v3.6.10/rabbitmq-server-3.6.10-1.el7.noarch.rpm
rpm --import https://www.rabbitmq.com/rabbitmq-release-signing-key.asc
yum update
rpm -Uvh rabbitmq-server-3.6.10-1.el7.noarch.rpm
systemctl start rabbitmq-server
systemctl enable rabbitmq-server
systemctl status rabbitmq-server
echo "[{rabbit, [{loopback_users, []}]}]." > /etc/rabbitmq/rabbitmq.config
rabbitmqctl add_user rabbit bunny
rabbitmqctl set_user_tags rabbit administrator
systemctl restart rabbitmq-server

# Mysql
yum install mariadb-server -y

#mysql_secure_installation
sed -i 's/^127.0.0.1/0.0.0.0/' /etc/my.cnf

# starting & enabling mariadb-server
systemctl start mariadb
systemctl enable mariadb

#restore the dump file for the application
mysqladmin -u root password "$DATABASE_PASS"
mysql -u root -p"$DATABASE_PASS" -e "UPDATE mysql.user SET Password=PASSWORD('$DATABASE_PASS') WHERE User='root'"
mysql -u root -p"$DATABASE_PASS" -e "DELETE FROM mysql.user WHERE User='root' AND Host NOT IN ('localhost', '127.0.0.1', '::1')"
mysql -u root -p"$DATABASE_PASS" -e "DELETE FROM mysql.user WHERE User=''"
mysql -u root -p"$DATABASE_PASS" -e "DELETE FROM mysql.db WHERE Db='test' OR Db='test\_%'"
mysql -u root -p"$DATABASE_PASS" -e "FLUSH PRIVILEGES"
mysql -u root -p"$DATABASE_PASS" -e "create database accounts"
mysql -u root -p"$DATABASE_PASS" -e "grant all privileges on accounts.* TO 'admin'@'localhost' identified by 'admin123'"
mysql -u root -p"$DATABASE_PASS" -e "grant all privileges on accounts.* TO 'admin'@'app01' identified by 'admin123'"
mysql -u root -p"$DATABASE_PASS" accounts < proyecto-linux-2/recursos/db_backup.sql
mysql -u root -p"$DATABASE_PASS" -e "FLUSH PRIVILEGES"

# Restart mariadb-server
systemctl restart mariadb
```

Editamos el archivo `memcache.sh`.

```bash
#!/bin/bash
sudo dnf install epel-release -y
sudo dnf install memcached -y
sudo systemctl start memcached
sudo systemctl enable memcached
sudo systemctl status memcached
sed -i 's/127.0.0.1/0.0.0.0/g' /etc/sysconfig/memcached
sudo systemctl restart memcached
firewall-cmd --add-port=11211/tcp
firewall-cmd --runtime-to-permanent
firewall-cmd --add-port=11111/udp
firewall-cmd --runtime-to-permanent
sudo memcached -p 11211 -U 11111 -u memcached -d
```

Editamos el archivo `mysql.sh`

```bash
#!/bin/bash
DATABASE_PASS='admin123'
sudo yum update -y
sudo yum install epel-release -y
sudo yum install git zip unzip -y
sudo yum install mariadb-server -y


# starting & enabling mariadb-server
sudo systemctl start mariadb
sudo systemctl enable mariadb
cd /tmp/
git clone https://github.com/alekniv/proyecto-linux-2.git
#restore the dump file for the application
sudo mysqladmin -u root password "$DATABASE_PASS"
sudo mysql -u root -p"$DATABASE_PASS" -e "DELETE FROM mysql.user WHERE User='root' AND Host NOT IN ('localhost', '127.0.0.1', '::1')"
sudo mysql -u root -p"$DATABASE_PASS" -e "DELETE FROM mysql.user WHERE User=''"
sudo mysql -u root -p"$DATABASE_PASS" -e "DELETE FROM mysql.db WHERE Db='test' OR Db='test\_%'"
sudo mysql -u root -p"$DATABASE_PASS" -e "FLUSH PRIVILEGES"
sudo mysql -u root -p"$DATABASE_PASS" -e "create database accounts"
sudo mysql -u root -p"$DATABASE_PASS" -e "grant all privileges on accounts.* TO 'admin'@'localhost' identified by 'admin123'"
sudo mysql -u root -p"$DATABASE_PASS" -e "grant all privileges on accounts.* TO 'admin'@'%' identified by 'admin123'"
sudo mysql -u root -p"$DATABASE_PASS" accounts < /tmp/proyecto-linux-2/recursos/db_backup.sql
sudo mysql -u root -p"$DATABASE_PASS" -e "FLUSH PRIVILEGES"

# Restart mariadb-server
sudo systemctl restart mariadb


#starting the firewall and allowing the mariadb to access from port no. 3306
sudo systemctl start firewalld
sudo systemctl enable firewalld
sudo firewall-cmd --get-active-zones
sudo firewall-cmd --zone=public --add-port=3306/tcp --permanent
sudo firewall-cmd --reload
sudo systemctl restart mariadb
```

Editamos el archivo `nginx.sh`

```bash
# adding repository and installing nginx		
apt update
apt install nginx -y
cat <<EOT > vproapp
upstream vproapp {

 server app01:8080;

}

server {

  listen 80;

location / {

  proxy_pass http://vproapp;

}

}

EOT

mv vproapp /etc/nginx/sites-available/vproapp
rm -rf /etc/nginx/sites-enabled/default
ln -s /etc/nginx/sites-available/vproapp /etc/nginx/sites-enabled/vproapp

#starting nginx service and firewall
systemctl start nginx
systemctl enable nginx
systemctl restart nginx
```

Editamos el archivo `rabbitmq.sh`.

```bash
#!/bin/bash
sudo yum install epel-release -y
sudo yum update -y
sudo yum install wget -y
cd /tmp/
dnf -y install centos-release-rabbitmq-38
 dnf --enablerepo=centos-rabbitmq-38 -y install rabbitmq-server
 systemctl enable --now rabbitmq-server
 firewall-cmd --add-port=5672/tcp
 firewall-cmd --runtime-to-permanent
sudo systemctl start rabbitmq-server
sudo systemctl enable rabbitmq-server
sudo systemctl status rabbitmq-server
sudo sh -c 'echo "[{rabbit, [{loopback_users, []}]}]." > /etc/rabbitmq/rabbitmq.config'
sudo rabbitmqctl add_user test test
sudo rabbitmqctl set_user_tags test administrator
sudo systemctl restart rabbitmq-server
```

Editamos el archivo `tomcat.sh`

```bash
TOMURL="https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.75/bin/apache-tomcat-9.0.75.tar.gz"
dnf -y install java-11-openjdk java-11-openjdk-devel
dnf install git maven wget -y
cd /tmp/
wget $TOMURL -O tomcatbin.tar.gz
EXTOUT=`tar xzvf tomcatbin.tar.gz`
TOMDIR=`echo $EXTOUT | cut -d '/' -f1`
useradd --shell /sbin/nologin tomcat
rsync -avzh /tmp/$TOMDIR/ /usr/local/tomcat/
chown -R tomcat.tomcat /usr/local/tomcat

rm -rf /etc/systemd/system/tomcat.service

cat <<EOT>> /etc/systemd/system/tomcat.service
[Unit]
Description=Tomcat
After=network.target

[Service]

User=tomcat
Group=tomcat

WorkingDirectory=/usr/local/tomcat

#Environment=JRE_HOME=/usr/lib/jvm/jre
Environment=JAVA_HOME=/usr/lib/jvm/jre

Environment=CATALINA_PID=/var/tomcat/%i/run/tomcat.pid
Environment=CATALINA_HOME=/usr/local/tomcat
Environment=CATALINE_BASE=/usr/local/tomcat

ExecStart=/usr/local/tomcat/bin/catalina.sh run
ExecStop=/usr/local/tomcat/bin/shutdown.sh


RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target

EOT

systemctl daemon-reload
systemctl start tomcat
systemctl enable tomcat

git clone https://github.com/alekniv/proyecto-linux-2.git
cd proyecto-linux-2
mvn install
systemctl stop tomcat
sleep 20
rm -rf /usr/local/tomcat/webapps/ROOT*
cp target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war
systemctl start tomcat
sleep 20
systemctl stop firewalld
systemctl disable firewalld
#cp /vagrant/application.properties /usr/local/tomcat/webapps/ROOT/WEB-INF/classes/application.properties
systemctl restart tomcat
```

Editamos el archivo `tomcat_ubuntu.sh`.

```bash
#!/bin/bash
sudo apt update
sudo apt upgrade -y
sudo apt install openjdk-8-jdk -y
sudo apt install tomcat8 tomcat8-admin tomcat8-docs tomcat8-common git -y
```

El siguiente paso será volver a modificar el archivo `Vagrantfile` en donde se incluirá los scritps para la automatización completa del stack.

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
     vb.memory = "600"
   end
    db01.vm.provision "shell", path: "mysql.sh"  

  end
  
### Memcache vm  #### 
  config.vm.define "mc01" do |mc01|
    mc01.vm.box = "eurolinux-vagrant/centos-stream-9"
    mc01.vm.box_version = "9.0.43"
    mc01.vm.hostname = "mc01"
    mc01.vm.network "private_network", ip: "192.168.56.14"
    mc01.vm.provider "virtualbox" do |vb|
     vb.memory = "600"
   end
    mc01.vm.provision "shell", path: "memcache.sh"  
  end
  
### RabbitMQ vm  ####
  config.vm.define "rmq01" do |rmq01|
    rmq01.vm.box = "eurolinux-vagrant/centos-stream-9"
    rmq01.vm.box_version = "9.0.43"
    rmq01.vm.hostname = "rmq01"
    rmq01.vm.network "private_network", ip: "192.168.56.16"
    rmq01.vm.provider "virtualbox" do |vb|
     vb.memory = "600"
   end
    rmq01.vm.provision "shell", path: "rabbitmq.sh"  
  end
  
### tomcat vm ###
   config.vm.define "app01" do |app01|
    app01.vm.box = "eurolinux-vagrant/centos-stream-9"
    app01.vm.box_version = "9.0.43"
    app01.vm.hostname = "app01"
    app01.vm.network "private_network", ip: "192.168.56.12"
    app01.vm.provision "shell", path: "tomcat.sh"  
    app01.vm.provider "virtualbox" do |vb|
     vb.memory = "800"
   end
   end
   
  
### Nginx VM ###
  config.vm.define "web01" do |web01|
    web01.vm.box = "ubuntu/jammy64"
    web01.vm.hostname = "web01"
  web01.vm.network "private_network", ip: "192.168.56.11"
#  web01.vm.network "public_network"
  web01.vm.provider "virtualbox" do |vb|
     vb.gui = true
     vb.memory = "800"
   end
  web01.vm.provision "shell", path: "nginx.sh"  
end
  
end
```



Una vez que hemos configurados estos archivos los subiremos al repositorio remoto.









