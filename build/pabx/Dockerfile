FROM centos:6.9

LABEL maintainer="DAD Engenharia <suporte@dad.eng.br>"

# Install REPOS
RUN rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-6.noarch.rpm \
    && rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm

# YUM Update
RUN yum update -y

# Install Utils
RUN yum install -y yum-utils openssl openssl-devel git systemtap-sdt-devel \
    gcc g++ gcc-c++ make subversion bison gmp kernel-devel sox make \
    logrotate qt-sqlite.x86_64 libdbi-dbd-sqlite.x86_64 wget kernel-devel-`uname -r` \
    newt-devel ncurses-devel libxml2-devel kernel-devel perl curl curl-devel \
    libtermcap-devel newt newt-devel ncurses ncurses-devel uuid-devel libuuid-devel \
    sqlite.x86_64 perl-DBD-SQLite.x86_64 sqlite-tcl.x86_64 sqlite-devel.x86_64 \
    apr-util-sqlite.x86_64 e2fsprogs-devel keyutils-libs-devel krb5-devel libogg \
    ncurses-devel audiofile-devel libogg-devel zlib-devel perl-DateManip sendmail-cf \
    libtool libtool-ltdl libtool-ltdl-devel unixODBC-devel libsepol-devel libtiff-devel \
    mysql mysql-server mysql-devel

# Install Modules PHP 7.2
RUN yum --enablerepo=remi,remi-php72 install -y php-fpm php-common php-opcache \
    php-cli php-pear php-pdo php-mysqlnd php-pgsql php-pecl-mongodb php-pecl-apcu \
    php-pecl-redis php-pecl-memcache php-pecl-memcached php-gd php-mbstring php-mcrypt \
    php-xml php-pecl-zip

# Composer PHP
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php --install-dir=/usr/bin --filename=composer \
    && php -r "unlink('composer-setup.php');" \
    && service php-fpm stop \
    && yum install -y supervisor 

# Download e Install das Dependências Asterisk
RUN mkdir /tmp/build 

# Install SRTP
RUN wget http://srtp.sourceforge.net/historical/srtp-1.4.2.tgz -O /tmp/build/srtp-1.4.2.tgz \
    && /bin/tar -zxvf /tmp/build/srtp-1.4.2.tgz -C /tmp/build/ \
    && cd /tmp/build/srtp/ \
    && autoconf \
    && ./configure CFLAGS=-fPIC --prefix=/usr \
    && make && make runtest && make install && ldconfig

# Install JansSon
RUN wget http://www.digip.org/jansson/releases/jansson-2.10.tar.gz -O /tmp/build/jansson-2.10.tar.gz \
    && /bin/tar -zvxf /tmp/build/jansson-2.10.tar.gz -C /tmp/build/ \
    && cd /tmp/build/jansson-2.10/ \
    && autoreconf --force --install \
    && ./configure --prefix=/usr/ --libdir=/usr/lib64 \
    && make && make check && make install && ldconfig

# Install Lame
RUN wget http://downloads.sourceforge.net/project/lame/lame/3.99/lame-3.99.5.tar.gz -O /tmp/build/lame-3.99.5.tar.gz \
    && /bin/tar -zvxf /tmp/build/lame-3.99.5.tar.gz -C /tmp/build/ \
    && cd /tmp/build/lame-3.99.5/ \
    && ./configure && make && make install

# Install Pacotes do Asterisk DHMI-LINUX
RUN wget http://downloads.asterisk.org/pub/telephony/dahdi-linux/releases/dahdi-linux-2.11.1.tar.gz -O /tmp/build/dahdi-linux-2.11.1.tar.gz \
    && /bin/tar -zvxf /tmp/build/dahdi-linux-2.11.1.tar.gz -C /usr/src/ \
    && cd /usr/src/dahdi-linux-* 
    # && make clean && make && make install

# Install Pacotes do Asterisk DHMI-TOOLS
RUN wget http://downloads.asterisk.org/pub/telephony/dahdi-tools/dahdi-tools-2.11.1.tar.gz -O /tmp/build/dahdi-tools-2.11.1.tar.gz \
    && /bin/tar -zvxf /tmp/build/dahdi-tools-2.11.1.tar.gz -C /usr/src/ \
    && cd /usr/src/dahdi-tools-* 
    # && ./configure && make && make install

# Install Pacotes do Asterisk LIBPRI
RUN wget http://downloads.asterisk.org/pub/telephony/libpri/releases/libpri-1.6.0.tar.gz -O /tmp/build/libpri-1.6.0.tar.gz \
    && /bin/tar -zvxf /tmp/build/libpri-1.6.0.tar.gz -C /usr/src/ \
    && cd /usr/src/libpri-* 
    # && ./configure && make clean && make && make install

# Install Pacotes do Asterisk 13
RUN wget https://downloads.asterisk.org/pub/telephony/asterisk/asterisk-13-current.tar.gz -O /tmp/build/asterisk-13-current.tar.gz \
    && /bin/tar -zvxf /tmp/build/asterisk-13-current.tar.gz -C /usr/src/

RUN yum install -y asterisk
RUN cd /usr/src/asterisk-13.* && ./configure --libdir=/usr/lib64 && make && make install && make config && make install-logrotate

# Install Driver KHOMP

# Alias
# alias liga='/usr/bin/php /opt/liga/pabx/api/artisan'

ADD supervisor.conf /etc/supervisor.conf

WORKDIR /opt
CMD ["supervisord", "-c", "/etc/supervisor.conf"]