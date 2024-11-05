### Update the system
sudo apt update  
sudo apt upgrade -y

### Install LAMP
sudo apt-get install apache2 libapache2-mod-php7.0 mariadb-server php7.0 php7.0-mysql php7.0-curl php7.0-gd     
php7.0-json php7.0-opcache php7.0-xml mcrypt php7.0-mcrypt php7.0-cgi php7.0-json php7-0-zip

### Download Open Source Social Network(OSSN)
wget https://www.opensource-socialnetwork.org/download_ossn/latest/build.zip  
sudo unzip build.zip -d /var/www/html  
sudo chown -R www-data:www-data /var/www/html/ossn  

### Configure database for OSSN
sudo mysql_secure_installation  
sudo mysql -u root -p  
  SET GLOBAL sql_mode='';  
  CREATE DATABASE ossndb;  
  CREATE USER 'ossnuser'@'localhost' IDENTIFIED BY 'password-here';  
  GRANT ALL PRIVILEGES ON ossndb.* TO 'ossnuser'@'localhost';  
  FLUSH PRIVILEGES;  
  \q  
  
### Configure Apache for OSSN
cd /etc/apache2/site-available  
sudo cp 000-default.conf ossn.conf  

sudo vim ossn.conf 

`<VirtualHost *:80>`  
ServerAdmin admin@example.com  
DocumentRoot /var/www/html/ossn/  
ServerName example.com  
ServerAlias www.example.com  
`<Directory /var/www/html/ossn/>`  
Options FollowSymLinks  
AllowOverride All  
Order allow,deny  
allow from all  
`</Directory>`  
ErrorLog /var/log/apache2/ossn_log  
CustomLog /var/log/apache2/ossn_custom_log common    
`</VirtualHost>`

sudo a2dissite 000-default.conf  
sudo a2ensite ossn.conf  
sudo a2enmod rewrite  
sudo systemctl restart apache2

### Configure php.ini
sudo vim /etc/php/7.0/cli/php.ini  
&nbsp;&nbsp;allow_url_fopen = On  
&nbsp;&nbsp;file_uploads = On  
&nbsp;&nbsp;upload_max_filesize = 32M  
 
### Create directory to save all ossn data
sudo mkdir /var/www/ossn_data
sudo chown -R www-data:www-data ossn_data

### Finish the installation in web browser
visit http://localhost or your ip address http://192.168.1.101
