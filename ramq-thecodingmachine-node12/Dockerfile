FROM thecodingmachine/php:7.4-v4-cli-node12

# Installer l'agent SSH si pas déjà installé, il est requis par Docker.
# (change apt-get to yum if you use a CentOS-based image)
# Installer rsync si pas déjà installé.

RUN which ssh-agent || ( sudo apt-get update -y && sudo apt-get install openssh-client -y ) && \
    which rsync || ( sudo apt-get update -y && sudo apt-get install rsync -y );

