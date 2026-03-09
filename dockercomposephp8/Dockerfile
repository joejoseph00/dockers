FROM php:8.0-alpine

#######################
#BEGIN DOCKER IMAGE https://github.com/docker-library/docker/blob/master/18.05/Dockerfile
#######################

RUN apk add --no-cache ca-certificates

RUN set -ex; \
# why we use "curl" instead of "wget":
# + wget -O docker.tgz https://download.docker.com/linux/static/stable/x86_64/docker-17.03.1-ce.tgz
# Connecting to download.docker.com (54.230.87.253:443)
# wget: error getting response: Connection reset by peer
	apk add --no-cache --virtual .fetch-deps \
		curl \
		tar;

#BEGIN ECHO OF modprobe.sh to /usr/local/bin/modprobe

RUN echo '#!/bin/sh' >> /usr/local/bin/modprobe; \
echo 'set -eu' >> /usr/local/bin/modprobe; \
echo '#' >> /usr/local/bin/modprobe; \
echo '# "modprobe" without modprobe' >> /usr/local/bin/modprobe; \
echo '# https://twitter.com/lucabruno/status/902934379835662336' >> /usr/local/bin/modprobe; \
echo '#' >> /usr/local/bin/modprobe; \
echo '# this is not 100% fool-proof, but it will have a much higher success rate than simply using the "real" modprobe' >> /usr/local/bin/modprobe; \
echo '#' >> /usr/local/bin/modprobe; \
echo '# Docker often uses "modprobe -va foo bar baz"' >> /usr/local/bin/modprobe; \
echo '# so we ignore modules that start with "-"' >> /usr/local/bin/modprobe; \
echo 'for module; do' >> /usr/local/bin/modprobe; \
echo '	if [ "${module#-}" = "$module" ]; then' >> /usr/local/bin/modprobe; \
echo '		ip link show "$module" || true' >> /usr/local/bin/modprobe; \
echo '		lsmod | grep "$module" || true' >> /usr/local/bin/modprobe; \
echo '	fi' >> /usr/local/bin/modprobe; \
echo 'done' >> /usr/local/bin/modprobe; \
echo '#' >> /usr/local/bin/modprobe; \
echo '# remove /usr/local/... from PATH so we can exec the real modprobe as a last resort' >> /usr/local/bin/modprobe; \
echo 'export PATH="/usr/sbin:/usr/bin:/sbin:/bin"' >> /usr/local/bin/modprobe; \
echo 'exec modprobe "$@"' >> /usr/local/bin/modprobe;
RUN chmod 755 /usr/local/bin/modprobe;
#BEGIN ECHO OF docker-entrypoint.sh

RUN echo '#!/bin/sh' >> /usr/local/bin/docker-entrypoint.sh; \
echo 'set -e' >> /usr/local/bin/docker-entrypoint.sh; \
echo '#' >> /usr/local/bin/docker-entrypoint.sh; \
echo '# first arg is `-f` or `--some-option`' >> /usr/local/bin/docker-entrypoint.sh; \
echo 'if [ "${1#-}" != "$1" ]; then' >> /usr/local/bin/docker-entrypoint.sh; \
echo '	set -- docker "$@"' >> /usr/local/bin/docker-entrypoint.sh; \
echo 'fi' >> /usr/local/bin/docker-entrypoint.sh; \
echo '#' >> /usr/local/bin/docker-entrypoint.sh; \
echo '# if our command is a valid Docker subcommand, let us invoke it through Docker instead' >> /usr/local/bin/docker-entrypoint.sh; \
echo '# (this allows for "docker run docker ps", etc)' >> /usr/local/bin/docker-entrypoint.sh; \
echo 'if docker help "$1" > /dev/null 2>&1; then' >> /usr/local/bin/docker-entrypoint.sh; \
echo '	set -- docker "$@"' >> /usr/local/bin/docker-entrypoint.sh; \
echo 'fi' >> /usr/local/bin/docker-entrypoint.sh; \
echo '#' >> /usr/local/bin/docker-entrypoint.sh; \
echo '# if we have "--link some-docker:docker" and not DOCKER_HOST, let us set DOCKER_HOST automatically' >> /usr/local/bin/docker-entrypoint.sh; \
echo 'if [ -z "$DOCKER_HOST" -a "$DOCKER_PORT_2375_TCP" ]; then' >> /usr/local/bin/docker-entrypoint.sh; \
echo '	export DOCKER_HOST="tcp://docker:2375"' >> /usr/local/bin/docker-entrypoint.sh; \
echo 'fi' >> /usr/local/bin/docker-entrypoint.sh; \
echo '#' >> /usr/local/bin/docker-entrypoint.sh; \
echo 'if [ "$1" = "dockerd" ]; then' >> /usr/local/bin/docker-entrypoint.sh; \
echo '	cat >&2 <<-"EOW"' >> /usr/local/bin/docker-entrypoint.sh; \
echo '		📎 Hey there!  It looks like you are trying to run a Docker daemon.' >> /usr/local/bin/docker-entrypoint.sh; \
echo '		   You probably should use the "dind" image variant instead, something like:' >> /usr/local/bin/docker-entrypoint.sh; \
echo '		     docker run --privileged --name some-overlay-docker -d docker:stable-dind --storage-driver=overlay' >> /usr/local/bin/docker-entrypoint.sh; \
echo '		   See https://hub.docker.com/_/docker/ for more documentation and usage examples.' >> /usr/local/bin/docker-entrypoint.sh; \
echo '	EOW' >> /usr/local/bin/docker-entrypoint.sh; \
echo '	sleep 3' >> /usr/local/bin/docker-entrypoint.sh; \
echo 'fi' >> /usr/local/bin/docker-entrypoint.sh; \
echo '#' >> /usr/local/bin/docker-entrypoint.sh; \
echo 'exec "$@"' >> /usr/local/bin/docker-entrypoint.sh
RUN chmod 755 /usr/local/bin/docker-entrypoint.sh;
#########################
#END OF FROM DOCKER IMAGE https://github.com/docker-library/docker/blob/master/18.05/Dockerfile
#########################

RUN apk update; \
    apk add --no-cache git wget bash openssh-client;

RUN apk add --no-cache \
    freetype-dev \
    libjpeg-turbo-dev \
    libpng-dev \
    zip \
    unzip \
    patch \
    iputils \
  && docker-php-ext-configure gd --with-freetype --with-jpeg \
  && docker-php-ext-install -j$(nproc) gd

#RUN useradd -ms /bin/bash docker-dev
#run as docker-dev
#USER docker-dev
WORKDIR /root

RUN echo "#!/bin/sh" >> /root/greet.sh
RUN echo "echo \"Welcome to the docker php container.\"" >> /root/greet.sh
RUN echo "echo \"Docker image for running behat (php).\"" >> /root/greet.sh

RUN chmod 755 /root/greet.sh
RUN echo ". /root/greet.sh" >> /root/.bashrc

ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["su", "-", "root", "-c", "/bin/bash"]

