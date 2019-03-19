# base image https://hub.docker.com/_/nginx
FROM nginx:1.14-alpine

# change repositories
RUN set -ex \
	&& cp /etc/apk/repositories /etc/apk/repositories.default \
	&& { \
		echo 'https://mirrors.aliyun.com/alpine/v3.8/main'; \
		echo 'https://mirrors.aliyun.com/alpine/v3.8/community'; \
	} > /etc/apk/repositories

# set timezone
ENV TZ Asia/Shanghai

# set workdir
WORKDIR /etc/nginx
# copy config
ARG CONF_PATH=/etc/nginx

COPY nginx.conf enable-php.conf ${CONF_PATH}/
COPY vhosts/ ${CONF_PATH}/conf.d/