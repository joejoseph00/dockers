FROM thecodingmachine/php:8.0-v4-cli-node14

# Installer l'agent SSH si pas déjà installé, il est requis par Docker.
# (change apt-get to yum if you use a CentOS-based image)
# Installer rsync si pas déjà installé.

RUN which ssh-agent || ( sudo apt-get update -y && sudo apt-get install openssh-client -y ) && \
    which rsync || ( sudo apt-get update -y && sudo apt-get install rsync -y );

RUN npm install -g npm@8.9.0 && \
    nvm install 14.17.0
