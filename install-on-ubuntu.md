## Introduction

This guide explains how  you can install ossn on Ubuntu 24.04

## Update the system

```
sudo apt update  
sudo apt upgrade -y
```

## Install LAMP Stack

```
sudo apt-get install apache2 libapache2-mod-php8.1 libapache2-mod-fcgid php8.1-fpm  mariadb-server php8.1 php8.1-mysql php8.1-curl php8.1-gd  php-json php8.1-opcache php8.1-xml mcrypt php8.1-cgi  php8.1-zip
```

## Download Open Source Social Network(OSSN)

```
wget https://www.opensource-socialnetwork.org/download_ossn/latest/build.zip
```

```
sudo unzip build.zip -d /var/www/html
```

```
sudo chown -R www-data:www-data /var/www/ossn_data
```

## Note

If you have an ossn premium version license, you must first login to your premium version account before downloading it. In this instance, you must skip the ```wget``` command line above. 

## Configure database for OSSN

```
sudo mysql_secure_installation
```

```
sudo mysql -u root -p
```

```
SET GLOBAL sql_mode='';
```

```
CREATE DATABASE ossndb;  
```

```
CREATE USER 'ossnuser'@'localhost' IDENTIFIED BY 'password-here';  
```

```
GRANT ALL PRIVILEGES ON ossndb.* TO 'ossnuser'@'localhost';
```

```
FLUSH PRIVILEGES;
```

```
\q
```
  
## Configure Apache for OSSN

### If you have a ufw firewall set up, open up the http and https ports

```
sudo ufw allow "Apache Full"
```

### Enabling mod_ssl


To utilize any TLS certificates, first enable mod_ssl, an Apache module that supports SSL encryption.

Enable `mod_ssl` with the `a2enmod` command:

```
sudo a2enmod ssl
```

### Restart Apache to activate the module:

```
sudo systemctl restart apache2
```

With Apache now prepared for encryption, we can proceed to generate a new TLS certificate. The certificate will contain essential information about your site and will be accompanied by a key file that enables the server to safely manage encrypted data.

```
sudo openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout /etc/ssl/private/srilankan.social.key -out /etc/ssl/certs/srilankan.social.crt

```

Enter required information as mentioned in the output below

````
.................+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*...+.........+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.+...+.........+....................+...+......+....+...+...+..+...+...................+............+........+.+......+...+..+.+........+.......+.................+.......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
........+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*..+.+...+.....+.......+.....+.......+..+...............+....+..+.......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.....+..+...+...+....+..+.+...+.....+......+....+.....+.+...+..+.............+...+...+.....+....+.....+.+......+.........+............+..+............+....+.....+.........+...............+..........+...+..............+....+..+....+...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:LK
State or Province Name (full name) [Some-State]:Western
Locality Name (eg, city) []:Colombo
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Libresoft Technologies            
Organizational Unit Name (eg, section) []:IT
Common Name (e.g. server FQDN or YOUR name) []:srilankan.social
Email Address []:info@srilankan.social
```

```
cd /etc/apache2/sites-available
```

```
sudo cp 000-default.conf ossn.conf
```

```
sudo vim ossn.conf
```

```
<VirtualHost *:443>
   ServerAdmin admin@srilankan.social  
   DocumentRoot /var/www/html/ossn/  
   ServerName srilankan.social
   SSLEngine on
   SSLCertificateFile /etc/ssl/certs/srilankan.social.key
   SSLCertificateKeyFile /etc/ssl/private/srilankan.social.key
   ServerAlias www.srilankan.social
   
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

### HTTPS site for Ossn



### Configure php.ini

```
sudo vim /etc/php/8.1/cli/php.ini
```

```
allow_url_fopen = On  
file_uploads = On
upload_max_filesize = 32M
```
 
### Create directory to save all ossn data

```
sudo mkdir -p /var/www/ossn_data
```

```
sudo chown -R www-data:www-data ossn_data
```

### Finish the installation in web browser

visit http://localhost or your ip address http://192.168.1.101
