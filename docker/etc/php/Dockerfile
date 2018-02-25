FROM php:7.0.26-fpm
MAINTAINER Edwin Siebel <edwin@yard.nl>

RUN apt-get update && \
    apt-get install -y --no-install-recommends nano git zip wget curl apt-utils libssl-dev openssh-server zip unzip sudo

# Curl
RUN apt-get install -y libcurl4-openssl-dev

# GD
RUN apt-get install -y libpng-dev libjpeg-dev

# Intl
RUN apt-get install -y libicu-dev

# Composer
RUN curl -o /tmp/composer-setup.php https://getcomposer.org/installer \
&& curl -o /tmp/composer-setup.sig https://composer.github.io/installer.sig \
# Make sure we're installing what we think we're installing!
&& php -r "if (hash('SHA384', file_get_contents('/tmp/composer-setup.php')) !== trim(file_get_contents('/tmp/composer-setup.sig'))) { unlink('/tmp/composer-setup.php'); echo 'Invalid installer' . PHP_EOL; exit(1); }" \
&& php /tmp/composer-setup.php --no-ansi --install-dir=/usr/local/bin --filename=composer --snapshot \
&& rm -f /tmp/composer-setup.*

# ZSH
RUN ["apt-get", "install", "-y", "zsh"]

# Change www-data user to match the host system UID and GID and chown www directory
RUN usermod --non-unique --uid 1000 www-data \
  && groupmod --non-unique --gid 1000 www-data \
  && chown -R www-data:www-data /var/www

RUN curl -LO https://deployer.org/deployer.phar
RUN mv deployer.phar /usr/local/bin/dep
RUN chmod +x /usr/local/bin/dep

USER www-data

WORKDIR /var/www

RUN wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh || true

RUN mkdir -p /var/www/.ssh

#COPY  ~/.ssh/id_rsa /var/www/.ssh/id_rsa                                             
RUN chmod 700 /var/www/.ssh

RUN echo "Host github.com\n\tStrictHostKeyChecking no\n" >> /var/www/.ssh/config
RUN echo "    IdentityFile ~/.ssh/id_rsa" >> /var/www/.ssh/ssh_config