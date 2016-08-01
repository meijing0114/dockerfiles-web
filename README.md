# dockerfiles-web
common used web related dockerfiles 

## web-dev

This reposotory consists of the following parts:

* Dockerfile
* necessary tar files 
* necessary config files
* necessary php libaries

Using this Dockerfile you could build an envirioent with:

* Nginx 1.10.1 debug version
* php 5.5.30 debug version with php-fpm
* openssl enabled
* php with gd openssl mcrypt curl xml gmp redis swoole enabled
* nginx with http_sub_module and http_ssl_module

You could use web-dev to build a common web development enviroment or you could modify it to custimize one of you own.

