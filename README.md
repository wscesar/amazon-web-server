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


#### habilitar website 
sudo a2ensite meusite.com.conf

#### reiniciar apache
sudo service apache2 restart



## Database Configuration
https://linuxize.com/post/how-to-create-mysql-user-accounts-and-grant-privileges/

sudo mysql -p

CREATE DATABASE db_name

CREATE USER 'user'@'%' IDENTIFIED BY 'password';

GRANT ALL PRIVILEGES ON db_name.* TO 'user'@'%';

SHOW GRANTS FOR 'user'@'%';


## FTP Configuration
sudo apt install vsftpd -y && sudo nano /etc/vstpd.conf

--- Inicio do arquivo ---

listen=NO

listen_ipv6=YES

anonymous_enable=YES

local_enable=YES

write_enable=YES

local_umask=022

dirmessage_enable=YES

use_localtime=YES

xferlog_enable=YES

connect_from_port_20=YES

data_connection_timeout=120

chroot_local_user=YES

allow_writeable_chroot=YES

secure_chroot_dir=/var/run/vsftpd/empty

pam_service_name=vsftpd

#userlist_enable=YES

tcp_wrappers=YES

#rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem

#rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key

#ssl_enable=YES

#rsa_cert_file=/etc/ssl/private/vsftpd.pem

#rsa_private_key_file=/etc/ssl/private/vsftpd.pem

#allow_anon_ssl=NO

#force_local_data_ssl=YES

#force_local_logins_ssl=YES

#ssl_tlsv1=YES

#ssl_sslv2=NO

#ssl_sslv3=NO

#require_ssl_reuse=NO

#ssl_ciphers=HIGH

force_dot_files=YES

pasv_enable=NO

#pasv_min_port=40000

#pasv_max_port=50000

--- Fim do arquivo ---
