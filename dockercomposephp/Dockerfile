FROM php:7.0-alpine

#######################
#BEGIN DOCKER IMAGE https://github.com/docker-library/docker/blob/master/18.05/Dockerfile
#######################

RUN apk add --no-cache \
		ca-certificates

# set up nsswitch.conf for Go's "netgo" implementation (which Docker explicitly uses)
# - https://github.com/docker/docker-ce/blob/v17.09.0-ce/components/engine/hack/make.sh#L149
# - https://github.com/golang/go/blob/go1.9.1/src/net/conf.go#L194-L275
# - docker run --rm debian:stretch grep '^hosts:' /etc/nsswitch.conf
RUN [ ! -e /etc/nsswitch.conf ] && echo 'hosts: files dns' > /etc/nsswitch.conf

ENV DOCKER_CHANNEL edge
ARG docker_version=18.05.0-ce
ENV DOCKER_VERSION ${docker_version}
# TODO ENV DOCKER_SHA256
# https://github.com/docker/docker-ce/blob/5b073ee2cf564edee5adca05eee574142f7627bb/components/packaging/static/hash_files !!
# (no SHA file artifacts on download.docker.com yet as of 2017-06-07 though)

RUN set -ex; \
# why we use "curl" instead of "wget":
# + wget -O docker.tgz https://download.docker.com/linux/static/stable/x86_64/docker-17.03.1-ce.tgz
# Connecting to download.docker.com (54.230.87.253:443)
# wget: error getting response: Connection reset by peer
	apk add --no-cache --virtual .fetch-deps \
		curl \
		tar \
	; \
	\
# this "case" statement is generated via "update.sh"
	apkArch="$(apk --print-arch)"; \
	case "$apkArch" in \
		x86_64) dockerArch='x86_64' ;; \
		armhf) dockerArch='armel' ;; \
		aarch64) dockerArch='aarch64' ;; \
		ppc64le) dockerArch='ppc64le' ;; \
		s390x) dockerArch='s390x' ;; \
		*) echo >&2 "error: unsupported architecture ($apkArch)"; exit 1 ;;\
	esac; \
	\
	if ! curl -fL -o docker.tgz "https://download.docker.com/linux/static/${DOCKER_CHANNEL}/${dockerArch}/docker-${DOCKER_VERSION}.tgz"; then \
		echo >&2 "error: failed to download 'docker-${DOCKER_VERSION}' from '${DOCKER_CHANNEL}' for '${dockerArch}'"; \
		exit 1; \
	fi; \
	\
	tar --extract \
		--file docker.tgz \
		--strip-components 1 \
		--directory /usr/local/bin/ \
	; \
	rm docker.tgz; \
	\
	apk del .fetch-deps; \
	\
	dockerd -v; \
	docker -v

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

#########################
#START OF DOCKER-COMPOSE
#########################

ARG compose_version=1.21.2
# Install docker-compose.
# The no-cache flag prevents apk from caching the index locally, reducing the image size.
# Using single RUN statement and cleaning up unused files after installation, to reduce image size.
# Note: adding wget even though it comes with the base image (alpine), because it's a stripped-down version missing many features.
# Note: also adding git, wget, bash, and openssh
RUN apk update; \
    apk add --no-cache git wget bash openssh-client; \
    wget -O /usr/local/bin/docker-compose https://github.com/docker/compose/releases/download/${compose_version}/docker-compose-$(uname -s)-$(uname -m); \
    chmod +x /usr/local/bin/docker-compose; \
    #Alpine doesn't come with glibc, and docker-compose needs it to run.
    GLIBC_VERSION='2.27-r0'; \
    wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/${GLIBC_VERSION}/glibc-${GLIBC_VERSION}.apk; \
    apk add --no-cache --allow-untrusted glibc-${GLIBC_VERSION}.apk; \
    rm glibc-${GLIBC_VERSION}.apk; \
    wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/${GLIBC_VERSION}/glibc-bin-${GLIBC_VERSION}.apk; \
    apk add --no-cache --allow-untrusted glibc-bin-${GLIBC_VERSION}.apk; \
    rm glibc-bin-${GLIBC_VERSION}.apk; \
    docker-compose --version

#########################
#END OF DOCKER-COMPOSE
#########################

RUN apk add --no-cache \
        freetype-dev \
        libjpeg-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        zip \
        unzip \
        patch \
        iputils \
#        telnetd \ # if we need it we can install through bash interface
    && docker-php-ext-install -j$(nproc) iconv mcrypt \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd

#RUN useradd -ms /bin/bash docker-dev
#run as docker-dev
#USER docker-dev
WORKDIR /home/root

RUN echo "#!/bin/sh" >> /home/root/greet.sh
RUN echo "echo \"Welcome to the docker php docker compose container.\"" >> /home/root/greet.sh
RUN echo "echo \"Experimental docker image for running behat (php) on sri, add description here.\"" >> /home/root/greet.sh

RUN chmod 755 /home/root/greet.sh
RUN echo ". /home/root/greet.sh" >> /home/root/.bashrc

ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["su", "-", "root", "-c", "/bin/bash"]

