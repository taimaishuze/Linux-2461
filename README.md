# Project Web-Server

The desired domain names should point to your Droplet's IP address in the DigitalOcean control panel. See Step 3 of How To Set Up a Host Name with DigitalOcean for an example of how to do this. If you host your domains' DNS elsewhere, you should create appropriate A records there instead.## Project Name: Web Server

### Project Roles
| **Documentation** |
| ----------------- |
| Kao Xiong         |


### Installing sftp 

 ##### Search for desire sftp program

 Search command:
 ~~~shell
 apt search sftp
 ~~~
 After finding desire sftp program time to install

 ##### Installing sftp program

 Command to install the sftp program:
 ~~~shell
 sudo apt-get install <sftp program>
 ~~~


### SootSplash

 ##### Step 1 Adding User 

 Makaing sure you have admin right
 ~~~shell
 sudo adduser <Username>
 ~~~
 ~~~shell
 Enter new UNIX password:<Password>
 ~~~
 Now you be asked for your full name and other information
 After inputting your information you will need to confirm it
 After you have confirm your information
 You are finally a User on SootSplash

 ##### Step 2 Generating public/private rsa key pair

 To generate the key use the command:
 ~~~shell
 ssh-keygen -t rsa -f <Username>_rsa
 ~~~
 Yoou be ask for your passphrase
 ~~~shell
 Enter passphrase (empty for no passphrase): 
 ~~~
 Once passphase has gone through the following message should appear:
 ~~~shell
 Your identification has been saved in <Username>_rsa.
 Your public key has been saved in <Username>_rsa.pub.
 ~~~
 You will also be shown your key fingerprint
 and your key's randomart image

 ##### Step 3 Adding SootSplash.csci2461.com to known host list

 Command to establish the connection:
 ~~~shell
 rsync -Pav <Username>_rsa.pub <Username>@sootsplash.csci2461.com:./2461-70/
 ~~~
 You will be warn
 ~~~shell
 Are you sure you want to continue connecting (yes/no)?
 ~~~
 Type ```yes``` continue connecting or ```no``` to not connect
 You will be warn one last time:
 ~~~shell
 Warning: Permanently added 'sootsplash.csci2461.com,167.99.232.136' (ECDSA) to the list of known hosts.
 ~~~
 Then be ask for your password, this is the ```<Password>``` you set in **Step 1** 
 ~~~shell
 <Username>@sootsplash.csci2461.com's password: 
 ~~~
 Directory will be created 

 ##### Step 4 Installing \<Username>_rsa.pub and key

 Enter following command to install <Username>_rsa.pub
 ~~~shell
 ssh-copy-id -i <Username>_rsa <Username>@sootsplash.csci2461.com
 ~~~
 Before it install you will be asked to enter your ```<Password>``` for it to be added
 ~~~shell
 <Username>@sootsplash.csci2461.com's password: 
 ~~~
 Keys will then be install

 ##### Step 5 Finally connecting to SootSplash.csci2461.com

 The command to connect is:
 ~~~shell
 ssh -i <Username>_rsa <Username>@sootsplash.csci2461.com
 ~~~
 You will be asked to enter your passphrase:
 ~~~shell
 Enter passphrase for key '<Username>_rsa': 
 ~~~
 This will connect you to SootSplash

