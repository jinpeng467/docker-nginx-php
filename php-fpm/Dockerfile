# base image https://hub.docker.com/_/php
FROM php:7.2-fpm-alpine3.8

# change repositories
RUN set -ex \
	&& cp /etc/apk/repositories /etc/apk/repositories.default \
	&& { \
		echo 'https://mirrors.aliyun.com/alpine/v3.8/main'; \
		echo 'https://mirrors.aliyun.com/alpine/v3.8/community'; \
	} > /etc/apk/repositories

# Bring in tzdata so users could set the timezones through the environment variables
RUN apk add --no-cache tzdata

# set timezone
ENV TZ Asia/Shanghai

# add php core extensions
RUN set -ex \
	&& apk add --no-cache --virtual .build-deps \
		libjpeg-turbo-dev \
		libpng-dev \
		freetype-dev \
	&& docker-php-ext-configure gd \
		--with-jpeg-dir=/usr/include/ \
		--with-png-dir=/usr/include/ \
		--with-freetype-dir=/usr/include/ \
	&& docker-php-ext-install -j "$(nproc)" \
		gd \
		opcache \
		pdo_mysql \
		mysqli \
		zip \
	&& runDeps="$( \
		scanelf --needed --nobanner --format '%n#p' --recursive /usr/local/lib/php/extensions \
			| tr ',' '\n' \
			| sort -u \
			| awk 'system("[ -e /usr/local/lib/" $1 " ]") == 0 { next } { print "so:" $1 }' \
	)" \
	&& apk add --virtual .phpexts-rundeps $runDeps \
	&& apk del .build-deps

# php ini dir(base image already set env)
ARG PHP_INI_DIR=/usr/local/etc/php
RUN cp ${PHP_INI_DIR}/php.ini-development ${PHP_INI_DIR}/php.ini

# add pecl extensions
RUN set -ex \
	&& apk add --no-cache yaml \
	&& apk add --no-cache --virtual .build-deps \
		$PHPIZE_DEPS \
		yaml-dev \
		libressl-dev \
	&& pecl install http://pecl.php.net/get/yaml-2.0.4.tgz \
	&& pecl install http://pecl.php.net/get/mongodb-1.5.3.tgz \
	&& pecl install http://pecl.php.net/get/redis-4.1.1.tgz \
	&& chmod +x -c $(php -r 'echo ini_get("extension_dir");')/*.so \
	&& docker-php-ext-enable yaml mongodb redis \
	&& apk del .build-deps

# fpm conf dir
ENV FPM_CONF_DIR /usr/local/etc/php-fpm.d

# set workdir
WORKDIR /home/jinpeng

# set php ini
RUN sed -i 's/;date.timezone =/date.timezone = PRC/g' ${PHP_INI_DIR}/php.ini
RUN sed -i 's/upload_max_filesize = 2M/upload_max_filesize = 10M/g' ${PHP_INI_DIR}/php.ini
RUN sed -i 's/post_max_size = 8M/post_max_size = 16M/g' ${PHP_INI_DIR}/php.ini
RUN sed -i 's/max_execution_time = 30/max_execution_time = 300/g' ${PHP_INI_DIR}/php.ini
RUN sed -i 's/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/g' ${PHP_INI_DIR}/php.ini
RUN sed -i 's/disable_functions =/disable_functions = passthru,exec,system,chroot,chgrp,chown,shell_exec,proc_open,proc_get_status,popen,ini_alter,ini_restore,dl,openlog,syslog,readlink,symlink,popepassthru,stream_socket_server/g' ${PHP_INI_DIR}/php.ini

# modify fpm config
RUN sed -i 's/pm.max_children = 5/pm.max_children = 80/g' ${FPM_CONF_DIR}/www.conf
RUN sed -i 's/pm.start_servers = 2/pm.start_servers = 10/g' ${FPM_CONF_DIR}/www.conf
RUN sed -i 's/pm.min_spare_servers = 1/pm.min_spare_servers = 5/g' ${FPM_CONF_DIR}/www.conf
RUN sed -i 's/pm.max_spare_servers = 3/pm.max_spare_servers = 40/g' ${FPM_CONF_DIR}/www.conf
