# 以daocloud提供的centos6为基准，daocloud是国内一家提供镜像托管等服务的公司
FROM daocloud.io/library/centos:6
# 声明维护者
MAINTAINER meijing0114 <525937005@qq.com>
# 设置两个环境变量
ENV src-dir /data/sources
ENV run-dir /data/env/runtime
# 安装必须的软件包
RUN yum install -y \
git \
svn \
vim \
wget \
gcc \
gcc-c++ \
m4 \
pcre \
pcre-devel \
openssl \
openssl-devel \
zlib \
libcurl \
libcurl-devel \
epel-release \
libxml2 \
libxml2-devel \
gmp \
gmp-devel \
&& yum clean all 

# 创建必须的文件夹：
RUN mkdir /data \
&& mkdir /data/env \
&& mkdir /data/env/runtime \
&& mkdir /data/sources \
&& mkdir /data/logs \
&& mkdir /data/logs/nginx \
&& mkdir /data/logs/php5.5 \
&& mkdir /data/logs/service \
&& mkdir /data/website \
&& mkdir /data/admin \
&& mkdir /data/cache \
&& mkdir /data/cache/nginx \
&& chmod 666 /data/logs

##############################
# 拷贝libmcrypt的包并编译
ADD libmcrypt-2.5.8.tar.gz $src-dir
WORKDIR $src-dir/libmcrypt-2.5.8
RUN ./configure --prefix=/data/env/runtime/libmcrypt-2.5.8 \
&& make  \
&& make install \
&& make clean

# php本身
ADD ./php-5.5.30.tar.gz $src-dir
WORKDIR $src-dir/php-5.5.30
RUN ./configure --prefix=/data/env/runtime/php-5.5.30 --with-config-file-path=/data/env/runtime/php-5.5.30/etc --enable-opcache --enable-fpm --with-mysql=mysqlnd --with-pdo-mysql=mysqlnd --with-mysqli=mysqlnd --enable-xml --enable-pcntl --with-gettext=shared --enable-sysvmsg --enable-sysvshm --enable-sysvsem --enable-sockets --enable-mbstring --enable-soap --enable-bcmath --enable-zip --with-libxml --with-curl --with-gmp --with-mcrypt=/data/env/runtime/libmcrypt-2.5.8 --with-openssl --with-zlib \
&& make \
&& make install \
&& make clean 


# 更改php配置文件（拷贝一个过来）
COPY ./etc/php.ini /data/env/runtime/php-5.5.30/etc/

# php-fpm配置文件拷贝
COPY ./etc/php-fpm.conf /data/env/runtime/php-5.5.30/etc/

# 拷贝php扩展
COPY ./ext/swoole.so /data/env/runtime/php-5.5.30/lib/php/extensions/no-debug-non-zts-20121212/
COPY ./ext/gd.so /data/env/runtime/php-5.5.30/lib/php/extensions/no-debug-non-zts-20121212/
COPY ./ext/redis.so /data/env/runtime/php-5.5.30/lib/php/extensions/no-debug-non-zts-20121212/
#############################################
# 下载（拷贝）nginx的依赖包并编译安装

# nginx 本身的编译，增加了debug
ADD ./nginx-1.10.1.tar.gz $src-dir 
WORKDIR $src-dir/nginx-1.10.1
RUN ./configure --prefix=/data/env/runtime/nginx-1.10.1 --with-http_sub_module --with-http_realip_module --with-debug --with-http_ssl_module \
&& make \
&& make install \
&& make clean 

RUN rm -r /data/env/runtime/nginx-1.10.1/conf
RUN mkdir /data/env/runtime/nginx-1.10.1/conf
COPY ./etc/conf /data/env/runtime/nginx-1.10.1/conf/

RUN rm -r /data/sources

RUN echo -e "export PATH=/data/env/runtime/php-5.5.30/bin/:$PATH\n" >> /etc/profile \
"export PATH=/data/env/runtime/nginx/sbin:/data/website/qidian.com/framework/tsf/tsf2.0/bin:$PATH\n" >> /etc/profile \
"export QD_TSF_ENV=dev\n" >> /etc/profile  
RUN ln -s /data/env/runtime/nginx-1.10.1 /usr/local/nginx 
RUN ln -s /data/env/runtime/php-5.5.30 /usr/local/php


# 暴露80 443端口访问
EXPOSE 80 \
443

WORKDIR /data/admin

COPY ./admin/start.sh /data/admin/
RUN chmod 755 /data/admin/start.sh
CMD ["./start.sh"]

