## Project Name: Web Server

### Project Roles
| **Documentation** |
| ----------------- |
| Kao Xiong         |


### Apache 

##### Installing Apache and PHP-FPM
In addition to Apache and PHP-FPM, we must also install the PHP FastCGI Apache module which is named libapache2-mod-fastcgi.

First, update the apt repository to ensure you have the latest packages.
~~~shell
sudo apt-get update
~~~
Next, install the necessary packages:
~~~shell
sudo apt-get install apache2 libapache2-mod-fastcgi php-fpm
~~~

##### Configuring Apache and PHP-FPM
Edit the Apache configuration file and change the port number of Apache.
~~~shel
sudo nano /etc/apache2/ports.conf
~~~
Find the following line:
~~~shell
Listen 80
~~~
Chang it to: 
~~~shell
Listen 8080
~~~
Save and exit ```ports.conf```.

Next we'll edit the default virtual host file of Apache. The ```<VirtualHost>``` directive in this file is set to serve sites only on port ```80```, so we'll have to change that as well. Open the default virtual host file.

~~~shell
sudo nano /etc/apache2/sites-available/000-default.conf
~~~
The first line should be:
~~~shell
<VirtualHost *:80>
~~~
Change it to:
~~~shell
<VirtualHost *:8080>
~~~
Save the fle and reload Apache.
~~~shell
sudo systemctl reload apache2
~~~
Verify that Apache is now listening on 8080
~~~shell
sudo netatat -tlpn
~~~
The output should look like the follwing example, with **apache2** listening on **:::8080**
~~~shell
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address     Foreign Address      State    PID/Program name
tcp        0      0 0.0.0.0:22        0.0.0.0:*            LISTEN   1086/sshd
tcp6       0      0 :::8080           :::*                 LISTEN   4678/apache2
tcp6       0      0 :::22             :::*                 LISTEN   1086/sshd
~~~
Once you verify that Apache is listening on the correct port, you can configure support for PHP and FastCGI.

##### Configuring Apache to Use mod_fastcgi
Apache serves PHP pages using mod_php by default, but it requires additional configuration to work with PHP-FPM.
~~~shell
sudo a2dismod php7.0
~~~
We will be adding a configuration block for mod_fastcgi which depends on mod_action. mod_action is disabled by default, so we first need to enable it.
~~~shell
sudo a2enmod actions
~~~
These configuration directives pass requests for .php files to the PHP-FPM UNIX socket.
~~~shell
sudo nano /etc/apache2/mods-enabled/fastcgi.conf
~~~
Add the following lines within the <IfModule mod_fastcgi.c> . . . </IfModule> block, below the existing items in that block:
~~~shell
 AddType application/x-httpd-fastphp .php
 Action application/x-httpd-fastphp /php-fcgi
 Alias /php-fcgi /usr/lib/cgi-bin/php-fcgi
 FastCgiExternalServer /usr/lib/cgi-bin/php-fcgi -socket /run/php/php7.0-fpm.sock -pass-header Authorization
 <Directory /usr/lib/cgi-bin>
    Require all granted
 </Directory>
~~~
Save the changes you made to fastcgi.conf and do a configuration test.
~~~shell
sudo apachectl -t
~~~
Reload Apache if **Syntax OK** is displayed. If you see the warning Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to suppress this message., that's fine. It doesn't affect us now.
~~~shell
sudo systemctl reload apache2
~~~

##### Verifying PHP Functionality

Check if PHP works by creating a ```phpinfo()``` file and accessing it from your web browser.

~~~shell
echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php
~~~

To see the file in a browser, go to ```http://your_ip_address:8080/info.php```. This will give you a list of configuration settings PHP is using.

![at text](N-Arctica-Web-Server/YbWDj9i.png)

### Copyright & License
 This work is licensed Apache License Version 2.0 
