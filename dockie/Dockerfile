# Dockie Development Environment

FROM ubuntu:14.10
MAINTAINER Rob Loach <robloach@gmail.com>


# Update

ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update && \
  apt-get upgrade -y


# Supervisor

RUN apt-get install -y supervisor && \
  mkdir -p /var/log/supervisor && \
  supervisord --version
ADD configs/supervisor/supervisor.conf /etc/supervisor/conf.d/supervisor.conf


# Version Control

RUN apt-get install -y git mercurial subversion && \
  git --version && \
  hg --version && \
  svn --version


# OpenSSH

ADD configs/openssh/supervisor.conf /etc/supervisor/conf.d/openssh.conf
ADD configs/openssh/ssh-setup.sh /ssh-setup.sh
ENV SSH_ROOT_PASSWORD root
RUN apt-get install -y openssh-server && \
  chmod +x /ssh-setup.sh && \
  mkdir -p /var/run/sshd && \
  chmod 755 /var/run/sshd && \
  (echo "root:root" | chpasswd) && \
  sed -i.bak -e 's/PermitRootLogin without-password/PermitRootLogin yes/g' \
    /etc/ssh/sshd_config


# Zsh

RUN apt-get install -y zsh && \
  git clone git://github.com/robbyrussell/oh-my-zsh.git /root/.oh-my-zsh && \
  chsh --shell $(which zsh) && \
  zsh --version
ADD configs/oh-my-zsh/zshrc /root/.zshrc


# PHP

RUN apt-get install -y php-pear php5-cgi php5-json php5-cli php5-curl curl \
  php5-mcrypt php5-xdebug mcrypt libmcrypt-dev php5-mysql php5-gd php5-sqlite \
  sqlite && \
  php5enmod mcrypt pdo pdo_sqlite sqlite3 && \
  php --version
ADD configs/php/php-cli.ini /etc/php5/cli/conf.d/dockie-dockie.ini


# Composer

ENV COMPOSER_HOME /root/.composer
ENV PATH $COMPOSER_HOME/vendor/bin:$PATH
RUN curl -sS https://getcomposer.org/installer | php -- --filename=composer \
  --install-dir=/usr/bin --version=1.0.0-alpha9
RUN composer --version


# PHPUnit

RUN composer global require phpunit/phpunit:4.* && \
  phpunit --version


# Drush

RUN composer global require drush/drush:~7.0.0@alpha && \
  drush --version


# HHVM

RUN wget -O - http://dl.hhvm.com/conf/hhvm.gpg.key | sudo apt-key add - && \
  echo 'deb http://dl.hhvm.com/ubuntu utopic main' \
    | tee /etc/apt/sources.list.d/hhvm.list && \
  apt-get update && \
  apt-get install --no-install-recommends -y hhvm libmemcached-dev libgmp-dev && \
  hhvm --version


# Python

RUN apt-get install -y python python3 pylint && \
python --version && \
python3 --version


# Node.js

RUN apt-get install -y build-essential && \
  curl -sL https://deb.nodesource.com/setup | sudo bash -
RUN apt-get install -y nodejs && \
  nodejs --version && \
  npm install -g coffee-script bower grunt-cli gulp component yo eslint


# io.js

ENV IOJS_VERSION 1.0.3
RUN apt-get install -y ca-certificates curl pkg-config python
RUN gpg --keyserver pool.sks-keyservers.net --recv-keys DD8F2338BAE7501E3DD5AC78C273792F7D83545D && \
  curl -SLO "https://iojs.org/dist/v$IOJS_VERSION/iojs-v$IOJS_VERSION-linux-x64.tar.gz" && \
  curl -SLO "https://iojs.org/dist/v$IOJS_VERSION/SHASUMS256.txt.asc" && \
  gpg --verify SHASUMS256.txt.asc && \
  grep " iojs-v$IOJS_VERSION-linux-x64.tar.gz\$" SHASUMS256.txt.asc | sha256sum -c - && \
  tar -xzf "iojs-v$IOJS_VERSION-linux-x64.tar.gz" -C /usr/local --strip-components=1 && \
  rm "iojs-v$IOJS_VERSION-linux-x64.tar.gz" SHASUMS256.txt.asc
RUN iojs --version


# Ruby

RUN apt-get install -y ruby ruby-dev ri && \
  ruby --version && \
  echo "Gem version:" && gem --version
RUN gem install rake bundler sass && \
  bundle --version && rake --version && sass --version && \
  gem install compass && \
  compass --version


# Go

RUN apt-get install -y golang && \
  go version


# Rust

RUN mkdir -p /tmp/rust && cd /tmp/rust && \
  wget https://static.rust-lang.org/dist/rust-1.0.0-alpha-x86_64-unknown-linux-gnu.tar.gz
RUN cd /tmp/rust && tar -zxvf * && cd r* && \
  ./install.sh && rm -rf /tmp/rust
RUN rustc --version && cargo --version


# Utility Packages

RUN apt-get install -y mysql-client


# Version Check

COPY configs/dockie/dockie-version /dockie-version
RUN chmod +x /dockie-version


# Clean up

RUN apt-get clean && apt-get autoremove
RUN /dockie-version


# Start

VOLUME /app
EXPOSE 22
CMD ["supervisord"]
