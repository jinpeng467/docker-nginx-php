# PHP镜像

#### 基于官方镜像
php:7.2-fpm-alpine3.8

- [主页](https://hub.docker.com/_/php)
- [镜像文件](https://github.com/docker-library/php/tree/c44aab7001ea12c8bec96664533df503bbf0af19/7.2/alpine3.8/fpm)

#### 修改
1. 更换阿里云源
2. 调整系统时区
3. 添加核心扩展
4. 添加pecl扩展
5. 调整部分配置
6. 设定工作目录

#### 参考：
- [drupal 8.5-fpm-alpine](https://github.com/docker-library/drupal/blob/b7220e37ef3f417b39a8b50de772013124105d83/8.5/fpm-alpine/Dockerfile)
- [Setting up PHP, PHP-FPM and NGINX for local development on Docker](https://www.pascallandau.com/blog/php-php-fpm-and-nginx-on-docker-in-windows-10/#connecting-nginx-php-fpm)
- [lnmp一键安装包](http://soft.vpser.net/lnmp/lnmp1.5.tar.gz)
- [Dockerfile 最佳实践](https://yeasy.gitbooks.io/docker_practice/appendix/best_practices.html)

