FROM nginx:1.16.1-alpine

ENV NGINX_HTTP_FLV_MODULE_VERSION 1.2.6
ENV NGINX_NGX_DEVEL_MODULE_VERSION 0.3.1
ENV NGINX_LUA_MODULE_VERSION 0.10.15

ENV LUAJIT_LIB=/usr/lib
ENV LUAJIT_INC=/usr/include/luajit-2.1

RUN CONFIG="\
    --prefix=/etc/nginx \
    --sbin-path=/usr/sbin/nginx \
    --modules-path=/usr/lib/nginx/modules \
    --conf-path=/etc/nginx/nginx.conf \
    --error-log-path=/var/log/nginx/error.log \
    --http-log-path=/var/log/nginx/access.log \
    --pid-path=/var/run/nginx.pid \
    --lock-path=/var/run/nginx.lock \
    --user=nginx \
    --group=nginx \
    --with-compat \
    " \
    && apk add --no-cache --virtual .build-deps gcc libc-dev make zlib-dev openssl-dev pcre-dev curl luajit-dev \
    && curl -fSL https://nginx.org/download/nginx-$NGINX_VERSION.tar.gz -o nginx.tar.gz \
    && curl -fSL https://github.com/winshining/nginx-http-flv-module/archive/v$NGINX_HTTP_FLV_MODULE_VERSION.zip -o nginx-http-flv-module.zip \
    && curl -fSL https://github.com/simplresty/ngx_devel_kit/archive/v$NGINX_NGX_DEVEL_MODULE_VERSION.zip -o ngx_devel_kit.zip \
    && curl -fSL https://github.com/openresty/lua-nginx-module/archive/v$NGINX_LUA_MODULE_VERSION.zip -o lua-nginx-module.zip \
    && mkdir -p /usr/src /var/www /etc/nginx/conf.d/http /etc/nginx/conf.d/rtmp /tmp/hls /tmp/dash \
    && tar -zxC /usr/src -f nginx.tar.gz \
    && unzip nginx-http-flv-module.zip -q -d /usr/src \
    && unzip lua-nginx-module.zip -q -d /usr/src \
    && unzip ngx_devel_kit.zip -q -d /usr/src \
    && rm nginx.tar.gz nginx-http-flv-module.zip lua-nginx-module.zip ngx_devel_kit.zip \
    && cd /usr/src/nginx-$NGINX_VERSION \
    && ./configure $CONFIG --add-dynamic-module=/usr/src/nginx-http-flv-module-$NGINX_HTTP_FLV_MODULE_VERSION --add-dynamic-module=/usr/src/lua-nginx-module-$NGINX_LUA_MODULE_VERSION --add-dynamic-module=/usr/src/ngx_devel_kit-$NGINX_NGX_DEVEL_MODULE_VERSION \
    && make modules \
    && cp /usr/src/nginx-http-flv-module-$NGINX_HTTP_FLV_MODULE_VERSION/stat.xsl /var/www \
    && cp objs/ngx_http_flv_live_module.so /usr/lib/nginx/modules \
    && cp objs/ngx_http_lua_module.so /usr/lib/nginx/modules \
    && cp objs/ndk_http_module.so /usr/lib/nginx/modules \
    && strip /usr/lib/nginx/modules/ngx_http_flv_live_module.so \
    && strip /usr/lib/nginx/modules/ngx_http_lua_module.so \
    && strip /usr/lib/nginx/modules/ndk_http_module.so \
    && rm -rf /usr/src/nginx-$NGINX_VERSION /usr/src/nginx-http-flv-module-$NGINX_HTTP_FLV_MODULE_VERSION /usr/src/lua-nginx-module-$NGINX_LUA_MODULE_VERSION /usr/src/ngx_devel_kit-$NGINX_NGX_DEVEL_MODULE_VERSION \
    && apk del .build-deps \
    && apk add luajit-dev

COPY nginx.conf /etc/nginx/nginx.conf
COPY conf.d/ /etc/nginx/conf.d/
COPY www/ /var/www/

EXPOSE 80 1935
