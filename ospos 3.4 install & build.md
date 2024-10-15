# OpenSource POS 3.4 Installation and Build on Ubuntu 24.04

## Install Apache + MySql + PHP
```sh
sudo apt install apache2 mysql-server 
sudo apt install  php php-mysql libapache2-mod-php php-gd php-bcmath php-intl php-mbstring  php-curl  php-xml
```

## Install software **tools**
We need Composer for our PHP code and npm for our HTML/Css/Javascript/bootstrap code
```sh
sudo apt install npm
sudo apt install composer
```



## Download and install Open Source POS
Get last development OpensourcePos release (3.4 dev), and copy them in the /var/www/html folder.
User set to www-data so that Apache can read them.

```sh
wget https://github.com/opensourcepos/opensourcepos/archive/refs/heads/master.zip
unzip master.zip
sudo cp -r opensourcepos-master/* /var/www/html
sudo chown -R www-data:www-data /var/www/html/
```

## compile Open Source POS project
from  the folder **var/www/html*  run the followinng  commands:
```sh
cd /var/www/html
sudo composer install
sudo npm install
sudo npm run build
sudo chown -R www-data:www-data /var/www/html/

```
Files are owned by  www-data so that file ownership will let Apache read them.

## Create a MySQL database & user; import database tables 
open a terminal windows, and get mysql admine role
```sh 
sudo mysql -u root -p
```
When in mysql consolle, give following commands:
```sh
CREATE DATABASE ospos;
CREATE USER 'osposuser'@'localhost' IDENTIFIED BY 'your_db_password_here';
GRANT ALL PRIVILEGES ON ospos.* TO 'osposuser'@'localhost';
FLUSH PRIVILEGES;
exit
```
Now we need an sql file, to import the tables used by ospos. 
the sql file has just been created when compiling with npm our project 
```sh
mysql -p --user=osposuser ospos < /var/www/html/app/Database/database.sql
```
The consolle will ask for the ospsuser password before importi the sql tables
## Configure Open Source POS
Edit file app/Config/Database.php, section public array $default,  to update mysql account user/pwd 
| Field | Value |
| ------ | ------ |
|'hostname' |  'localhost'|
|'username' | 'osposuser'|
|'password' | 'your_db_password_here' |
|'database' | 'ospos'|

## Configure Apache
We need to create and enable our site virtualhost configuration for Apache:
```sh
sudo -u www-data nano /etc/apache2/sites-available/opensourcepos.conf
```
In the editor add the following lines
```sh
<VirtualHost *:80>
    ServerName ___YOUR_SERVEr_IP_OR_HOSTNAME___
    DocumentRoot /var/www/html/
    <Directory /var/www/html/>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```
Remembrer to replace your_server_ip_or_hostname with your server's IP address or hostname. Save and close the file.

Disable default site Apache Ubuntu welcome page
```sh
sudo a2dissite 000-default.conf
```

You also need to enable the rewrite module and the new virtual host configuration:
```sh
sudo a2enmod rewrite
sudo a2ensite opensourcepos.conf
sudo systemctl reload apache2
```

## LOGIN
    Log in using via the browser the address your_serve_ip_or_address/public:
    • Username: admin 
    • Password: pointofsale 
    
    
## EDIT Software IDE
You can try
- Eclipse 2024-09 + PHP Development tools 8,0 plugin 
- PHP Storm By IntelliJ