### Configure Nginx as a Web Server and Reverse Proxy for Apache on One Ubuntu Server

 **Source**: https://www.digitalocean.com/community/tutorials/how-to-configure-nginx-as-a-web-server-and-reverse-proxy-for-apache-on-one-ubuntu-16-04-server
 #### Prerequisites
 * A new Ubuntu 16.04 Droplet.

 * A standard user account with ```sudo``` privileges. You can set up a standard account by following Steps 2 and 3 of the Initial Server Setup with Ubuntu 16.04.

 * The desired domain names should point to your Droplet's IP address in the DigitalOcean control panel. See Step 3 of How To Set Up a Host Name with DigitalOcean for an example of how to do this. If you host your domains' DNS elsewhere, you should create appropriate A records there instead.

 ##### Step 1 Installing Apache and PHP-FPM
 In addition to Apache and PHP-FPM, we must also install the PHP FastCGI Apache module which is named libapache2-mod-fastcgi.

 First, update the apt repository to ensure you have the latest packages.
 ~~~shell
 sudo apt-get update
 ~~~
 Next, install the necessary packages:
 ~~~shell
 sudo apt-get install apache2 libapache2-mod-fastcgi php-fpm
 ~~~

 ##### Step 2 Configuring Apache and PHP-FPM

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

 **Note**: Web servers are generally set to listen on **```127.0.0.1:8080```** when configuring a reverse proxy but doing so would set the value of PHP's environment variable **SERVER_ADDR** to the loopback IP address instead of the server's public IP. Our aim is to set up Apache in such a way that its websites do not see a reverse proxy in front of it. So, we will configure it to listen on **8080** on all IP addresses.


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
 Verify that Apache is now listening on 8080https://www.digitalocean.com/community/tutorials/how-to-configure-nginx-as-a-web-server-and-reverse-proxy-for-apache-on-one-ubuntu-16-04-server
 ~~~shell
 sudo netatat -tlpn
 ~~~
 The output should look like the follwing example, with **apache2** listening on **:::8080**
 ~~~shell
 Active Internet connections (only servers)
 Proto Recv-Q Send-Q Local Address     Foreign Address      State    PID/Program namehttps://www.digitalocean.com/community/tutorials/how-to-configure-nginx-as-a-web-server-and-reverse-proxy-for-apache-on-one-ubuntu-16-04-server
 tcp        0      0 0.0.0.0:22        0.0.0.0:*            LISTEN   1086/sshd
 tcp6       0      0 :::8080           :::*                 LISTEN   4678/apache2
 tcp6       0      0 :::22             :::*                 LISTEN   1086/sshd
 ~~~
 Once you verify that Apache is listening on the correct port, you can configure support for PHP and FastCGI.

 ##### Step 3 Configuring Apache to Use mod_fastcgi

 Apache serves PHP pages using ```mod_php``` by default, but it requires additional configuration to work with PHP-FPM.

 **Note**: If you are trying this tutorial on an existing installation of **LAMP** with mod_php, disable it first with:

 ~~~shell
 sudo a2dismod php7.0
 ~~~
 We will be adding a configuration block for ```mod_fastcgi``` which depends on ```mod_action```. ```mod_action``` is disabled by default, so we first need to enable it.
 ~~~shell
 sudo a2enmod actions
 ~~~
 These configuration directives pass requests for ```.php``` files to the PHP-FPM UNIX socket.
 ~~~shell
 sudo nano /etc/apache2/mods-enabled/fastcgi.conf
 ~~~
 Add the following lines within the ```<IfModule mod_fastcgi.c> . . .</IfModule> block```, below the existing items in that block:
 ~~~shell
  AddType application/x-httpd-fastphp .php
  Action application/x-httpd-fastphp /php-fcgi
  Alias /php-fcgi /usr/lib/cgi-bin/php-fcgi
  FastCgiExternalServer /usr/lib/cgi-bin/php-fcgi -socket /run/php/php7.0-fpm.sock -pass-header Authorization
  <Directory /usr/lib/cgi-bin>
     Require all granted
  </Directory>
 ~~~
 Save the changes you made to ```fastcgi.conf``` and do a configuration test.
 ~~~shell
 sudo apachectl -t
 ~~~
 Reload Apache if **Syntax OK** is displayed. If you see the warning ```Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to suppress this message.```, that's fine. It doesn't affect us now.
 ~~~shell
 sudo systemctl reload apache2
 ~~~
 Now let's make sure we can serve PHP from Apache.

 ##### Step 4 Verifying PHP Functionality

 Check if PHP works by creating a ```phpinfo()``` file and accessing it from your web browser.

 ~~~shell
 echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php
 ~~~

 To see the file in a browser, go to ```http://your_ip_address:8080/info.php```. This will give you a list of configuration settings PHP is using.

 ![alt text](https://github.com/taimaishuze/N-Arctica-Web-Server/blob/master/YbWDj9i.png)
 ![alt text](https://github.com/taimaishuze/N-Arctica-Web-Server/blob/master/363bUHT.png)

 At the top of the page, check that ***Server API*** says ***FPM/FastCGI***. About two-thirds of the way down the page, the ***PHP Variables*** section will tell you the ***SERVER_SOFTWARE*** is Apache on Ubuntu. These confirm that ```mod_fastcgi``` is active and Apache is using ```PHP-FPM``` to process PHP files.

 ##### Step 5 Creating Virtual Hosts for Apache

 Let's create Apache virtual host files for the domains ```foobar.net``` and ```test.io```. To do that, we'll first create document root directories for both sites and place some default files in those directories so we can easily test our configuration.

 First, create the root directories:
 ~~~shell
 sudo mkdir -v /var/www/{foobar.net,test.io}
 ~~~
 Then create an ```index``` file for each site.
 ~~~shell
 echo "<h1 style='color: green;'>Foo Bar</h1>" | sudo tee /var/www/foobar.net/index.html
 ~~~
 ~~~shell
 echo "<h1 style='color: red;'>Test IO</h1>" | sudo tee /var/www/test.io/index.html
 ~~~
 Then create a ```phpinfo()``` file for each site so we can test PHP is configured properly.
 ~~~shell
 echo "<?php phpinfo(); ?>" | sudo tee /var/www/foobar.net/info.php
 ~~~
 ~~~shell
 echo "<?php phpinfo(); ?>" | sudo tee /var/www/test.io/info.php
 ~~~
 Now create the virtual host file for the ```foobar.net``` domain.
 ~~~shell
 sudo nano /etc/apache2/sites-available/foobar.net.conf
 ~~~
 Place the following dirctive in this new file:
 ~~~shell
 <VirtualHost *:8080>
     ServerName foobar.net
     ServerAlias www.foobar.net
     DocumentRoot /var/www/foobar.net
     <Directory /var/www/foobar.net>
         AllowOverride All
     </Directory>
 </VirtualHost>
 ~~~

 **Note**: ```AllowOLverride All``` enables ```.htaccess``` support.

 These are only the most basic directives. For a complete guide on setting up virtual hosts in Apache, see How To Set Up Apache Virtual Hosts on Ubuntu 16.04.

 Save and close the file. Then create a similar configuration ```for test.io```.
 ~~~shell
 sudo nano /etc/apache2/sites-available/test.io.conf
 ~~~
 ~~~shell
 <VirtualHost *:8080>
     ServerName test.io
     ServerAlias www.test.io
     DocumentRoot /var/www/test.io
     <Directory /var/www/test.io>
         AllowOverride All
     </Directory>
 </VirtualHost>
 ~~~
 Now that both Apache virtual hosts are set up, enable the sites using the ```a2ensite``` command. This creates a symbolic link to the virtual host file in the ```sites-enabled``` directory.
 ~~~shell
 sudo a2ensite foobar.net
 ~~~
 ~~~shell
 sudo a2ensite test.io
 ~~~
 Check Apache for configuration errors again.
 ~~~shell
 sudo apachectl -t
 ~~~
 Reload Apache if **Syntax OK** is displayed.
 ~~~shell
 sudo systemctl reload apache2
 ~~~
 To confirm the sites are working, open ```http://foobar.net:8080``` and ```http://test.io:8080``` in your browser and verify that each site displays its index.html file.

 You should see the following results:

 ![alt text](https://github.com/taimaishuze/N-Arctica-Web-Server/blob/master/3.png)

 ![alt text](https://github.com/taimaishuze/N-Arctica-Web-Server/blob/master/4.png)

 Also, check that PHP is working by accessing the info.php files for each site. Visit ```http://foobar.net:8080/info.php``` and ```http://test.io:8080/info.php``` in your browser.

 You should see the same PHP configuration spec list on each site as you saw in Step 4. We now have two websites hosted on Apache at port ```8080```

 ##### Step 6 Installing and Configuring Nginx

 In this step we'll install Nginx and configure the domains ```example.com``` and ```sample.org``` as Nginx's virtual hosts. For a complete guide on setting up virtual hosts in Nginx, see How To Set Up Nginx Server Blocks (Virtual Hosts) on Ubuntu 16.04.

 Install Nginx using the package manager.
 ~~~shell
 sudo apt-get install nginx
 ~~~
 Then remove the default virtual host's symlink since we won't be using it any more. We'll create our own default site later (```example.com```).
 ~~~shell
 sudo rm /etc/nginx/sites-enabled/default
 ~~~
 Now we'll create virtual hosts for Nginx using the same procedure we used for Apache. First create document root directories for both the websites:
 ~~~shell
 sudo mkdir -v /usr/share/nginx/{example.com,sample.org}
 ~~~
 As we did with Apache's virtual hosts, we'll again create index and phpinfo() files for testing after setup is complete.
 ~~~shell
 echo "<h1 style='color: green;'>Example.com</h1>" | sudo tee /usr/share/nginx/example.com/index.html
 ~~~
 ~~~shell
 echo "<h1 style='color: red;'>Sample.org</h1>" | sudo tee /usr/share/nginx/sample.org/index.html
 ~~~
 ~~~shell
 echo "<?php phpinfo(); ?>" | sudo tee /usr/share/nginx/example.com/info.php
 ~~~
 ~~~shell
 echo "<?php phpinfo(); ?>" | sudo tee /usr/share/nginx/sample.org/info.php
 ~~~
 Now create a virtual host file for the domain ```example.com```.
 ~~~shell
 sudo nano /etc/nginx/sites-available/example.com
 ~~~
 Nginx calls ```server {. . .}``` areas of a configuration file **server blocks**. Create a server block for the primary virtual host, example.com. ```The default_server``` configuration directive makes this the default virtual host which processes HTTP requests that do not match any other virtual host.

 Paste the following into the file for example.com:
 ~~~shell
 server {
     listen 80 default_server;

     root /usr/share/nginx/example.com;
     index index.php index.html index.htm;

     server_name example.com www.example.com;
     location / {
         try_files $uri $uri/ /index.php;
     }

     location ~ \.php$ {
         fastcgi_pass unix:/run/php/php7.0-fpm.sock;
         include snippets/fastcgi-php.conf;
     }
 }
 ~~~
 Save and close the file. Now create a virtual host file for Nginx's second domain, sample.org.
 ~~~shell
 sudo nano /etc/nginx/sites-available/sample.org
 ~~~
 The server block for sample.org should look like this:
 ~~~shell
 server {
     root /usr/share/nginx/sample.org;
     index index.php index.html index.htm;

     server_name sample.org www.sample.org;
     location / {
         try_files $uri $uri/ /index.php;
     }

     location ~ \.php$ {
         fastcgi_pass unix:/run/php/php7.0-fpm.sock;
         include snippets/fastcgi-php.conf;
     }
 }
 ~~~
 Save and close the file. Then enable both the sites by creating symbolic links to the sites-enabled directory.
 ~~~shell
 sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/example.com
 ~~~
 ~~~shell
 sudo ln -s /etc/nginx/sites-available/sample.org /etc/nginx/sites-enabled/sample.org
 ~~~
 Do an Nginx configuration test:
 ~~~shell
 sudo nginx -t
 ~~~
 Then reload Nginx if **OK** is displayed.
 ~~~shell
 sudo systemctl reload nginx
 ~~~
 Now acccess the ```phpinfo()``` file of your Nginx virtual hosts in a web browser by visiting ```http://example.com/info.php``` and ```http://sample.org/info.php```. Look under the PHP Variables sections again.
 ![alt text](https://github.com/taimaishuze/N-Arctica-Web-Server/blob/master/A43puCy.png)

 **["SERVER_SOFTWARE"]** should say ```nginx```, indicating that the files were directly served by Nginx. **["DOCUMENT_ROOT"]** should point to the directory you created earlier in this step for each Nginx site.

 At this point, we have installed Nginx and created two virtual hosts. Next we will configure Nginx to proxy requests meant for domains hosted on Apache.

 ##### Step 7 Configuring Nginx for Apache's Virtual Hosts

### Copyright & License
 This work is licensed Apache License Version 2.0 

