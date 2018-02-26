FROM php:7.0-fpm

# adding a docker-dev user to run composer

RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        zip \
        unzip \
        patch \
        iputils-ping \
        telnetd \
    && docker-php-ext-install -j$(nproc) iconv mcrypt \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd

RUN apt-get install bash git -y
RUN useradd -ms /bin/bash docker-dev
#run as docker-dev
USER docker-dev
WORKDIR /home/docker-dev
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php \
    && php -r "unlink('composer-setup.php');"
 
#RUN php composer.phar install
RUN echo "#!/bin/sh" >> /home/docker-dev/greet.sh
RUN echo "echo \"Welcome to the https://hub.docker.com/r/joejoseph00/phpcomposer/ docker container, run composer as follows:\"" >> /home/docker-dev/greet.sh
RUN echo "echo \"php composer.phar install\"" >> /home/docker-dev/greet.sh
RUN chmod 755 /home/docker-dev/greet.sh
RUN echo ". /home/docker-dev/greet.sh" >> /home/docker-dev/.bashrc
 # \
#    && 'which ssh-agent || ( apt-get update -y && apt-get install openssh-client -y )' \
#    && mkdir -p ~/.ssh \
CMD ["su", "-", "docker-dev", "-c", "/bin/bash"]
