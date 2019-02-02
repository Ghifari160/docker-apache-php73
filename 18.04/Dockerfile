FROM ghifari160/apache:18.04

LABEL maintainer "GHIFARI160 <ghifari160@ghifari160.com>"

# Disable interactive functions
ENV DEBIAN_FRONTEND noninteractive

# Switch locale to UTF-8 and add ppa for php73
RUN export LC_ALL=en_US.UTF-8 && add-apt-repository -y ppa:ondrej/php

# Add the custom default page
RUN rm /var/www/html/index.html
ADD index.php /var/www/html/index.php

# Install php73
RUN apt update && apt install -y php7.3 php7.3-mysql php-gettext \
        php7.3-mbstring php-xdebug libapache2-mod-php7.3 php7.3-xml && \
    apt clean && rm -rf /var/lib/apt/lists/*
