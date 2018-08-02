FROM jiar/centos-systemctl:7


LABEL maintainer="Jiar <jiar.world@gmail.com>"
LABEL description="Docker container for phenix Project"


##### ARG ENV #####

ARG DB_CONFIG_DATABASE=phenix_db
ARG DB_CONFIG_USER=root
ARG DB_CONFIG_PASSWORD=password
ARG JWT_CONFIG_SECRET=phenix_secret
ARG JWT_CONFIG_EXPRISESIN=604800

ENV DB_CONFIG_DATABASE=$DB_CONFIG_DATABASE \
    DB_CONFIG_USER=$DB_CONFIG_USER \
    DB_CONFIG_PASSWORD=$DB_CONFIG_PASSWORD \
    JWT_CONFIG_SECRET=$JWT_CONFIG_SECRET \
    JWT_CONFIG_EXPRISESIN=$JWT_CONFIG_EXPRISESIN


##### mkdir -p #####

RUN mkdir -p /etc/supervisor \
    && mkdir -p /var/log/supervisor \
    && mkdir -p /usr/local/nvm \
    && mkdir -p /etc/nginx/conf.d \
    && mkdir -p /var/www/serve \
    && mkdir -p /var/www/html


##### Mariadb #####

RUN yum install -y mariadb mariadb-server

VOLUME ["/var/lib/mysql"]

COPY /etc/my.cnf /etc/my.cnf
RUN chmod 751 /etc/my.cnf

EXPOSE 3306


##### supervisor #####

RUN yum install -y python-setuptools \
    && easy_install supervisor

COPY /etc/supervisor /etc/supervisor


##### nvm、node #####

ENV NVM_DIR /usr/local/nvm
ENV NODE_VERSION 8.11.3

# Downloading nvm as script to '/usr/local/nvm'
RUN curl --silent -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash

RUN source $NVM_DIR/nvm.sh \
    && nvm install $NODE_VERSION \
    && nvm alias default $NODE_VERSION \
    && nvm use default

ENV NODE_PATH $NVM_DIR/v$NODE_VERSION/lib/node_modules
ENV PATH $NVM_DIR/versions/node/v$NODE_VERSION/bin:$PATH

# npm depended
RUN yum install -y make gcc*


##### nginx #####

RUN rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm \
    && yum install -y nginx

COPY /etc/nginx /etc/nginx


##### serve、front、admin #####

COPY /phenix/server /var/www/serve/phenix_server
COPY /phenix/front /var/www/html/phenix_front
COPY /phenix/admin /var/www/html/phenix_admin

WORKDIR /var/www/serve/phenix_server

RUN npm install

EXPOSE 8060

WORKDIR /var/www/html/phenix_front

RUN npm install \
    && npm run build \
    && rm -rf ./node_modules

EXPOSE 8061

WORKDIR /var/www/html/phenix_admin

RUN npm install \
    && npm run build \
    && rm -rf ./node_modules

EXPOSE 8062

WORKDIR /


CMD ["/usr/sbin/init"]