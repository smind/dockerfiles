# Dockerfile
FROM debian:stretch-slim

LABEL maintainer="Kostyantyn Didenko <kdidenko@secondmind.ai>"
LABEL ai.secondmind.module="RTMP Server"

LABEL version="1.0"
LABEL description="Contains Nginx Server build with RTMP module support"

# TODO: think about replacing the value with parameter placeholder
ENV NGINX_VERSION 1.14.0

# install build dependencies
RUN set -x \
        && apt-get update \
        && apt-get install -y wget unzip build-essential libpcre3 libpcre3-dev libssl-dev


# stemporary work dir
WORKDIR /usr/src/smind

# download the Nginx source code
RUN wget "http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz"
# and get the RTMP module source code from git
RUN wget "https://github.com/arut/nginx-rtmp-module/archive/master.zip"

# extract and go to sources
RUN set -x \
        tar -zxvf nginx-${NGINX_VERSION}.tar.gz \
        && unzip master.zip \
        && cd nginx-${NGINX_VERSION}

# build and install Nginx RTMP
RUN ./configure \
                --with-http_ssl_module \
                --add-module=../nginx-rtmp-module-master
RUN make && sudo make install


# forward request and error logs to docker log collector
RUN ln -sf /dev/stdout /var/log/nginx/access.log \
        && ln -sf /dev/stderr /var/log/nginx/error.log

# expose HTTP and RTMP ports
EXPOSE 80
EXPOSE 1935

STOPSIGNAL SIGTERM

# start the Nginx RTMP server
CMD ["sudo /usr/local/nginx/sbin/nginx"]
