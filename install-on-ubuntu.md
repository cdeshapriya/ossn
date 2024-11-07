## Introduction

This guide explains how  you can install ossn on Ubuntu 24.04

### Update the system

```
sudo apt update  
sudo apt upgrade -y
```

### Install LAMP Stack

```
sudo apt-get install apache2 libapache2-mod-php8.1  mariadb-server php8.1 php8.1-mysql php8.1-curl php8.1-gd     
php8.1-json php8.1-opcache php8.1-xml mcrypt php8.1-mcrypt php8.1-cgi php8.1-json php8.1-zip
```

### Download Open Source Social Network(OSSN)

```
wget https://www.opensource-socialnetwork.org/download_ossn/latest/build.zip  
sudo unzip build.zip -d /var/www/html  
sudo chown -R www-data:www-data /var/www/html/ossn
```

### Configure database for OSSN

```
sudo mysql_secure_installation  
sudo mysql -u root -p
```

```
  SET GLOBAL sql_mode='';  
  CREATE DATABASE ossndb;  
  CREATE USER 'ossnuser'@'localhost' IDENTIFIED BY 'password-here';  
  GRANT ALL PRIVILEGES ON ossndb.* TO 'ossnuser'@'localhost';  
  FLUSH PRIVILEGES;
```
```
  \q
```
  
### Configure Apache for OSSN

```
cd /etc/apache2/site-available  
sudo cp 000-default.conf ossn.conf
```

```
sudo vim ossn.conf
```

```
<VirtualHost *:80>
   ServerAdmin admin@example.com  
   DocumentRoot /var/www/html/ossn/  
   ServerName example.com  
   ServerAlias www.example.com  
   <Directory /var/www/html/ossn/> 
      Options FollowSymLinks  
      AllowOverride All  
      Order allow,deny  
      allow from all  
</Directory>
   ErrorLog /var/log/apache2/ossn_log  
   CustomLog /var/log/apache2/ossn_custom_log common    
</VirtualHost>
```

```
sudo a2dissite 000-default.conf  
sudo a2ensite ossn.conf  
sudo a2enmod rewrite  
sudo systemctl restart apache2
```

### Configure php.ini

```
sudo vim /etc/php/8.1/cli/php.ini  
allow_url_fopen = On  
file_uploads = On
upload_max_filesize = 32M
```
 
### Create directory to save all ossn data

```
sudo mkdir -p /var/www/ossn_data
sudo chown -R www-data:www-data ossn_data
```

### Finish the installation in web browser

visit http://localhost or your ip address http://192.168.1.101
