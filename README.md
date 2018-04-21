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
 You will be asked for your passphrase
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

### How I got an HTTPS SSL certificate with certbot
By: rarar0
Original soruce: https://github.com/rarar0/TFTPslackersOops/edit/master/DocumentationHTTPS.md

Matthew directed me toward this source on how to use certbot: 

https://www.digitalocean.com/community/tutorials/how-to-secure-apache-with-let-s-encrypt-on-ubuntu-14-04

Here are the steps I followed

1) Install certbot with apt-get

```
sudo apt-get update && apt-get install python-certbot-apache
```

2) Add firewall rules!

You need to allow tcp/udp traffic on ports 80 for HTTP and 443 for HTTPS, this can be done in 4 commands.
```
sudo iptables -I INPUT -i eth0 -p udp --dport 80 -j ACCEPT
sudo iptables -I INPUT -i eth0 -p tcp --dport 80 -j ACCEPT
sudo iptables -I INPUT -i eth0 -p tcp --dport 443 -j ACCEPT
sudo iptables -I INPUT -i eth0 -p udp --dport 443 -j ACCEPT
```
Note that the "-i eth0" command is not required

You can list all iptable rules with ```sudo iptables -L```


3)Run certbot and insert the domain you wish to get an SSL cert for

```
sudo certbot --apache -d example.com
```

* If you are having troubles around this step like I did, you may have to restart the apache service using systemctl

```
sudo systemctl restart apache2
```

```
sudo systemctl status apache2
```

And that should be it! The certbot will give you a link to ssllabs allowing you to check and see if you have a working
SSL certificate!

[Image](https://i.imgur.com/pocPsdK.png)

### Copyright & License
 This work is licensed Apache License Version 2.0 

