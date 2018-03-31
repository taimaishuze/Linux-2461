## Project Name: Web Server




## HTTP SERVER
### Apache HTTP Server Version 2.4
##### Overview
|Related Modules |Related Directives |
| -------------- |:-----------------:|
|core            |\<virtualHost\>    |
|mpm_common      |Listen             |

When httpd starts, it binds to some port and address on the local machine and waits for incoming requests. By default, it listens to all addresses on the machine. However, it may need to be told to listen on specific ports, or only on selected addresses, or a combination of both. This is often combined with the Virtual Host feature, which determines how httpd responds to different IP addresses, hostnames and ports.

The Listen directive tells the server to accept incoming requests only on the specified port(s) or address-and-port combinations. If only a port number is specified in the Listen directive, the server listens to the given port on all interfaces. If an IP address is given as well as a port, the server will listen on the given port and interface. Multiple Listen directives may be used to specify a number of addresses and ports to listen on. The server will respond to requests from any of the listed addresses and ports.

For example, to make the server accept connections on both port 80 and port 8000, on all interfaces, use:
  ~~~shell
  Listen 80
  Listen 8000
  ~~~

To make the server accept connections on port 80 for one interface, and port 8000 on another, use
  ~~~shell
  Listen 192.0.2.1:80
  Listen 192.0.2.5:8000
  ~~~  
 
 IPv6 addresses must be enclosed in square brackets, as in the following example:
  ~~~shell
  Listen [2001:db8::a00:20ff:fea7:ccea]:80
  ~~~  

##### Changing Listen configuration on restart
When httpd is restarted, special consideration must be made for changes to Listen directives. During a restart, httpd keeps ports bound (as in the original configuration) to avoid generating "Connection refused" errors for any new attempts to connect to the server. If changes are made to the set of Listen directives used which conflict with the old configuration, configuration will fail and the server will terminate.

For example, changing from configuration:
  ~~~shell
  Listen 127.0.0.1:80
  ~~~

to the following may fail, because binding to port 80 across all addresses conflicts with binding to port 80 on just 127.0.0.1.
  ~~~shell
  Listen 80
  ~~~
  
  To have such configuration changes take effect, it is necessary to stop and then start the server.
### Copyright & License
 This work is licensed Apache License Version 2.0 
