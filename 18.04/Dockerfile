FROM ghifari160/apache:18.04

LABEL maintainer "GHIFARI160 <ghifari160@ghifari160.com>"

# Disable interactive functions
ENV DEBIAN_FRONTEND noninteractive

RUN apt update && \

# Install Git and Make
    apt install -y git make && \

# Install build prerequisite
    apt install -y autoconf automake gcc g++ build-essential \
        libxml2-dev apache2-dev gettext libxml2 && \
    apt clean && rm -rf /var/lib/apt/lists/* && \

# Build and install Bison v2.7
    wget http://ftp.gnu.org/pub/gnu/bison/bison-2.7.tar.gz -O bison-2.7.tar.gz && \
    tar -xvzf bison-2.7.tar.gz && cd /bison-2.7 && ./configure \
        --prefix=/usr/local/bison \
        --with-libiconv-prefix=/usr/local/libiconv && \
    make && make install && export PATH=$PATH:/usr/local/bison/bin

# Download PHP v7.3.1 source code
RUN cd / && \
    git clone -b PHP-7.3.1 --single-branch https://github.com/php/php-src.git && \
    export PATH=$PATH:/usr/local/bison/bin && \

# Configure Apache2 mod-php
    wget https://gist.githubusercontent.com/Ghifari160/a5c8778c192c34363178a9dc065029f3/raw/php.conf -O /etc/apache2/mods-available/php.conf && \

# Build and install PHP v7.3.1 WITHOUT PEAR
    cd /php-src && ./buildconf --force && \
    ./configure --with-apxs2=/usr/bin/apxs2 \
        --with-mysqli --with-gettext --with-mbstring --with-libxml \
        --without-pear && \
    make && make install && \

# Disable PHP for configuration
    a2dismod php7 && \

# Configure PHP
    mv /etc/apache2/mods-available/php7.load /etc/apache2/mods-available/php7.3.load && \
    wget https://gist.githubusercontent.com/Ghifari160/760ab4aee8dd87d0736ec7b871e19fad/raw/php7.3.conf -O /etc/apache2/mods-available/php7.3.conf && \
    wget https://gist.githubusercontent.com/Ghifari160/760ab4aee8dd87d0736ec7b871e19fad/raw/php.ini -O /usr/local/lib/php.ini && \

# Disable mpm_event and enable mpm_prefork
    a2dismod mpm_event && a2enmod mpm_prefork && \

# Reenable PHP
    a2enmod php7.3

# Clean up build environment
RUN rm -rf /php-src/* /bison-2.7/* /bison-2.7.tar.gz

# Uninstall build prerequisites
RUN apt remove --autoremove -y autoconf automake gcc g++ build-essential \
     libxml2-dev apache2-dev && \

# Uninstall Git and Make
    apt remove --autoremove -y git make && \
    apt clean && rm -rf /var/lib/apt/lists/*

# Add the custom default page
RUN rm /var/www/html/index.html
ADD index.php /var/www/html/index.php
