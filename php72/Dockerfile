# 拉取 CentOS7
FROM centos:7.2.1511

# 维护者
MAINTAINER hano666 <hano666@gmail.com>

# 设置 PHP 和redis版本
ENV PHP_VERSION 7.2.30
ENV REDIS_VERSION 5.2.2
# 安装定时任务
RUN yum -y install crontabs
# 安装依赖
RUN set -x \
  && yum update -y \
  && yum install epel-release -y \
  && yum install -y wget git gcc gcc-c++ make automake autoconf perl file tar re2c libjpeg libpng libjpeg-devel libpng-devel libjpeg-turbo freetype freetype-devel \
        libcurl-devel libxml2-devel libjpeg-turbo-devel libXpm-devel libXpm libicu-devel libmcrypt libmcrypt-devel libxslt-devel libxslt openssl openssl-devel bzip2-devel \
  
  # 建立目录
  
  && mkdir ~/download \
  && cd ~/download \

  # 编译 PHP
  && wget https://www.php.net/distributions/php-$PHP_VERSION.tar.gz \
  && tar -zxf php-$PHP_VERSION.tar.gz \
  && cd php-$PHP_VERSION \
  && groupadd -r nginx \
  && useradd -r -g nginx nginx \
  && ./configure \
       --prefix=/usr/local/php \
       --with-config-file-path=/usr/local/php/etc/ \
       --with-config-file-scan-dir=/usr/local/php/conf.d/ \
       --enable-fpm \
       --enable-debug \
       --enable-cgi \
       --with-fpm-user=nginx  \
       --with-fpm-group=nginx \
       --enable-phpdbg \
       --enable-mbstring \
       --enable-calendar \
       --with-xsl \
       --with-openssl \
       --enable-soap \
       --enable-zip \
       --enable-shmop \
       --enable-sockets \
       --enable-bcmath \
       --with-gd \
       --with-freetype-dir=/usr/include/freetype2/freetype \
       --with-jpeg-dir \
       --with-png-dir \
       --with-xpm-dir \
       --with-xmlrpc \
       --enable-pcntl \
       --enable-intl \
       --enable-sysvsem \
       --enable-sysvshm \
       --enable-sysvmsg \
       --enable-opcache \
       --with-iconv \
       --with-bz2 \
       --with-curl \
       --enable-mysqlnd \
       --with-mysqli=mysqlnd \
       --with-pdo-mysql=mysqlnd \
       --with-zlib \
       --with-gettext \
  && make \
  && make install \

  # 复制配置文件
  && cp ~/download/php-$PHP_VERSION/php.ini-production /usr/local/php/etc/php.ini \
  && cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf \
  && cp /usr/local/php/etc/php-fpm.d/www.conf.default /usr/local/php/etc/php-fpm.d/www.conf \

  # 安装 Redis 扩展
  && cd ~/download \
  && wget https://github.com/phpredis/phpredis/archive/$REDIS_VERSION.tar.gz \
  && tar -zxvf $REDIS_VERSION.tar.gz \
  && cd phpredis-$REDIS_VERSION \
  && /usr/local/php/bin/phpize \
  && ./configure --with-php-config=/usr/local/php/bin/php-config \
  && make \
  && make install \

  # 安装 Swoole 扩展
#  && cd ~/download \
#  && wget https://github.com/swoole/swoole-src/archive/v4.3.2.tar.gz \
#  && tar -zxvf v4.3.2.tar.gz \
#  && cd swoole-src-4.3.2 \
#  && /usr/local/php/bin/phpize \
#  && ./configure --with-php-config=/usr/local/php/bin/php-config \
#  && make \
#  && make install \

  # 安装 Xdebug 扩展
  && cd ~/download \
  && wget http://pecl.php.net/get/xdebug-2.7.1.tgz \
  && tar -zxvf xdebug-2.7.1.tgz \
  && cd xdebug-2.7.1 \
  && /usr/local/php/bin/phpize \
  && ./configure --with-php-config=/usr/local/php/bin/php-config \
  && make \
  && make install \

  # 加入环境变量
  && echo "export PATH=/usr/local/php/bin:/usr/local/php/sbin:$PATH" >> ~/.bashrc \
  && source ~/.bashrc \

  # 安装 Composer
  && cd ~/download \
  && php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
  && php composer-setup.php --install-dir=/usr/local/sbin --filename=composer \
  && php -r "unlink('composer-setup.php');" \

  # 删除安装文件
  && rm -rf ~/download \
  && yum clean all

CMD ["/usr/local/php/sbin/php-fpm"]

EXPOSE 9000