ARG php_version

FROM php:$php_version

# Install SSL
RUN apt update --assume-yes
RUN DEBIAN_FRONTEND=noninteractive apt install --assume-yes ssl-cert
RUN rm -r /var/lib/apt/lists/*

# enable ssl module and enable the default-ssl site
RUN a2enmod ssl
RUN a2ensite default-ssl

# Install PHP modules
RUN apt install --assume-yes libpng-dev

RUN apt install --assume-yes zlib1g-dev
RUN docker-php-ext-install  pdo pdo_mysql
RUN docker-php-ext-enable  pdo pdo_mysql
RUN docker-php-ext-install mysqli && docker-php-ext-enable mysqli
RUN docker-php-ext-install gd

# mhsendmail dependencies
RUN apt install --assume-yes --no-install-recommends \
    golang-go \
    git \
    nmap \
    iputils-ping \
    vim

# mhsendmail
RUN GOPATH=$HOME/go \
    PATH=$PATH:$GOROOT/bin:$GOPATH/bin \
    go get github.com/mailhog/mhsendmail
RUN ln  ~/go/bin/mhsendmail /usr/bin/mhsendmail

RUN apt install --quiet --assume-yes \
    git \
    gnupg \
    unzip \
    zip \
    webp

#Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install Apache Modules
RUN a2enmod rewrite

RUN openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt -subj "/C=FR/ST=France/L=Lille/O=Veranet/OU=IT Department/CN=localhost"

RUN mkdir /usr/tools
COPY activator.sh /usr/tools/activator.sh
COPY sites-availables/ /etc/apache2/sites-available/
COPY conf/ /etc/apache2/conf-available/
RUN sh /usr/tools/activator.sh

#RUN find /etc/apache2/sites-available/ -type f -and -not -name "*default*" -exec a2ensite {} \;
#RUN find /etc/apache2/conf-available/ -type f -and -not -name "*default*" -exec a2enconf {} \;
# RUN a2enconf app.conf
