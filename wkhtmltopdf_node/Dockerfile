FROM ubuntu:14.04
MAINTAINER Max Alcala <maxarturo@gmail.com>

RUN rm /bin/sh && ln -s /bin/bash /bin/sh

# Download and install wkhtmltopdf

RUN sed 's/main$/main universe/' -i /etc/apt/sources.list \ 
  && apt-get update && apt-get upgrade -y \ 
  && apt-get install -y build-essential xorg libssl-dev libxrender-dev wget python

RUN wget http://download.gna.org/wkhtmltopdf/0.12/0.12.3/wkhtmltox-0.12.3_linux-generic-amd64.tar.xz \
  && tar -xvf wkhtmltox-0.12.3_linux-generic-amd64.tar.xz \
  && mv wkhtmltox/bin/wkhtmltopdf /usr/local/bin/wkhtmltopdf \
  && chmod +x /usr/local/bin/wkhtmltopdf

ENV NVM_DIR=/root/.nvm

# install nvm and node versions
RUN wget -qO- https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash

# make a folder for the app
RUN mkdir -p /usr/src/app

WORKDIR /usr/src/app

# rely on project's .nvmrc file to install the appropriate version and run tests
CMD source ~/.nvm/nvm.sh && cd /usr/src/app && nvm i && npm i && npm test

