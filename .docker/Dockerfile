ARG WORDPRESS_IMAGE=wordpress

FROM ${WORDPRESS_IMAGE}

# PHP extensions
# Example : php-intl
RUN apt-get update && apt-get install -y zlib1g-dev libicu-dev g++
RUN docker-php-ext-configure intl
RUN docker-php-ext-install intl
