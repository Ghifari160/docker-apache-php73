FROM ghifari160/apache:16.04

LABEL maintainer "GHIFARI160 <ghifari160@ghifari160.com>"

# Disable interactive functions
ENV DEBIAN_FRONTEND noninteractive

# Install Git, Make, and general build tools
RUN apt update && \
    apt install -y git make autoconf automake gcc g++ build-essential && \
    apt clean && rm -rf /var/lib/apt/lists/*

# Build and install Bison v2.7
RUN wget http://ftp.gnu.org/pub/gnu/bison/bison-2.7.tar.gz -O bison-2.7.tar.gz && \
    tar -xvzf bison-2.7.tar.gz && \
    cd /bison-2.7 && \
    ./configure --prefix=/usr/local/bison \
                --with-libiconv-prefix=/usr/local/libiconv && \
    make && make install && \
    make clean && cd / && rm -rf /bison-2.7

# Install PHP build prerequisites
RUN apt update && \
    apt install -y apache2-dev libxml2-dev gettext \
                   libreadline-dev zlib1g-dev libssl-dev && \
    apt clean && rm -rf /var/lib/apt/lists/*

# Build and Install PHP v7.3.1 WITHOUT PEAR
RUN git clone -b PHP-7.3.1 --single-branch https://github.com/php/php-src.git && \
    export PATH=$PATH:/usr/local/bison/bin && \
    cd /php-src && \
    ./buildconf --force && \
    ./configure --with-apxs2=/usr/bin/apxs2 --without-pear \
                --with-libdir=/lib/x86_64-linux-gnu \
                --enable-calendar \
                --enable-exif \
                --enable-ftp \
                --with-gettext \
                --enable-mbstring \
                --with-mhash \
                --with-mysqli \
                --with-openssl \
                --with-pdo-mysql \
                --with-readline \
                --enable-shmop \
                --enable-sockets \
                --enable-sysvmsg \
                --enable-wddx \
                --enable-opcache \
                --with-zlib \
                --enable-sysvsem \
                --enable-sysvshm && \
    make && make install && \
    make clean && cd / && rm -rf /php-src

# Remove PHP build prerequisites
RUN apt update && \
    apt remove --autoremove -y apache2-dev libxml2-dev gettext libreadline-dev \
               zlib1g-dev libssl-dev && \
    apt clean && rm -rf /var/lib/apt/lists/*

# Remove Git, Make, and general build tools
RUN apt update && \
    apt remove --autoremove -y git make autoconf automake gcc g++ build-essential && \
    apt clean && rm -rf /var/lib/apt/lists/*

# Configure PHP and Apache2
RUN a2dismod php7 && \
    mv /etc/apache2/mods-available/php7.load /etc/apache2/mods-available/php7.3.load && \
    wget https://gist.githubusercontent.com/Ghifari160/760ab4aee8dd87d0736ec7b871e19fad/raw/php7.3.conf -O /etc/apache2/mods-available/php7.3.conf && \
    wget https://gist.githubusercontent.com/Ghifari160/760ab4aee8dd87d0736ec7b871e19fad/raw/php.ini -O /usr/local/lib/php.ini && \
    a2dismod mpm_event && a2enmod mpm_prefork && \
    a2enmod php7.3

# Add the custom default page
RUN rm /var/www/html/index.html
ADD index.php /var/www/html/index.php
