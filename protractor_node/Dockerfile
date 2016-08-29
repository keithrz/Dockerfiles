FROM buildpack-deps:jessie

MAINTAINER maxarturo@gmail.com

ENV DEBIAN_FRONTEND noninteractive
ENV NPM_CONFIG_LOGLEVEL info
ENV NODE_VERSION 0.12.15
ENV PROTRACTOR_VERSION 2

# apt-key adv --keyserver keyserver.ubuntu.com --recv-keys EEA14886 && \
#   echo 'deb http://ppa.launchpad.net/webupd8team/java/ubuntu wily main ' >> /etc/apt/sources.list && \
#   echo 'deb-src http://ppa.launchpad.net/webupd8team/java/ubuntu wily main ' >> /etc/apt/sources.list && \

RUN apt-get update --fix-missing && \
  apt-get install -y \
    openjdk-7-jre \
    xvfb \
    libgconf-2-4 \
    libexif12 \
    chromium \
    netcat-traditional \ 
    supervisor \
    wget \
    xz-utils && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/*

# Install specified node version
# gpg keys listed at https://github.com/nodejs/node
RUN set -ex \
  && for key in \
    9554F04D7259F04124DE6B476D5A82AC7E37093B \
    94AE36675C464D64BAFA68DD7434390BDBE9B9C5 \
    0034A06D9D9B0064CE8ADF6BF1747F4AD2306D93 \
    FD3A5288F042B6850C66B31F09FE44734EB7990E \
    71DCFD284A79C3B38668286BC97EC7A07EDE3FC1 \
    DD8F2338BAE7501E3DD5AC78C273792F7D83545D \
    B9AE9905FFD7803F25714661B63B535A4C206CA9 \
    C4F0DFFF4E8C1A8236409D08E73BC641CC11F4C8 \
  ; do \
    gpg --keyserver ha.pool.sks-keyservers.net --recv-keys "$key"; \
  done


RUN curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.xz" \
  && curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc" \
  && gpg --batch --decrypt --output SHASUMS256.txt SHASUMS256.txt.asc \
  && grep " node-v$NODE_VERSION-linux-x64.tar.xz\$" SHASUMS256.txt | sha256sum -c - \
  && tar -xJf "node-v$NODE_VERSION-linux-x64.tar.xz" -C /usr/local --strip-components=1 \
  && rm "node-v$NODE_VERSION-linux-x64.tar.xz" SHASUMS256.txt.asc SHASUMS256.txt

RUN mkdir -p /project
WORKDIR /project

# Install Protractor
RUN npm install -g "protractor@$PROTRACTOR_VERSION"

# Install Selenium and Chrome driver
RUN webdriver-manager update

# Add a non-privileged user for running Protrator
RUN adduser --home /project --uid 1000 \
  --disabled-login --disabled-password --gecos node node

# Add main configuration file
ADD supervisord.conf /etc/supervisor/supervisor.conf

# Add service defintions for Xvfb, Selenium and Protractor runner
ADD supervisord/*.conf /etc/supervisor/conf.d/

# By default, tests in /data directory will be executed once and then the container
# will quit. When MANUAL envorinment variable is set when starting the container,
# tests will NOT be executed and Xvfb and Selenium will keep running.
ADD bin/run-protractor /usr/local/bin/run-protractor

# Container's entry point, executing supervisord in the foreground
CMD ["/usr/bin/supervisord", "-n", "-c", "/etc/supervisor/supervisor.conf"]

# Protractor test project needs to be mounted at /project
VOLUME ["/project"]

