# Use a Debian base image
FROM debian:bookworm

# Set environment variables for the Nginx version and build dependencies
ENV NGINX_VERSION=1.25.3
ENV NGINX_DEPS="build-essential libpcre3-dev zlib1g-dev libssl-dev libxml2-dev libxslt1-dev"
ENV NGINX_CONFIG_OPTS="\
    --prefix=/usr/local/nginx \
    --user=www-data \
    --group=www-data \
    --with-http_ssl_module \
    --with-http_v2_module \
    --with-http_realip_module \
    --with-http_gzip_static_module \
    --with-http_stub_status_module \
    --with-stream \
    --with-stream_ssl_preread_module \
    --with-pcre \
    --with-http_dav_module \
    --add-module=/tmp/nginx-dav-ext-module \
    --conf-path=/etc/nginx/nginx.conf \
    --http-log-path=/var/log/nginx/access.log \
    --error-log-path=/var/log/nginx/error.log \
"

# Create a non-root user and group
#RUN groupadd -r www-data && useradd -r -g www-data www-data

# Install build dependencies and download Nginx source
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    wget \
    ${NGINX_DEPS} \
    \
    && wget --no-check-certificate https://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz -O /tmp/nginx.tar.gz \
    && tar -xzvf /tmp/nginx.tar.gz -C /tmp \
    && rm /tmp/nginx.tar.gz \
    \
    # Download nginx-dav-ext-module
    && wget --no-check-certificate https://github.com/arut/nginx-dav-ext-module/archive/master.tar.gz -O /tmp/nginx-dav-ext-module.tar.gz \
    && tar xf /tmp/nginx-dav-ext-module.tar.gz -C /tmp \
    && mv /tmp/nginx-dav-ext-module-master /tmp/nginx-dav-ext-module \
    && rm /tmp/nginx-dav-ext-module.tar.gz

# Compile and install Nginx
WORKDIR /tmp/nginx-${NGINX_VERSION}
RUN ./configure ${NGINX_CONFIG_OPTS} \
    && make \
    && make install \
    && rm -rf /tmp/nginx-${NGINX_VERSION} /tmp/nginx-dav-ext-module

# Create necessary directories
RUN mkdir -p /etc/nginx/conf.d \
    && mkdir -p /var/log/nginx \
    && touch /var/log/nginx/access.log \
    && touch /var/log/nginx/error.log \
    && chown -R www-data:www-data /var/log/nginx \
    && chmod -R 755 /var/log/nginx
# Clean up build dependencies and temporary files
RUN apt-get remove --purge -y ${NGINX_DEPS} \
    && apt-get autoremove -y \
    && apt-get clean \
    && apt-get update \
    && apt-get install -y --no-install-recommends libpcre3 libssl3 libxml2 \
    && rm -rf /var/lib/apt/lists/*

# Expose ports 80 and 443
EXPOSE 80 443

# Command to run Nginx
ENV PATH="/usr/local/nginx/sbin:$PATH"

CMD ["nginx", "-g", "daemon off;"]
