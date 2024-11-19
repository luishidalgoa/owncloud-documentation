# NextCloud

## Requisitos previos

```bash
sudo apt update && sudo apt upgrade -y
```
```bash
sudo apt install apache2 libapache2-mod-php mysql-server mysql-client-core-8.0 \
php php-mysql php-xml php-mbstring php-curl php-zip php-gd php-intl php-bcmath php-imagick unzip wget -y
```
```bash
sudo a2enmod rewrite headers env dir mime
sudo systemctl restart apache2
```
## Configuracion MySQL
Aseguramos MySQL 
```bash
sudo mysql_secure_installation
```
iniciamos sesion
```bash
sudo mysql -u root -p
```
creamos la base de datos y el usuario de la misma
```sql
CREATE DATABASE nextcloud;
CREATE USER 'nextclouduser'@'localhost' IDENTIFIED BY 'Tu-contraseña-segura';
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextclouduser'@'localhost';
FLUSH PRIVILEGES;
```
comprobamos el plugin de autentificación que usamos
```sql
SELECT user, host, plugin FROM mysql.user WHERE user = 'nextclouduser';
```
> **NOTA** si el plugin no es mysql_native_password lo cambiamos
```sql
ALTER USER 'nextclouduser'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Tu-contraseña-segura'
EXIT;
```
## Descarga e instalación de NextCloud
Vamos a irnos a nuestro directorio donde descargaremos el fichero zip
```bash
sudo cd
```
Descargamos el fichero zip
```bash
wget https://download.nextcloud.com/server/releases/latest.zip
```	
Descomprimimos el fichero zip
```bash
unzip latest.zip
sudo mv nextcloud /var/www/html/
```
Cambiamos los permisos de la carpeta
```bash
sudo chown -R www-data:www-data /var/www/html/nextcloud
sudo chmod -R 750 /var/www/html/nextcloud
```
## Configura apache
1. Vamos a crear un fichero de configuracion para http con el nombre de nextcloud.conf. Con el objetivo de no sobreescribir el fichero 000-default.conf
```bash
sudo nano /etc/apache2/sites-available/nextcloud.conf
```	
Una vez hayamos creado el fichero, lo editamos
```apache
<VirtualHost *:80>
    ServerName tu-dominio-o-ip
    DocumentRoot /var/www/html/nextcloud

    <Directory /var/www/html/nextcloud/>
        Require all granted
        AllowOverride All
        Options FollowSymLinks MultiViews
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/nextcloud_error.log
    CustomLog ${APACHE_LOG_DIR}/nextcloud_access.log combined
</VirtualHost>
```
2. Vamos a indicarle apache que use el fichero de configuracion que acabamos de crear y reiniciamos apache
```bash
sudo a2ensite nextcloud.conf
sudo a2enmod rewrite
sudo systemctl restart apache2
```
## Comprobamos que todo funciona
Vamos a acceder a la web de NextCloud
```bash
http://tu-dominio-o-ip
```

## Configuracion de certificado SSL
### Pasos previos
> si estas en WSL <br>
Deberas redireccionar el puerto 443 de tu maquina windows a la maquina WSL
```bash
netsh interface portproxy add v4tov4 listenport=443 listenaddress=0.0.0.0 connectport=443 connectaddress=172.24.12.198
```

Permite el redireccionamiento del puerto 443 desde tu router a la maquina windows.
Para ello acceder a la ip de tu Gateway por ejemplo `http://192.168.0.1`

### ¡Empecemos!
Instalamos cerbot
```bash
sudo apt install certbot python3-certbot-apache
```
```bash
sudo certbot --apache
```

Sigue las intrucciones que aparecen en la consola. te apareceran las siguientes instrucciones:
1. introduce el correo electronico que usaras para registrarte
2. acepta los terminos de servicio
3. acepta compartir tu correo electronico con el servicio
4. Introduce el dns por ejemplo `luishidalgoa.ddns.net`

una vez completados los pasos anteriores, deberiamos tener un certificado SSL en nuestro dominio. para ello desde el navegador accedemos a `https://luishidalgoa.ddns.net`

## Configuracion DNS (OPCIONAL) con No-IP
```











```

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
