# GUÍA DE INSTALACIÓN DE OWN CLOUD EN UBUNTU 24.04
## Instalación de dependencias previas
```bash
sudo apt-get update
```
```bash
sudo apt -y install software-properties-common`
```
```bash
sudo add-apt-repository ppa:ondrej/php
```
```bash
sudo apt -y install php7.4
```
```bash
sudo apt install apache2 libapache2-mod-php7.4 openssl php-imagick php7.4-common php7.4-curl php7.4-gd php7.4-imap php7.4-intl php7.4-json php7.4-ldap php7.4-mbstring php7.4-mysql php7.4-pgsql php-smbclient php-ssh2 php7.4-sqlite3 php7.4-xml php7.4-zip
```
```bash
sudo apt install mysql-server
```
```bash
sudo apt install mysql-client-core-8.0
```
```bash
sudo wget https://download.owncloud.com/server/stable/owncloud-complete-latest.zip
```
```bash
sudo apt install unzip
```
```bash
sudo unzip owncloud-complete-latest.zip
```

## Configuración MySQL

> creamos la base de datos, el usuario de la base de datos y elevamos los privilegios del usuario creado
```sql
CREATE DATABASE owncloud;
CREATE USER 'ownclouduser'@'localhost' IDENTIFIED BY 'TU-CONTRASEÑA';
GRANT ALL PRIVILEGES ON owncloud.* TO 'ownclouduser'@'localhost';
FLUSH PRIVILEGES;
```

*comprobamos el plugin de autentificación que usamos*
```sql
SELECT user, host, plugin FROM mysql.user WHERE user = 'ownclouduser';
```

*si el plugin no es mysql_native_password lo cambiamos*
```sql
ALTER USER 'ownclouduser'@'localhost' IDENTIFIED WITH mysql_native_password BY 'TU-CONTRASEÑA';
```
>instalamos este paquete y le indicamos el tipo de seguridad q tendra MySQL para conectarse a la bbdd
```bash
sudo mysql_secure_installation
```
```bash
sudo apt-get install php7.4-zip php7.4-gd php7.4-curl php7.4-mbstring php7.4-mysql php7.4-pgsql php7.4-xml php7.4-intl php7.4-xmlreader php7.4-xmlwriter -y
```
```bash
sudo systemctl restart apache2
```
```bash
sudo chmod 777 -R /var/www/html/owncloud/
```
### comprobar instalación de mysql
Deberiamos poder visualizar que entramos en la consola de mysql
```bash
sudo mysql
```

> Nota: Mas información [video instalación owncloud](https://www.youtube.com/watch?v=KU_EcXsrlv4)

## Acceso a Owncloud

> accedemos con la ip de la maquina ubuntu

[http://IP-WSL/owncloud](http://172.24.12.16/owncloud)

## Configuración acceso externo
> vamos a configurar el acceso mediante un dns. pero previamente, vamos a crear un túnel entre la maquina window y nuestro WSL

**enrutamos el WSL al puerto 80**
> NOTA: CAMBIAMOS EL 172.24.12.198 POR LA IP DE TU WSL
```bash
netsh interface portproxy add v4tov4 listenport=80 listenaddress=0.0.0.0 connectport=80 connectaddress=172.24.12.198
```


## Configuracion http (OPCIONAL)
queremos redirigir que cuando accedan a http://IP-WSL, redirigirlos a /var/www/html/owncloud/index.php
1. abrimos el fichero 000-default.conf
```bash
sudo nano /etc/apache2/sites-available/000-default.conf
```
```bash
<VirtualHost *:80>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html/owncloud


        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```
```bash
sudo systemctl restart apache2
```

## Configuración DNS

INSTALAR NO-IP y configurar un dominio

## Configuracion de certificado SSL


## Arranque de inicio de la maquina WSL
1. **Escribimos en un bloc de notas el siguiente comando**
```bash
wsl.exe -d Ubuntu
```
2. **Lo guardamos con la extension .bat**
3. **Abre el Programador de tareas**:
   - Pulsa la tecla Windows y busca "Programador de tareas".
   - En el Programador de tareas, selecciona **Crear tarea** en el panel derecho.

4. **En la ventana Crear tarea**:
   - En la pestaña **General**, asigna un nombre a la tarea, por ejemplo, "Iniciar WSL Ubuntu".
   - Marca la opción **Ejecutar con los privilegios más altos**.
   
5. **Ve a la pestaña Desencadenadores** y haz clic en **Nuevo**:
   - En el cuadro de diálogo **Nuevo desencadenador**, selecciona **Al iniciar el sistema**.
   - Haz clic en **Aceptar**.

6. **Ve a la pestaña Acciones** y haz clic en **Nuevo**:
   - En **Acción**, selecciona **Iniciar un programa**.
   - En **Programa/script**, busca y selecciona el archivo `.bat` que creaste anteriormente, por ejemplo, `iniciar_wsl.bat`.
   - Haz clic en **Aceptar**.

7. **En la pestaña Condiciones**, puedes desmarcar las opciones si no deseas que la tarea dependa de condiciones específicas, como la conexión a la red.

8. **Haz clic en Aceptar** para crear la tarea.
