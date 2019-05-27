# Configurar Servidor Web na Amazon

## Apache Configuration

#### create website root directory
sudo mkdir -p /var/www/iocomunica.com/public_html

#### change owner to current user
sudo chown -R $USER:$USER /var/www/iocomunica.com/public_html

#### or change owner to a specifc ftp user
sudo chown -R ftpuser:publishers /var/www/emkt/public_html

#### change to files permission
sudo chmod -R 755 /var/www

#### create a index file on the root directory
nano /var/www/iocomunica.com/public_html/index.html
write "Hello World!" save and exit
  
#### create domain configuration file
sudo nano /etc/apache2/sites-available/iocomunica.com.conf

--- DOMAIN-NAME.COM.CONF START ---

\<VirtualHost *:80> <br>
  ServerAdmin suporte@iocomunica.com <br>
  ServerName iocomunica.com <br>
  ServerAlias www.iocomunica.com <br>
  DocumentRoot /var/www/iocomunica.com/public_html <br>
  ErrorLog ${APACHE_LOG_DIR}/error.log <br>
  CustomLog ${APACHE_LOG_DIR}/access.log combined <br>
\</VirtualHost>

--- DOMAIN-NAME.COM.CONF END ---


#### enable website 
sudo a2ensite iocomunica.com.conf

#### restart apache
sudo service apache2 restart



## Database Configuration

sudo mysql -p

CREATE DATABASE db_name

CREATE USER 'username'@'*' IDENTIFIED BY 'password';

#### conceder privilegio administrativo em todas tabelas de db_name
GRANT ALL PRIVILEGES ON db_name.* TO 'username'@'*';

#### conceder privilegio administrativo em todas tabelas de todos dbs a partit do ip 123.123.123.123
GRANT ALL PRIVILEGES ON *.* TO 'database_user'@'123.123.123.123';

#### outros exemplos
GRANT SELECT, INSERT, DELETE ON database_name.* TO database_user@'localhost';
GRANT ALL PRIVILEGES ON database_name.table_name TO 'database_user'@'localhost';

#### exibir privilegios
SHOW GRANTS FOR 'database_user'@'localhost';

#### revogar privilegios
REVOKE ALL PRIVILEGES ON database_name.* TO 'database_user'@'localhost';

#### exluir usuario
DROP USER 'user'@'localhost'


## FTP Configuration

sudo apt install vsftpd

sudo adduser ftpuser

sudo usermod ftpuser -d /var/www/iocomunica.com 


#### create and append a Group for ftp and apache users
groupadd publishers 
usermod -a -G publishers ftpuser
usermod -a -G publishers www-data

#### display groups for user
groups ftpuser
groups www-data



#### grant the propers permission to files and folders
sudo chown nobody:nogroup /var/www/ <br>
chmod 2775 /var/www <br>
sudo find /var/www -type d -exec chmod 2775 {} + <br>
sudo find /var/www -type f -exec chmod 0664 {} + <br>


#### configure ftp server
sudo nano /.../vstpd.conf <br>

--- VSTPD.CONF START ---
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

--- VSTPD.CONF END ---
