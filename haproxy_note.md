# Config HA proxy
- 4 essential section in haproxy:
	- global:	# global settings
	- defaults: # default settings
	- frontend: # a frontend accept requests from clients
	- backend: # severs that fulfill the requests 

## global
#####################CONFIG################
```
global
    maxconn 50000
    log /dev/log local0
    user haproxy
    group haproxy
    stats socket /run/haproxy/admin.sock user haproxy group haproxy mode 660 level admin
    nbproc 2
    nbthread 4
    cpu-map auto:1/1-4 0-3
    ssl-default-bind-ciphers ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256
    ssl-default-bind-options ssl-min-ver TLSv1.2 no-tls-tickets
```
### Explain
- maxconn: limit numbers of connections that haproxy will accept. Protect your loadbalance running out of memory 
note: memory consumed by processing connections, stick tables, map files and ACL files

- log: listen at /dev/log or other place 

- user/ group: tell haproxy to drop priviledges after initialization
note: linux require processes to be root when listening port below 1024

- stats socket: enables runtime API, use to dynamically disable servers or health check, change load balancing weights of servers

- nbproc/ nbthread: specify the number of processes or threads, that haproxy spawns on startup 
note: increase efficiency of your load balance 

- ssl-default-bind-ciphers: config SSL/TLS options 
note: such as ssl-min-ver to disable support for older protocols

## defaults: reduce duplication, applay to all frontend and backend
######################CONFIG#####################
```
defaults
    timeout connect 10s
    timeout client 30s
    timeout server 30s
    log global
    mode http
    option httplog
    maxconn 3000
```
### Explain
        	- timout connect, client, server : haproxy will wait a TCP connections to backend server to be established
	- log global: telling each subsequent frontend to use the log settings in global section
	- mode http/tcp
	- maxconn: limits numbers of connections to each frontend will accept, default is 2000
	- option httplog/tcplog: haproxy use a more verbose log format to send messages to syslog                       

## frontend: define a IP and port that clients can connect to, listening IP:port and protocol defination, HTTP validation

##############################CONFIG######################
```
frontend www.mysite.com
    bind 10.0.0.3:80
    bind 10.0.0.3:443 ssl crt /etc/ssl/certs/mysite.pem
    http-request redirect scheme https unless { ssl_fc }
    use_backend api_servers if { path_beg /api/ }
    default_backend web_servers
```
### Explain
- bind:  assigns a listener to a given IP and port
- http-request: responds to the client 
- use_backend: choose a backend pool of servers to respond to incoming requests if a given condition is true 
note: ACL statements (path_beg /api/)
- default_backend: is found in every frontend and gives name of a backend server to send traffic
note: if a request isn't routed by a use_backend or default_backend directive, haproxy will retur 503 status errors


## backend: 
- defines a group of servers that will be load balanced or assigned to handle request, server monitoring, load balancing, queueing
############################CONFIG########################
```
backend web_servers
    balance roundrobin
    cookie SERVERUSED insert indirect nocache
    option httpchk HEAD /
    default-server check maxconn 20
    server server1 10.0.1.3:80 cookie server1
    server server2 10.0.1.4:80 cookie server2
```
### Explain
- loadblance: controls how haproxy select servers to respond to the request, such as roundrobin, leastconn
- cookie: enable cookie-based persistence, it tell haproxy to send a cookie named SERVERUSED to the client 
- option httpchk: haproxy sto send layer 7 (HTTP) health checks instead of layer 4 (TCP) checks to your backend servers 
- default-server: configures defaults for any servers, such as enabling health check, max connections, 
- server: is the heart of the backend

## SAMPLE
```
listen stats
    bind *:8404
    stats enable
    stats uri /monitor
    stats refresh 5s
```

### active health checks
```
backend apiservers
    balance roundrobin
    server server1 192.168.50.3:80 check
# => easiest way to check whether a server is up 
	inter: interval between checks (default miliseconds)
	downinter: interval between checks when service is already in the down state 
	fall: number of faild checks before making the server as down
	rise: number of successful checks before making the server as up again
	fastinter: interval betwwen checks when up 
#
server server1 192.168.50.3:80 check inter 5s downinter 5s fall 3 rise 3

#
backend apiservers
    balance roundrobin
    option httpchk GET /check
    server server1 192.168.50.3:80 check
#
option httpchk GET /check
server server1 192.168.50.3:443 check  port 80

#
option httpchk GET /check HTTP/1.1\r\nHost:\ mywebsite.com
server server1 192.168.50.3:80 check  port 80

#
backend apiservers
   balance roundrobin
   option httpchk GET /check
   http-check expect status 200
   server server1 192.168.50.3:80 check
#
http-check expect string success

####PASSIVE CHECK
backend apiservers
    balance roundrobin
    option redispatch
    server server1 192.168.50.3:80 check inter 2m downinter 2m observe layer4 error-limit 10 on-error mark-down

#
server server1 192.168.50.3:80 check inter 2m downinter 2m observe layer7 error-limit 10 on-error mark-down

#
backend apiservers
    balance roundrobin
    server server1 192.168.50.3:80 check weight 100 agent-check agent-inter 5s agent-addr 192.168.50.3 agent-port 9999

```






















