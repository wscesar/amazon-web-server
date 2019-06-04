# Configurar Servidor Web na Amazon

#### criar diretorio raiz do site
sudo mkdir -p /var/www/meusite.com/public_html

#### criar arquivo index na raiz do site
sudo nano /var/www/meusite.com/public_html/index.html

#### criar usuario ftp
sudo adduser ftpuser

#### definir diretorio do site como home do usuario
sudo usermod ftpuser -d /var/www/meusite.com

#### criar grupo em comum para o apache e usuario ftp para que o wordpress possa fazer uploads na pasta do usuario
sudo groupadd publishers <br>
sudo usermod -a -G publishers ftpuser <br>
sudo usermod -a -G publishers www-data

#### verificar se os grupos foram atribuidos corretamente
groups ftpuser && groups www-data

#### definir usuario ftp e grupo publishers como proprietários do diretório
sudo chown -R ftpuser:publishers /var/www/meusite.com/

#### definir permissoes adequadas para arquivos e diretorios
sudo chown nobody:nogroup /var/www/ <br>
sudo chmod 2775 /var/www <br>
sudo find /var/www -type d -exec chmod 2775 {} + <br>
sudo find /var/www -type f -exec chmod 0664 {} +


## Apache Configuration
sudo nano /etc/apache2/sites-available/meusite.com.conf

--- Inicio do arquivo ---

\<VirtualHost *:80> <br>
  ServerAdmin email@meusite.com <br>
  ServerName meusite.com <br>
  ServerAlias www.meusite.com <br>
  DocumentRoot /var/www/meusite.com/public_html <br>
  ErrorLog ${APACHE_LOG_DIR}/error.log <br>
  CustomLog ${APACHE_LOG_DIR}/access.log combined <br>
\</VirtualHost>

--- Fim do arquivo ---


#### habilitar website e reiniciar apache
sudo a2ensite meusite.com.conf && sudo service apache2 restart



## Database Configuration
https://linuxize.com/post/how-to-create-mysql-user-accounts-and-grant-privileges/

sudo mysql -p

CREATE DATABASE db_name

CREATE USER 'user'@'%' IDENTIFIED BY 'password';

GRANT ALL PRIVILEGES ON db_name.* TO 'user'@'%';

SHOW GRANTS FOR 'user'@'%';


## FTP Configuration
https://devanswers.co/installing-ftp-server-vsftpd-ubuntu-18-04/

sudo apt install vsftpd -y && sudo nano /etc/vstpd.conf

--- Inicio do arquivo ---

listen=NO
<br> listen_ipv6=YES
<br> anonymous_enable=YES
<br> local_enable=YES
<br> write_enable=YES
<br> local_umask=022
<br> dirmessage_enable=YES
<br> use_localtime=YES
<br> xferlog_enable=YES
<br> connect_from_port_20=YES
<br> data_connection_timeout=120
<br> chroot_local_user=YES
<br> allow_writeable_chroot=YES
<br> secure_chroot_dir=/var/run/vsftpd/empty
<br> pam_service_name=vsftpd
<br> tcp_wrappers=YES
<br> force_dot_files=YES
<br> pasv_enable=NO

--- Fim do arquivo ---
