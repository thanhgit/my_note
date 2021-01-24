# BUILD NGINX FROM SOURCE

## If nginx masked
```
sudo systemctl unmask nginx.service
```

## Check version ubuntu
```
lsb_release -ds 
```

## Set datetime
```
timedatectl set-timezone 'Asia/Ho_Chi_Minh'
```

## Update system    
```
sudo apt-get update && sudo apt upgrade -y
sudo apt-get install -y build-essential git tree
```

## Download source
```
wget https://nginx.org/download/nginx-1.15.0.tar.gz && tar zxvf nginx-1.15.0.tar.gz
wget https://ftp.pcre.org/pub/pcre/pcre-8.42.tar.gz && tar xzvf pcre-8.42.tar.gz
wget https://www.zlib.net/zlib-1.2.11.tar.gz && tar xzvf zlib-1.2.11.tar.gz
wget https://www.openssl.org/source/openssl-1.1.0h.tar.gz && tar xzvf openssl-1.1.0h.tar.gz
```

## Install optional nginx dependencies
```
sudo add-apt-repository -y ppa:maxmind/ppa
sudo apt update && sudo apt upgrade -y 
sudo apt install -y perl libperl-dev libgd3 libgd-dev libgeoip1 libgeoip-dev geoip-bin libxml2 libxml2-dev libxslt1.1 libxslt1-dev
```

## Create man nginx
```
sudo cp ~/nginx-1.15.0/man/nginx.8 /usr/share/man/man8
sudo gzip /usr/share/man/man8/nginx.8
ls /usr/share/man/man8/ | grep nginx.8.gz
```

## Check that Man page for NGINX is working:
```
man nginx
```

## create symlink
```
sudo ln -s /usr/lib/nginx/modules /etc/nginx/modules
```

## Check nginx version
```
nginx -V
```

## create nginx system group and user
```
sudo adduser --system --home /nonexistent --shell /bin/false --no-create-home --disabled-login --disabled-password --gecos "nginx user" --group nginx
```

## check nginx
```
nginx -t
```

## create cache directories for nginx
```
sudo mkdir -p /var/cache/nginx/client_temp /var/cache/nginx/fastcgi_temp /var/cache/nginx/proxy_temp /var/cache/nginx/scgi_temp /var/cache/nginx/uwsgi_temp
```

## Set chown and chmod
```
sudo chmod 700 /var/cache/nginx/*
sudo chown nginx:root /var/cache/nginx/*
```

## Check again
```
nginx -t
```

## Create nginx systemd unit file
```
sudo vi /etc/systemd/system/nginx.service
```
```
################
[Unit]
Description=nginx - high performance web server
Documentation=https://nginx.org/en/docs/
After=network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target

[Service]
Type=forking
PIDFile=/var/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t -c /etc/nginx/nginx.conf
ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID

[Install]
WantedBy=multi-user.target
################
```

## Enable nginx
```
sudo systemctl enable nginx.service
sudo systemctl start nginx.service
```

## Install optional nginx dependencies
```
sudo add-apt-repository -y ppa:maxmind/ppa
sudo apt update && sudo apt upgrade -y 
sudo apt install -y perl libperl-dev libgd3 libgd-dev libgeoip1 libgeoip-dev geoip-bin libxml2 libxml2-dev libxslt1.1 libxslt1-dev	
```

## Compile nginx
```
./configure --prefix=/etc/nginx \ 
            --sbin-path=/usr/sbin/nginx \ 
            --modules-path=/usr/lib/nginx/modules \ 
            --conf-path=/etc/nginx/nginx.conf \
            --error-log-path=/var/log/nginx/error.log \
            --pid-path=/var/run/nginx.pid \
            --lock-path=/var/run/nginx.lock \
            --user=nginx \
            --group=nginx \
            --build=Ubuntu \
            --builddir=nginx-1.15.0 \
            --with-select_module \
            --with-poll_module \
            --with-threads \
            --with-file-aio \
            --with-http_ssl_module \
            --with-http_v2_module \
            --with-http_realip_module \
            --with-http_addition_module \
            --with-http_xslt_module=dynamic \
            --with-http_image_filter_module=dynamic \
            --with-http_geoip_module=dynamic \
            --with-http_sub_module \
            --with-http_dav_module \
            --with-http_flv_module \
            --with-http_mp4_module \
            --with-http_gunzip_module \
            --with-http_gzip_static_module \
            --with-http_auth_request_module \
            --with-http_random_index_module \
            --with-http_secure_link_module \
            --with-http_degradation_module \
            --with-http_slice_module \
            --with-http_stub_status_module \
            --with-http_perl_module=dynamic \
            --with-perl_modules_path=/usr/share/perl/5.26.1 \
            --with-perl=/usr/bin/perl \
            --http-log-path=/var/log/nginx/access.log \
            --http-client-body-temp-path=/var/cache/nginx/client_temp \
            --http-proxy-temp-path=/var/cache/nginx/proxy_temp \
            --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
            --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
            --http-scgi-temp-path=/var/cache/nginx/scgi_temp \
            --with-mail=dynamic \
            --with-mail_ssl_module \
            --with-stream=dynamic \
            --with-stream_ssl_module \
            --with-stream_realip_module \
            --with-stream_geoip_module=dynamic \
            --with-stream_ssl_preread_module \
            --with-compat \
            --with-pcre=../pcre-8.42 \
            --with-pcre-jit \
            --with-zlib=../zlib-1.2.11 \
            --with-openssl=../openssl-1.1.0h \
            --with-openssl-opt=no-nextprotoneg \
            --with-debug

```

- Basic configuration format:
```
<section> {
	<directive> <parameters>;
}
```

- global configuration directives 
	- user: user/ group under which the worker process run is configured
	- worker_processes: number of processes will be started, depends on the server environment, the disk subsystem, and the network infrastructure. Best practise is set equal numbers of processor cores of CPU-bounds loads
	- error_log: is where all errors is written
	- pid: this is file where main process is written
	- use: indicates which connection processing method should be used 
	- worker_connections: maximum number of connections that a worker process may have open

#### We want nginx to run as user 'www'
```
user www;
```
#### The load is CPU-bound and we have 12 cores
```
worker_processes 12;
```
#### Explicitly specifying the path to the mandatory error log
```
error_log /var/log/nginx/error.log;
```
#### Also explicitly specifying the path to the pid file
```
pid /var/run/nginx.pid;
```
#### Sets up a new configuration context for the 'events' module
```
events {
```
#### We're on a Solaris-based system and have determined that nginx will stop responding to new requests over time with the default
#### Connection-processing mechanism, so we switch to the secondbest
```
use /dev/poll;
```
#### The product of this number and the number of worker_processes indicates how many simultaneous connections per IP:port pair are accepted
```
	worker_connections 2048;
}
```

## BEST PRACTISE

### Count IP address in nginx access log
```
sudo awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -nr
sudo cat /var/log/nginx/access.log | grep 190.104.220.x
```