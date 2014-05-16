FROM deis/base
MAINTAINER Gabriel Monroy <gabriel@opdemand.com>

RUN apt-get update
RUN apt-get install -yq patch libpcre3 libpcre3-dev libssl-dev

RUN wget -q http://nginx.org/download/nginx-1.6.0.tar.gz -O /tmp/nginx-1.6.0.tar.gz
RUN wget -q https://github.com/yaoweibin/nginx_tcp_proxy_module/archive/v0.4.5.tar.gz -O /tmp/tcp-proxy.tar.gz

WORKDIR /tmp

RUN tar -xzf nginx-1.6.0.tar.gz
RUN tar -xzf tcp-proxy.tar.gz

WORKDIR /tmp/nginx-1.6.0
RUN patch -p1 < /tmp/nginx_tcp_proxy_module-0.4.5/tcp.patch
RUN ./configure --prefix=/usr/share/nginx --sbin-path=/usr/sbin/nginx --conf-path=/etc/nginx/nginx.conf --http-log-path==/var/log/nginx/access.log --error-log-path=/var/log/nginx/error.log --user=www-data --group=www-data --lock-path=/var/lock/nginx.lock --pid-path=/run/nginx.pid --with-http_ssl_module --with-pcre-jit --add-module=/tmp/nginx_tcp_proxy_module-0.4.5
RUN make
RUN make install

# install latest etcdctl including no-sync options
RUN wget -q https://s3-us-west-2.amazonaws.com/deis/etcdctl.no-sync -O /usr/local/bin/etcdctl
RUN chmod +x /usr/local/bin/etcdctl

ADD . /app
WORKDIR /app
EXPOSE 80 2222 5000
CMD ["/app/bin/boot"]
